---
publish: true
---

# CJ Re-indexing Pipeline — Source Recovery & New Ingestion
[[client-iitr]]

Design for rebuilding the Court Judgments ingestion pipeline from recovered source data, replacing the destroyed Chunkr-based pipeline with a lighter, purpose-built approach using shared infrastructure.

---

## Problem Statement

The CJ (Court Judgments) ingestion pipeline no longer exists. During the #1111 infrastructure cleanup, the chunking service (Chunkr), all processed data (2,567 raw.json files), and the processing script were permanently deleted. The serving layer — pipeline filter, prompts, OpenWebUI model — is functional (#1214) but serves empty search results because no data exists in Typesense.

This is a data pipeline problem, not a serving problem. The goal is to build a new path from recovered source files to a searchable Typesense collection, replacing Chunkr with a lighter, purpose-built approach.

**Preconditions:**
- Source data recovered: `Urteile-DSGVO.zip` (2,635 files) committed to `court-judgments/data/` on staging
- Serving layer operational: CJ hybrid filter wired into shared Pipelines container, prompts migrated to local files, OpenWebUI model configured
- Embedding infrastructure ready: BGE-M3 1024d via shared TEI (:8080)
- Search infrastructure ready: shared Typesense (:8109), hybrid search with alpha 0.7 (semantic-prioritized)
- Primary sources committed: email threads (Stellmacher/Rolnik correspondence), WhatsApp exports, work logs, ADRs — full reconstruction context available

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | CJ data is searchable on staging — Stellmacher can query DSGVO court judgments via OpenWebUI alongside Navigation |
| **Success** | All 2,635 source documents parsed, chunked, embedded (BGE-M3), and indexed in Typesense. CJ model returns relevant results with proper court citations (case number, court, date) and linkable URLs. No "Unnamed Document" fallbacks. |
| **Done test** | "Can I ask a DSGVO question in OpenWebUI's CJ model and get a cited answer with a working court reference link?" → If YES → pipeline is complete |

---

## Approach

### 1. Data Inventory

`Urteile-DSGVO.zip` contains 2,635 files — but the composition is not what the filename suggests. The vast majority is pre-extracted plain text, not HTML:

| Format | Count | % | Source | Parsing needed |
|--------|-------|---|--------|---------------|
| **TXT** (openJur) | 2,506 | 95% | openjur.de | None — already plain text |
| **HTML** (5 format families) | 106 | 4% | Federal portals, CURIA, state courts | Format-specific extraction |
| **PDF** | 6 | <1% | BGH, OLG originals | Docling |

