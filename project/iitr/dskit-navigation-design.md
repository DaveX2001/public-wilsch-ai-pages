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

IITR-STAGING hosts the target deployment. Old DS-Kit containers (Typesense, OpenWebUI, TEI, Pipelines) are being retired.

**Available now:**

| Component | Details |
|-----------|---------|
| GPU | NVIDIA RTX 4000 SFF Ada, 20 GB VRAM |
| Ollama | v0.17.7 in Docker (`rag-staging-ollama`, port 11436) |
| Qwen 3.5 9B | Pulled, benchmarked — 0.76s/call with think:false |
| Qwen 3.5 4B | Pulled, benchmarked — 0.67s/call with think:false |

**Access:**
- SSH: `ssh marius@IITR-STAGING`
- Ollama API: `http://localhost:11436/api/generate`
- Docker compose: `/home/shared/projects/IITR-RAG-V1/docker-compose.yml`

**New stack (to deploy):**
- PageIndex (open-source, MIT) — tree index generation + retrieval
- Qwen 3.5 via Ollama — tree traversal + answer generation
- OpenWebUI — chat interface (retained from previous deployment)
- Test harness — automated 29-question evaluation

---

## Approach

Four phases, executed sequentially. Each phase produces a testable artifact before the next begins.

### 1. Infrastructure Setup

Deploy the PageIndex + Ollama + OpenWebUI stack on IITR-STAGING:

- Clone PageIndex repo, apply tiktoken fallback patch for Qwen model names
- Configure Ollama endpoint (`http://localhost:11436/v1`) as PageIndex backend
- Set `OPENAI_BASE_URL` + dummy API key for PageIndex → Ollama routing
- Deploy OpenWebUI with PageIndex as retrieval backend (pipeline filter)
- Build test harness: automated 29-question evaluation against expected answers from [test set](https://docs.google.com/spreadsheets/d/1gKLPVazIDCVQtpZaR509-NbiVZbjEnmkY44raMdbM2A/edit?gid=492982273)

### 2. Data Preparation

Feed all source documents to PageIndex. PDFs go in directly (PageIndex handles PDF → tree natively). Non-PDF formats need conversion first. Ingest everything — full documents, not cherry-picked by question. Text-only extraction, no visual elements.

| Source | Format | Conversion | PageIndex Input |
|--------|--------|------------|-----------------|
| Anwenderleitfaden | PDF | None | `--pdf_path` |
| Templates (PDF subset) | PDF | None | `--pdf_path` |
| Templates (Word subset) | .docx | Convert to PDF or extract to markdown | `--pdf_path` or `--md_path` |
| Templates (Excel subset) | .xlsx | Convert to structured markdown | `--md_path` |
| Masterfragen (22 Q&A) | CSV/Excel | Convert to structured Q&A markdown | `--md_path` |
| core.iitr.de chapters 1-12 | Web | Scrape → markdown per chapter | `--md_path` |

### 3. Tree Index Generation

Run PageIndex on each prepared document to produce hierarchical tree indexes:

- `python run_pageindex.py --pdf_path <doc> --model qwen3.5:9b` (via Ollama)
- Also supports markdown: `--md_path` for pre-extracted content
- One-time operation per document — regenerate only when source content changes
- Tree indexes enable chapter-referenced answers naturally — the tree traversal path IS the chapter reference (no separate dual-answer prompting needed)

### 4. Evaluation & Iteration

Run the test harness against the 29-question set with Quelle-mapped expected answers. Iterate on:

- Retrieval prompts (tree traversal instructions)
- Answer generation prompts (format, language, detail level)
- Model selection (Qwen 3.5 4B vs 9B — 4B faster, 9B may be more accurate on edge cases)
- `think:false` confirmed for tree traversal; evaluate whether answer generation benefits from thinking

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
- [2026-03-04 Contract Meeting](https://drive.google.com/file/d/1wS9Z8jjH-hkeaCW5aQZ9aTXWBqIQ24H9/view) — Stellmacher "von bis" requirements, Kraska contract approval, Roman financial reset

**Sessions:**
- Original design: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-IITR--IITR-NAVIGATION/46a000cb-3044-40d7-adaf-e30987553859.jsonl
- Zielkorridor extraction: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/a868fafa-95e4-413d-95c8-f50bd3ff3ed4.jsonl
