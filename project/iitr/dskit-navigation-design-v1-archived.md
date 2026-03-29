---
publish: true
---

# DS-Kit Navigation — Production Readiness Design
[[client-iitr]]

Rebuilding the DS-Kit Navigation system from scratch using PageIndex (vectorless, tree-based retrieval) to deliver production-ready accuracy on IITR's on-premise infrastructure.

---

## Problem Statement

IITR's DS-Kit product serves ~3,500 customers who regularly contact support with recurring questions (27-28/day per Stellmacher). A navigation system reduces this support load by answering common questions automatically.

A previous prototype (Typesense + chunked RAG) scored 17/29 on the test harness. The chunking approach proved fragile — correct answers existed in the data but vector similarity retrieved wrong chunks. That implementation is being retired.

This project rebuilds the navigation system using PageIndex — a vectorless, tree-based retrieval framework that eliminates chunking entirely. The LLM reasons through a document tree index to find relevant sections, producing traceable, chapter-referenced answers.

**Preconditions:**
- Design artifacts from prior attempt preserved: Pflichtenheft, test questions with source references (29 Q&A + Quelle column), Masterfragen CSV (22 entries), Anwenderleitfaden PDF, 56 DS-Kit templates
- PageIndex benchmarked on IITR-STAGING: Qwen 3.5 9B with think:false achieves ~0.76s per tree traversal call, ~8.5s for full 5-call + answer pipeline (RTX 4000 SFF Ada, 20 GB VRAM)
- Financial reset: prior work uninvoiced, Roman terminated, clean start
- Developer: Dave implements from this design doc, Marius orchestrates as JA

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Navigation system is production-ready: accurate answers with chapter-referenced navigation on the 29-question test set |
| **Success** | Test harness scores ≥26/29 (>90%) using PageIndex retrieval. Answers include chapter reference from tree traversal path. Stellmacher/Kraska validate answer quality via sample review. |
| **Done test** | "Can I write a meeting agenda with open design questions?" → If NO → design is complete |

**Test harness mechanism:** Sends each question through PageIndex tree traversal (Qwen 3.5, think:false) to retrieve relevant sections, then generates answer from retrieved context. An LLM judge evaluates semantic correctness against expected answers from the Quelle-mapped test set. PASS/FAIL per question with German-language reasoning.

---

## Infrastructure Baseline

IITR-STAGING hosts the target deployment. A server audit (2026-03-09) found 11 Docker stacks running ~70 containers — far more than previously documented. This includes orphaned experiments, crash-looping services, and three overlapping observability stacks. Cleanup is a prerequisite for deploying the new PageIndex stack.

**Retire (cleanup first):**

| Stack | Why | Compose File |
|-------|-----|-------------|
| IITR-RAG-V2 (Typesense, OpenWebUI :3006, Pipelines) | Replaced by PageIndex | `/home/shared/projects/IITR-RAG-V2/docker-compose.yml` |
| DS-Kit clone (all stopped) | Duplicate of RAG-V2 | `/opt/dskit-rag/docker-compose.yml` |
| Chunkr (~47 containers, crash-looping) | Dependencies stopped 5 months ago, burning CPU | `/home/shared/projects/IITR-RAG-V2/services/chunkr/compose.yaml` |
| RAG-Dev (OpenWebUI :3001, Ollama, Qdrant) | Dev environment, not needed | `/home/shared/projects/IITR-RAG-V1/docker-compose.dev.yml` |
| Langfuse-POC (orphaned, compose file deleted) | Experiment containers still running | Compose deleted — stop manually |
| SigNoz (APM, ClickHouse, Zookeeper) | Consolidating to Langfuse only | `/home/shared/projects/signoz/deploy/docker/docker-compose.yaml` |
| OpenLIT (LLM observability, ClickHouse) | Overlaps with Langfuse | `/home/shared/projects/openlit/docker-compose.yml` |
| Trieve (orphaned volumes only) | No containers, 7 dangling volumes | Volumes only — `docker volume rm` |

