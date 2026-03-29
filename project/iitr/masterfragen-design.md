---
publish: true
---

# DS-Kit Masterfragen — Data-Driven Design

[[client-iitr]]

Redesigning the Masterfragen (MF) GDPR Q&A system based on the actual production dataset (317 Q&A pairs recovered from IITR-PROD), migrating from a legacy Flask proxy + Qdrant architecture to the shared pipeline filter + Typesense infrastructure — completing the three-project serving architecture on IITR-STAGING.

---

## Problem Statement

IITR's DS-Kit serves ~3,500 customers with a "Masterfragen" feature: 317 GDPR Q&A pairs covering data protection questions from cookie banners and AV contracts to Privacy by Design concepts and email marketing legality. The previous design doc referenced 22 entries from a single Stellmacher Excel file — the actual production dataset, recovered from IITR-PROD in March 2026, contains 317 entries across 6 CSV snapshots (Jan–Aug 2025) with sparse Frage-IDs ranging 1–809.

MF is the only IITR project still running on legacy architecture. Its Flask proxy (`simple_app.py`) intercepts OpenWebUI requests, runs dense vector search against Qdrant, and forwards to Ollama. The ingestion pipeline stores 3 embedding models (multilingual-e5-large dense, BM42 sparse, ColBERT late-interaction) but search uses only dense — two-thirds of compute is wasted. Navigation and Court Judgments already use pipeline filters with Typesense + BGE-M3. MF is the outlier.

The 22-entry subset was temporarily ingested into the Navigation collection during #1192. The full 317-entry dataset has never been served through the new architecture.

**This is NOT:**

| Concern | Handled by |
|---------|-----------|
| Navigation system (chapter references) | DS-Kit Navigation design doc |
| Court Judgments (DSGVO rulings) | CJ Re-indexing Pipeline design doc |
| PII Pseudonymization | #1100 (OpenWebUI-level, not pipeline) |
| Ingestion into Navigation collection | #1192 (completed, temporary) |

**Preconditions:**

- Three-project Typesense architecture established (Navigation + CJ operational)
- Shared infrastructure running: Ollama (Qwen 3.5 9B), TEI (BGE-M3 1024d), Typesense, Langfuse — all on IITR-STAGING
- Mono-repo consolidated: `iitr-platform/masterfragen/` contains legacy code + recovered production data
- Source data recovered: 317 Masterfragen across 6 CSVs + Qdrant dump (317 points) committed to `masterfragen/data/` on staging
- VPN split tunnel required for staging access — data now in git, reducing VPN dependency for design work

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Masterfragen operates as a standalone product on staging: queryable via its own OpenWebUI model, backed by a dedicated Typesense collection (`dskit_masterfragen`, 317 documents), with Langfuse observability via shared project and `masterfragen` trace name — completing the three-project serving architecture. |
| **Success** | Two-tier LLM judge test: iteration subset (~30 representative questions, ~4 min) for rapid prompt tuning, full 317/317 gate test (~40 min) for release validation. Two dimensions: Content Accuracy (semantic match against Masterantwort) and Retrieval Accuracy (correct Q&A pair in top results). Langfuse traces visible under `masterfragen` trace name in shared IITR Langfuse project. |
| **Done test** | Stellmacher selects the "Masterfragen" model in OpenWebUI, asks any of the 317 Masterfragen, receives the matching Masterantwort — without developer involvement. Full 317/317 gate test passes. |

---

## Approach

Five parts, ordered so each builds on the previous. Part 1 establishes the data shape that drives all downstream decisions. Parts 2-3 build the pipeline and serving layer. Part 4 defines the test strategy. Part 5 handles legacy cleanup.

### 1. Data Inventory

MF's production dataset was recovered from IITR-PROD in March 2026 and committed to `masterfragen/data/` on the staging branch. The canonical source is the latest CSV: `OnlyQuestionAnswer-2025-07-23.csv`.

| Property | Value |
|----------|-------|
| **Canonical source** | `OnlyQuestionAnswer-2025-07-23.csv` |
| **Entries** | 317 Q&A pairs |
| **Frage-ID range** | 1–809 (sparse — intentional gaps, ID 2 never existed) |
| **Language** | German |
| **Format** | CSV: `Frage-ID`, `Masterfrage`, `Masterantwort` (+ trailing empty columns, artifact) |
| **Content type** | GDPR support Q&A — direct answers, chapter references, support redirects, external links, procedural guidance |
| **Qdrant dump** | `mf-qdrant-dump.json` — 317 points, matches CSV exactly. No vectors stored (audit artifact only) |