**TXT files** are the main corpus. Each is a complete court ruling in plain text. Line 1 contains the citation string (e.g., "BGH, Urteil vom 14.02.2023 — 1 BvR 2683/16") — verified on staging (#1241). Filenames are base64-encoded openJur URLs with `.raw.txt` extension — decode filename to recover source URL. No openjur.de scraping needed for the main corpus.

**HTML files** come from 5 distinct source systems, each with different DOM structures:

| Source System | Count | Identifier | Courts |
|---------------|-------|-----------|--------|
| Bürgerservice Bayern/NRW | 34 | `<div class="rsprbox">` | State courts |
| CURIA (EU) | 21 | `curia.europa.eu` CSS link | ECJ, General Court |
| Infopark/NRWE | 14 | `<meta generator="Infopark CMS Fiona">` | NRW state courts |
| rechtsprechung-im-internet | 9 | `<base href="rechtsprechung-im-internet.de">` | Federal supreme courts |
| openJur HTML | 9 | `<meta name="author" content="openJur gGmbH">` | Various |
| Long tail (BAG, BGH press, etc.) | 19 | Various | Various |

**PDF files** (6) are original court documents — standard PDF extraction applies.

### 2. Source Parsing

Three parsers handle the three file types. All produce the same intermediate output: one text block per document with extracted metadata.

**TXT parser (2,506 files):** Read file content directly. Extract citation string from Line 1. Decode filename (base64 → openJur URL) for the source link. No HTML parsing, no conversion — the text is ready for chunking.

**HTML parser (106 files):** Use Docling `DocumentConverter` as the primary content extractor. If Docling returns 0 chunks (e.g., deeply nested or non-standard DOM), fall back to BeautifulSoup `get_text()` with boilerplate stripping. Metadata (court, case number, source URL) is always extracted via format-specific selectors — independent of the content extraction path:

| Source System | Content selector | Metadata extraction |
|---------------|-----------------|-------------------|
| Bürgerservice | `div.rdblock > div.absatz` | Panel heading: court, date, AZ |
| CURIA | `div#document_content` (nested HTML) | `span.outputEcli` for ECLI, body regex for case number |
| Infopark/NRWE | `div.feldinhalt` pairs | `div.feldbezeichnung` labels: Gericht, Datum, AZ |
| rechtsprechung-im-internet | `div.docLayoutText` | `table.documentHeader` cells: Gericht, AZ, ECLI |
| openJur HTML | `div.cnt#econtent` | `<title>` tag, `<meta keywords>`, canonical URL |

For the 19 long-tail files (BAG, BGH press releases, etc.): BeautifulSoup `get_text()` fallback applies. Metadata extraction is best-effort from `<title>` and `<meta>` tags.

**PDF parser (6 files):** Use Docling `DocumentConverter`. Extract text and headings. Metadata from filename patterns (e.g., `vi_zr_109-23.pdf` → case number `VI ZR 109/23`).

### 3. Section-Aware Chunking

German court rulings follow a predictable structure: Tenor (verdict), Gründe (reasoning), and optionally Verfahrensgang (procedural history), Tatbestand (facts), and Entscheidungsgründe (decision reasoning). The chunking strategy preserves this structure as metadata while keeping chunks at a consistent size for retrieval.

**Algorithm:**
1. Detect section headers in the text (regex: `^(Tenor|Gründe|Tatbestand|Entscheidungsgründe|Verfahrensgang|Leitsatz|Leitsätze):?\s*$`)
2. Split text into sections at detected headers
3. Within each section, apply sentence-boundary chunking with a ~500 token target (hard max: 800 tokens). Sentences are not split mid-sentence.
4. Each chunk carries metadata: `source_section` (e.g., "Gründe"), `chunk_index` within the section

**Why section-aware:** The hybrid search (alpha 0.7, semantic-heavy) benefits from chunks that carry structural context. A chunk from "Tenor" carries different weight than one from "Gründe." The `source_section` field enables faceted search and section-level filtering if needed later.

**Target density:** The old pipeline averaged ~8 chunks per document (20,256 chunks / 2,567 docs). Section-aware chunking at ~500 tokens should produce similar density — most rulings are 2,000–5,000 tokens with 2–3 sections.

**JSONL intermediate format** (matches Navigation pattern):
```json
{
  "id": "sha256(source_file:section:chunk_index)[:16]",
  "content": "chunk text",
  "title": "BGH, Urteil vom 14.02.2023 — 1 BvR 2683/16",
  "source_file": "base64-encoded-url.raw.txt",
  "source_type": "court_judgment",
  "source_section": "Gründe",
  "source_url": "https://openjur.de/u/123456.html",
  "court": "BGH",
  "case_number": "1 BvR 2683/16",
  "decision_date": "2023-02-14",
  "chunk_index": 3
}
```

### 4. Embedding & Indexing

Read JSONL chunks, embed via shared TEI, upsert to Typesense. This replaces `urteile_ingestion.py` — the old script read Chunkr's raw.json format and stored no title field.

**Embedding:** BGE-M3 via shared TEI at `http://rag-staging-tei:80/v1/embeddings` (1024 dimensions). Same endpoint Navigation uses for query-time embedding. CJ uses it for both index-time and query-time.

**Typesense schema** (updated — adds `title`, `source_section`, `source_url`, `court`, `case_number`, `decision_date`):

```python
{
    "name": "urteile_bge_m3_YYYY-MM-DD",
    "fields": [
        {"name": "id", "type": "string"},
        {"name": "content", "type": "string"},
        {"name": "title", "type": "string"},
        {"name": "embedding", "type": "float[]", "num_dim": 1024},
        {"name": "source_file", "type": "string", "facet": True},
        {"name": "source_type", "type": "string", "facet": True},
        {"name": "source_section", "type": "string", "facet": True},
        {"name": "source_url", "type": "string"},
        {"name": "court", "type": "string", "facet": True},
        {"name": "case_number", "type": "string"},
        {"name": "decision_date", "type": "string"},
        {"name": "chunk_index", "type": "int32"},
    ]
}
```

**Collection naming:** `urteile_bge_m3_YYYY-MM-DD` per ADR-004. Dated collections allow A/B comparison between ingestion runs. `urteile_search.py` reads the active collection name from `CJ_COLLECTION_NAME` env var (replacing the current hardcoded value).

**Idempotency:** Deterministic chunk IDs (sha256 hash of source_file + section + chunk_index). Typesense upsert mode — re-running the indexer updates existing documents instead of duplicating.

### 5. Integration

The new pipeline connects to the existing serving layer without changes to the pipeline filter or OpenWebUI configuration.

**Pipeline flow:**
```
Urteile-DSGVO.zip
  → unzip to court-judgments/data/extracted/
  → chunk_urteile.py (parse + chunk → JSONL)
  → urteile_ingestion.py (embed + index → Typesense)
  → urteile_search.py reads CJ_COLLECTION_NAME env var
  → urteile_hybrid_rag_filter.py serves via OpenWebUI
```

**Script updates required:**

| Script | Change | Why |
|--------|--------|-----|
| `chunk_urteile.py` | **New** | Replaces Chunkr — parses TXT/HTML/PDF, chunks, produces JSONL |
| `urteile_ingestion.py` | **Rewrite** | Reads JSONL (not raw.json), embeds via TEI, stores title + metadata fields |
| `urteile_search.py` | **Fix** | Collection name from `CJ_COLLECTION_NAME` env var instead of hardcoded |
| `urteile_hybrid_rag_filter.py` | **Update** | Use `title` field from Typesense (not "Unnamed Document" fallback) |

All scripts live in `court-judgments/services/rag/`. One-time batch execution — not a continuous pipeline. Run on IITR-STAGING where TEI and Typesense are accessible.

**Implementation results (2026-03-23):** Pipeline executed on staging. 170,382 chunks indexed to `urteile_bge_m3_2026-03-23`. All 6 ACs verified ([#1241](https://github.com/DaveX2001/deliverable-tracking/issues/1241) closed). CJ model returns cited court judgment answers on staging (CURIA C-46-23, LG Aschaffenburg 15 O 46/20, LG Münster 11 O 530/20). Source URLs (`source_url`) populated in Typesense — surfacing in LLM response addressed by [#1252](https://github.com/DaveX2001/deliverable-tracking/issues/1252).

---

## Source

**Data:**
- [Urteile-DSGVO.zip](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/court-judgments/data/Urteile-DSGVO.zip) — 2,635 source files (40MB)
- [Client eval CSVs](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging/court-judgments/client_evals) — Stellmacher's test evaluations

**Code (existing):**
- [urteile_ingestion.py](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/court-judgments/services/rag/urteile_ingestion.py)
- [urteile_search.py](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/court-judgments/services/rag/urteile_search.py)
- [urteile_hybrid_rag_filter.py](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/court-judgments/pipelines/urteile_hybrid_rag_filter.py)
- [ADRs](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging/court-judgments/docs/adr) — 7 architecture decisions
- [Type-3 citation success](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/court-judgments/docs/critical-path/1211020870915093-type3-citation-success.md)

**Primary sources (reconstruction context):**
- [Test-Bericht Datentopf Urteile.pdf](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/court-judgments/data/sources/Gmail%20-%20Test-Bericht%20Datentopf%20Urteile.pdf) — 163 pages, full eval history
- [WhatsApp Chat - Roman Rolnik.zip](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/court-judgments/data/sources/WhatsApp%20Chat%20-%20Roman%20Rolnik.zip) — 6,263 msgs, technical decisions

**Design references:**
- [DS-Kit Navigation Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-design) — Navigation system design (parallel project)

**Issues:**
- [#1234](https://github.com/DaveX2001/deliverable-tracking/issues/1234) — this design doc's tracking issue (closed)
- [#1241](https://github.com/DaveX2001/deliverable-tracking/issues/1241) — CJ Indexing + Serving Integration (closed — witness passed 6/6 ACs)
- [#1252](https://github.com/DaveX2001/deliverable-tracking/issues/1252) — CJ Prompt Restoration + Langfuse Tracing (next step)
- [#1214](https://github.com/DaveX2001/deliverable-tracking/issues/1214) — CJ Pipeline Filter + Housekeeping (serving layer)
- [#959](https://github.com/DaveX2001/deliverable-tracking/issues/959) — parent epic

**Sessions:**
- `ffc4b751-d331-48af-9caf-2811a5e2c5ae` — original extraction pass (design doc produced)
- `655f06bf-8060-449b-b7bd-e7bd7e01023f` — this update pass (Docling override, TXT verification, implementation results)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