After container removal: `docker system prune` to reclaim ~690 GB (unused images, volumes, build cache). All old data (Typesense indexes, Qdrant vectors, Chunkr parsed docs) is disposable — PageIndex replaces all prior retrieval approaches. No external IITR systems depend on this staging server.

**Production stack (after cleanup):**

| Component | Details |
|-----------|---------|
| GPU | NVIDIA RTX 4000 SFF Ada, 20 GB VRAM |
| Ollama | v0.17.7 in Docker (`rag-staging-ollama`, port 11436) |
| Qwen 3.5 9B | Single model for all pipeline stages. Tree traversal: 0.76s/call (think:false, forced by Ollama structured output). Full pipeline: ~8.5s (5 traversal calls + answer generation). Answer generation uses think:true for +25% instruction-following quality ([source](https://qwenlm.github.io/blog/qwen3/)). |
| OpenWebUI | Port 3002 — IITR Keycloak SSO + OTEL configured (`rag-staging-openwebui`) |
| Langfuse | Port 3003 — LLM tracing (web, worker, ClickHouse, Postgres, Redis, MinIO) |
| MetaMCP | Port 12008 — MCP proxy/manager |

**Access:**
- SSH: `ssh marius@IITR-STAGING`
- Ollama API: `http://localhost:11436/api/generate`
- Production compose: `/home/shared/projects/IITR-RAG-V1/docker-compose.staging.yml`
- Langfuse compose: `/home/shared/projects/langfuse/docker-compose.yml`
- MetaMCP compose: `/home/shared/projects/metamcp/docker-compose.yml`

**Cleanup sequence:** Retire all stacks in the table above → `docker system prune` → verify production stack healthy → then proceed to Approach Phase 1 (Infrastructure Setup) for PageIndex deployment.

**New stack (to deploy):**
- PageIndex (open-source, MIT) — tree index generation + retrieval
- Qwen 3.5 9B via Ollama — tree traversal + answer generation + tree index generation (single model)
- OpenWebUI — chat interface (retained on port 3002, reconfigure backend from Flask proxy to PageIndex)
- Test harness — automated 29-question evaluation with LLM judge

**Validated (2026-03-09):**
- **PageIndex runs fully local** — 2 patches needed: (1) tiktoken fallback in `utils.py` (`cl100k_base` for Qwen model names), (2) `OPENAI_BASE_URL` env var for Ollama routing
- **Qwen 3.5 9B single model** — produces valid structured JSON with `think:false`. Tested against Qwen 3 30B-A3B: 9B wins because `think:false` is ignored on Qwen 3, consuming all tokens on thinking. 9B also produces richer titles and summaries. Split config: `think:false` for tree traversal (Ollama forces this for structured output — [#10929](https://github.com/ollama/ollama/issues/10929)), `think:true` for answer generation (IFEval: 0.92 vs 0.69 non-thinking — [Qwen3 blog](https://qwenlm.github.io/blog/qwen3/)).
- **All 29 test questions sourced** — Navigationssystem FAQ (19/29), Anwenderleitfaden PDF (3/29), DS-Kit Oberfläche chapters (7/29). Full mapping in [epic #959 comment](https://github.com/DaveX2001/deliverable-tracking/issues/959#issuecomment-4021675769).
- **Source data organized** — [Drive folder](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) with INPUT/ (knowledge base) and TEST_RUBRIK/ (evaluation rubric)

---

## Approach

Four phases, executed sequentially. Each phase produces a testable artifact before the next begins.

### 1. Infrastructure Setup

Deploy the PageIndex + Ollama + OpenWebUI stack on IITR-STAGING. PageIndex is not currently installed — this is a from-scratch deployment.

**How PageIndex works:** PageIndex transforms documents into hierarchical JSON tree indexes (like a machine-readable table of contents with `title`, `text`, `page_index`, and nested `nodes`). At query time, the LLM receives the tree structure + user query and returns a `{thinking, node_list}` JSON identifying relevant nodes. The selected nodes' text becomes the context for answer generation. No vector database, no chunking — the LLM reasons through the document structure.

- Clone PageIndex repo, apply tiktoken fallback patch for Qwen model names
- Configure Ollama endpoint (`http://localhost:11436/v1`) as PageIndex backend
- Set `OPENAI_BASE_URL` + dummy API key for PageIndex → Ollama routing
- Rewrite OpenWebUI pipeline filter for PageIndex (the existing `dskit_navigation_filter.py` is Typesense-based and non-functional — `DskitRagBase` import missing). New pipeline: load tree JSON → LLM tree search via Ollama → extract node text → generate answer with chapter references
- Tree indexes stored as JSON files on disk. Multi-document loading strategy (all trees at once vs doc-level routing) depends on actual tree sizes — resolve during implementation
- Build test harness: automated 29-question evaluation against expected answers from [test set](https://docs.google.com/spreadsheets/d/1gKLPVazIDCVQtpZaR509-NbiVZbjEnmkY44raMdbM2A/edit?gid=492982273)

### 2. Data Preparation

Feed all source documents to PageIndex. PDFs go in directly (PageIndex handles PDF → tree natively). Non-PDF formats need conversion first. Ingest everything — full documents, not cherry-picked by question. Text-only extraction, no visual elements. Note: PageIndex warns that markdown converted from PDF/HTML often loses heading hierarchy — use `--pdf_path` for PDFs, reserve `--md_path` for natively structured markdown.

**Data availability (as of 2026-03-09):**
- Anwenderleitfaden PDF: on staging at `/home/shared/projects/IITR-RAG-V2/data/` and on [Google Drive](https://drive.google.com/file/d/1LzSc_X3yAlv1N55CvrWRAHsqGA7m2ISJ/view)
- Masterfragen + Testfragen: on [Google Drive](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) as Google Sheets
- Templates: TBD — download from `core.iitr.de/tenant/3520/page/82177` (Keycloak auth required) or Marius provides
- Web chapters 1-12: extract from `core.iitr.de/tenant/3520/page/82173` via Chrome DevTools MCP (Keycloak auth required — credentials on [#798](https://github.com/DaveX2001/deliverable-tracking/issues/798)). One-time re-extraction as structured markdown — prior extraction (#798, Feb 2026) produced flat JSONL chunks unsuitable for PageIndex. See Web Extraction below. 7/29 test questions depend on this data.

| Source | Format | Conversion | PageIndex Input |
|--------|--------|------------|-----------------|
| Anwenderleitfaden | PDF | None | `--pdf_path` |
| Templates (PDF subset) | PDF | None | `--pdf_path` |
| Templates (Word subset) | .docx | Convert to PDF or extract to markdown | `--pdf_path` or `--md_path` |
| Templates (Excel subset) | .xlsx | Convert to structured markdown | `--md_path` |
| Masterfragen (22 Q&A) | CSV/Excel | Convert to structured Q&A markdown | `--md_path` |
| core.iitr.de chapters 1-12 | Web | Extract via Chrome DevTools MCP → one combined markdown (H1 per chapter) | `--md_path` |

#### Web Extraction (core.iitr.de chapters 1-12)

Extract the 12 DS-Kit portal chapters as a single structured markdown file using Chrome DevTools MCP (Claude Code browser automation). The portal organizes content across `kapitel-01` through `kapitel-12`, each a separate page behind Keycloak SSO. Content is German data protection guidance with inline template references ("Mitgeltende Vorlagen") — keep all text, strip only navigation chrome and visual elements.

**Output format:** One combined markdown file. Each chapter starts with an H1 header (`# Kapitel 1: ...`), subsections as H2/H3. Preserving the heading hierarchy is critical — PageIndex `md_to_tree()` uses `#` characters to determine tree node structure. Flat text without headers produces a degenerate tree with poor retrieval.

**Fallback:** If Keycloak authentication prevents Chrome DevTools automation, use [vercel-labs/agent-browser](https://github.com/vercel-labs/agent-browser) (CLI-based headless browser with auth state persistence).

**Scope:** One-time extraction. Re-run only if IITR updates portal content. Prior extraction (#798) validated the Chrome DevTools approach — this pass produces structured markdown instead of JSONL chunks.

### 3. Tree Index Generation

Run PageIndex on each prepared document to produce hierarchical tree indexes:

- `python run_pageindex.py --pdf_path <doc> --model qwen3.5:9b` (via Ollama)
- Also supports markdown: `--md_path` for pre-extracted content
- One-time operation per document — regenerate only when source content changes
- Tree indexes enable chapter-referenced answers naturally — the tree traversal path IS the chapter reference (no separate dual-answer prompting needed)

### 4. Evaluation & Iteration

Run the test harness against the 29-question set with Quelle-mapped expected answers. An existing test harness (`dskit_test_harness.py` on staging) provides reusable infrastructure: OpenWebUI API integration, LLM judge with German-language reasoning (Claude Sonnet via OpenRouter), and CORRECT/PARTIAL/INCORRECT scoring. Currently tests 14 questions — expand to the full 29-question set. The judge evaluates against expected key information per question. Iterate on:

- Retrieval prompts (tree traversal instructions)
- Answer generation prompts (format, language, detail level)
- `think:false` for tree traversal, `think:true` for answer generation (decided — see Infrastructure Baseline)

Target: ≥26/29 (>90%). Present sample answers to Stellmacher for qualitative review at bi-weekly meeting (Mar 17).

---

## Source

**Data Files:**
- [`KI Testfragen (1).xlsx`](https://mail.google.com/mail/u/0/#all/19bfebdff5dcbf33) — 29 test questions with source references (Stellmacher, 2026-01-27)
- [`Fragen Navigationssystem.xlsx`](https://mail.google.com/mail/u/0/#all/19bfebdff5dcbf33) — 22 Masterfragen (Stellmacher, 2026-01-27)
- [Test Set with Quelle](https://docs.google.com/spreadsheets/d/1gKLPVazIDCVQtpZaR509-NbiVZbjEnmkY44raMdbM2A/edit?gid=492982273) — 29 questions with source references (Google Sheet)
- Portal: `https://core.iitr.de/tenant/3520/page/82173` — DS-Kit web interface chapters 1-12

**PageIndex:**
- [GitHub repo](https://github.com/VectifyAI/PageIndex) — MIT, open-source, vectorless tree-based RAG
- [Docs](https://docs.pageindex.ai/) — framework reference, cookbooks, tutorials
- Benchmark: Qwen 3.5 4B/9B with think:false on IITR-STAGING (RTX 4000 SFF Ada, 20 GB)

**Reference Documents:**
- [Test Analysis](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-rag-test-analysis) — question-by-question verification (prior Typesense attempt)
- [IITR Materials Index](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/index)

**Transcripts:**
- [2026-01-15 Client Meeting](https://app.fireflies.ai/view/01KF0N6JDANZB4JGW9WEP4GNMC) — data gap discussion + dual-answer decision with Stellmacher/Kraska
- [2026-03-04 Contract Meeting](https://drive.google.com/file/d/1wS9Z8jjH-hkeaCW5aQZ9aTXWBqIQ24H9/view) — Stellmacher cost corridor ("von bis") requirements for Contract 2, Kraska contract approval, Roman financial reset

**Sessions:**
- Original design: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-IITR--IITR-NAVIGATION/46a000cb-3044-40d7-adaf-e30987553859.jsonl
- Zielkorridor extraction: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/a868fafa-95e4-413d-95c8-f50bd3ff3ed4.jsonl
- Pass 1 extraction (Approach): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/80abc9d7-5b68-42f4-8d65-c159fb0cb0e8.jsonl
- Pass 2 extraction (Current Deployment): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/ca51af04-8bef-46c9-b294-b7a2c161da01.jsonl
- Pass 4 extraction (Qwen Model): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/a084b417-c473-471e-8ff2-5c647ccc572a.jsonl
- Pass 5 extraction (Web Extraction): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/d009a445-ce7e-4d6b-a939-cf18d2024374.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