#### Data Evolution

6 CSV snapshots show organic growth over 6 months:

| Snapshot | Entries | Frage-ID Max | Delta |
|----------|---------|-------------|-------|
| `OnlyQuestionAnswer.csv` (undated) | 303 | 795 | — |
| `2025-02-01` | 303 | 795 | +0 |
| `2025-03-10` | 308 | 800 | +5 |
| `2025-05-15` | 309 | 801 | +1 (one entry has empty Masterantwort) |
| `2025-07-07` | 316 | 808 | +7 |
| `2025-07-23` (canonical) | 317 | 809 | +1 |

Growth phase appears complete — no new snapshots since July 2025. Design for idempotent re-ingest if future snapshots arrive.

#### Answer Length Distribution

| Category | Count | % |
|----------|-------|---|
| ≤30 words | 159 | 50% |
| 31–80 words | 138 | 44% |
| >80 words | 20 | 6% |

Average: 37 words. Median: 30 words. Max: 156 words (Privacy by Design, Frage-ID 127). The dataset is naturally concise — 94% of answers fit within 80 words.

### 2. Ingestion Pipeline

Convert 317 Q&A pairs from CSV to a searchable Typesense collection. No chunking needed — each Q&A pair is one document. The Frage-ID serves as natural document ID.

**Pipeline:**
```
OnlyQuestionAnswer-2025-07-23.csv
  → masterfragen/scripts/convert_to_jsonl.py (CSV → JSONL)
  → masterfragen/scripts/ingest.py (embed via TEI + upsert to Typesense)
  → dskit_masterfragen collection (317 documents)
```

**JSONL intermediate format** (matches Navigation/CJ pattern):
```json
{
  "id": "mf-001",
  "content": "Frage: Wo finde ich die Texte für die Webseitendatenschutzerklärung?\n\nAntwort: Texte für die Webseiten-Datenschutzerklärung finden Sie in Kapitel 8 des Datenschutz-Kit.",
  "title": "Webseitendatenschutzerklärung",
  "source_type": "masterfrage",
  "question_id": 1
}
```

**Typesense schema:**
```json
{
  "name": "dskit_masterfragen",
  "fields": [
    {"name": "id", "type": "string"},
    {"name": "content", "type": "string"},
    {"name": "title", "type": "string"},
    {"name": "embedding", "type": "float[]", "num_dim": 1024},
    {"name": "source_type", "type": "string", "facet": true},
    {"name": "question_id", "type": "int32"}
  ]
}
```

**Embedding:** BGE-M3 via shared TEI at `rag-staging-tei:80` (1024 dimensions). Same endpoint used by Navigation and CJ.

**Idempotency:** IDs derived from Frage-ID (`mf-{frage_id}`, e.g. `mf-001`, `mf-003`, `mf-809`). Typesense upsert mode — re-running updates existing documents. Sparse IDs preserved (no renumbering).

**Data in git:** CSV committed to `masterfragen/data/`. The commit IS the audit trail. GHA trigger on `masterfragen/data/**` changes runs ingestion automatically (per Navigation design doc deployment contract).

### 3. Serving Layer

MF follows the standard serving pattern: pipeline filter + system prompt + OpenWebUI model. One filter per project, one prompt per project, one model per project.

#### Pipeline Filter

`masterfragen/pipelines/masterfragen_hybrid_rag_filter.py` — same retrieval logic as Navigation and CJ, different collection and prompt.

| Component | Value |
|-----------|-------|
| **Collection** | `dskit_masterfragen` |
| **Search** | Typesense hybrid (BM25 + BGE-M3 vector, alpha configurable) |
| **Embedding** | TEI at `rag-staging-tei:80` (query-time) |
| **LLM** | Qwen 3.5 9B via Ollama (shared, `think:false`) |
| **Prompt** | `masterfragen/prompts/dskit_masterfragen.md` (loaded via `__file__`-relative path) |

**Langfuse integration:** Use `@observe()` decorator from Langfuse SDK. Trace name: `masterfragen`. Shared Langfuse project with Navigation (`navigation`) and CJ (`urteile`) — NOT separate projects per pipeline. #1252 proved that separate Langfuse projects cause upgrade/configuration issues on the server. Traces are differentiated by trace name, not by project.

#### System Prompt

GDPR Q&A specialist prompt. Derived from the legacy prompt (`masterfragen/app/util/prompt.py`) with adjustments for the new pipeline:

- **Role:** DSGVO/Datenschutz specialist
- **Word limit:** Soft ceiling — typically 50-80 words for concise support answers, up to ~150 words for complex topics (Privacy by Design, email marketing legality). 94% of Masterantworten fit within 80 words naturally; the 6% that exceed need space to be complete.
- **Topic boundary:** Hard — refuse out-of-scope questions with template: "Diese Frage liegt außerhalb meines Fachgebiets..."
- **Language:** German only
- **No navigation hints** — unlike Navigation, MF answers don't reference DS-Kit chapters as navigation targets (some answers mention chapters as content sources, but this is answer content, not navigation UI)
- **Placeholders:** `{frage}` and `{context}` (Python `.format()` style, matching CJ pattern)
- **Legacy note:** Existing prompt in `app/util/prompt.py` contains a typo ("DSVGO" instead of "DSGVO" in role description) — fix in new prompt.

#### OpenWebUI Model

| Property | Value |
|----------|-------|
| **Name** | Masterfragen |
| **Filter** | `masterfragen_hybrid_rag_filter` |
| **Visibility** | All users (Stellmacher, Kraska, internal) |
| **Description** | DSGVO Masterfragen — häufig gestellte Datenschutzfragen |

#### Infrastructure Mount

The `infrastructure/docker-compose.yml` Pipelines service currently mounts Navigation and CJ pipeline filters but NOT MF. Add the MF pipeline mount:

```yaml
volumes:
  - ../navigation/pipelines:/app/pipelines/navigation
  - ../court-judgments/pipelines:/app/pipelines/court-judgments
  - ../masterfragen/pipelines:/app/pipelines/masterfragen  # ADD THIS
```

### 4. Test Rubric

Automated evaluation against the 317 Masterfragen. MF has no separate test set — the Q&A pairs are both the knowledge base and the ground truth. Same test harness pattern as Navigation: LLM judge, CSV output, Sonnet 4.6 as evaluator.

#### Two-Tier Testing

| Tier | Questions | Time | Purpose |
|------|-----------|------|---------|
| **Iteration** | ~30 representative subset | ~4 min | Rapid feedback for prompt tuning |
| **Gate** | Full 317 | ~40 min | Release validation |

The iteration subset uses random sampling (~30 questions) to catch regressions across the distribution. The gate test runs all 317 — no sampling, no shortcuts.

**Scoring:**

| Dimension | Evaluator | Scoring |
|-----------|-----------|---------|
| Content Accuracy | AI (Sonnet 4.6) | PASS/FAIL — semantic match against the Masterantwort for that Frage-ID |
| Retrieval Accuracy | AI (Sonnet 4.6) | PASS/FAIL — correct Masterfrage appears in top-5 retrieved results |

**Test set = knowledge base.** The 317 Q&A pairs serve dual purpose: they ARE the ingested data AND the ground truth for evaluation. Each question tests whether the system can find and reproduce its own pre-verified answer.

**Target:** ~90–95% on the gate test (~285–301 of 317). 100% is unrealistic — minor AI inconsistencies are expected. Any miss below 90% indicates retrieval or generation failure requiring diagnosis. The diagnostic chain follows Navigation's pattern: Data Source → Retrieval → Generation.

**Cost per run:** ~$1 via OpenRouter (Sonnet 4.6) — negligible. Time (~40 min) is the real cost, which is why the iteration subset exists.

**Output artifact:** CSV report with columns per dimension, uploaded to Google Drive with date and time in filename (e.g., `dskit-mf-test-2026-04-01-14-30.csv`). Two modes: internal (PASS/FAIL per dimension) and client-facing (full answers). Linked for Dev Lead witness review.

**Ownership** (per Dev Lead Witness & Review System): JA defines rubric dimensions and expected answers → SA approves rubric definition → Developer creates the LLM judge harness → Dev Lead approves test results via witness review.

### 5. Legacy Cleanup

Retire the old Qdrant-based MF architecture. Codebase investigation reveals extensive legacy code in `masterfragen/` that predates the pipeline filter pattern.

| Action | Target | Rationale |
|--------|--------|-----------|
| **Remove** | `app/` (Flask proxy, Qdrant ingestion, eval, utils) | Replaced by pipeline filter |
| **Remove** | `docker-compose*.yml` (4 files: base, dev, staging, prod) | Replaced by infrastructure compose |
| **Remove** | `docker/` (Dockerfile.prod, daily-restart cron) | No longer needed |
| **Remove** | `READMEs/` (10 stale documentation files) | Superseded by this design doc |
| **Remove** | `Makefile` | Legacy orchestration |
| **Archive** | `docs/` (ADRs, work logs, client handoff) | Historical value — move to repo archive |
| **Evaluate** | `scripts/monitor_ollama_health.sh` | May be useful for GPU monitoring (#1249) |
| **Retain** | `package/` (pseudonymization) | Separate concern (#1100) |
| **Retain** | `data/` (6 CSVs + Qdrant dump) | Source data + audit trail |
| **Create** | `pipelines/` | New pipeline filter |
| **Create** | `prompts/` | New system prompt |
| **Create** | `scripts/` (new) | Ingestion scripts (convert + ingest) |

**Cleanup order:** Deploy new pipeline first (Parts 1-4), verify it works, THEN remove legacy. Never remove before the replacement is serving.

**Navigation collection cleanup:** The 22 MF entries injected into `dskit_navigation` during #1192 stay — SA confirmed they should remain. Flag this with the client at some point so they're aware MF content appears in Navigation results. This is a communication task, not a technical one.

---

## Source

**Data:**
- [Recovered PROD data](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging/masterfragen/data) — 6 CSVs (Jan–Aug 2025) + Qdrant dump (317 points), committed to staging branch
- [Source Data Inventory (Drive)](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) — INPUT/ + TEST_RUBRIK/

**Design References:**
- [DS-Kit Navigation Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-design) — Navigation system design (structural reference, infrastructure pattern)
- [CJ Re-indexing Pipeline Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/cj-reindexing-pipeline-design) — CJ pipeline design (pattern reference)

**Issues:**
- [#1264](https://github.com/DaveX2001/deliverable-tracking/issues/1264) — this design doc's tracking issue
- [#1192](https://github.com/DaveX2001/deliverable-tracking/issues/1192) — MF data ingestion into Navigation (completed)
- [#1218](https://github.com/DaveX2001/deliverable-tracking/issues/1218) — MF Pipeline Filter (closed, awaiting this design doc)
- [#1252](https://github.com/DaveX2001/deliverable-tracking/issues/1252) — CJ Prompt + Langfuse (shared project lesson)
- [#1100](https://github.com/DaveX2001/deliverable-tracking/issues/1100) — PII Pseudonymizer (out of scope)
- [#1249](https://github.com/DaveX2001/deliverable-tracking/issues/1249) — GPU healthcheck (monitor_ollama_health.sh evaluation)
- [#959](https://github.com/DaveX2001/deliverable-tracking/issues/959) — parent epic

**Transcripts:**
- [2026-03-22 Grooming](https://app.fireflies.ai/view/01KMB243RF95QP9ZFG9VGBWFX9) — MF Typesense consolidation discussed

**Code:**
- [masterfragen/](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging/masterfragen) — full MF directory (legacy code + recovered data)
- [infrastructure/docker-compose.yml](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/staging/infrastructure) — shared infra (needs MF pipeline mount)
- [navigation/pipelines/typesense_rag_filter.py](https://github.com/WILSCH-AI-SERVICES/iitr-platform/blob/staging/navigation/pipelines/typesense_rag_filter.py) — reference implementation for MF filter

**Sessions:**
- `60dc7801-db94-4eb2-b39e-b03345e96175` — first extraction pass (22-entry design doc)
- `fd7b2b76-6f08-4ed1-a5f0-91a64f1b6be1` — this extraction pass (data-driven rewrite, 317 entries)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under CC BY-NC-ND 4.0.

WILSCH AI SERVICES OÜ | Harju maakond, Tallinn, Sepapaja tn 6, 15551
