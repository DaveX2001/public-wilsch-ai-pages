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

**This is NOT:**

IITR operates three separate RAG projects, each with distinct data and purpose. This design doc covers Navigation only.

| Project | Purpose | Data Input |
|---------|---------|------------|
| **DS-Kit Navigation** (this doc) | Support navigation — answer recurring customer questions with chapter references | Anwenderleitfaden PDF + 56 templates + core.iitr.de ch.1-12 + 29 test Q&A |
| [Court Judgments](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Court-Judgments) | Court rulings RAG (Urteile) | German legal documents |
| [Masterfragen](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Masterfragen) | GDPR Q&A (Masterfragen) | 22 Masterfragen CSV |

Court Judgments and Masterfragen are not replaced by PageIndex — different projects, different data, different retrieval.

**Preconditions:**
- Design artifacts from prior attempt preserved: Pflichtenheft, test questions with source references (29 Q&A + Quelle column), Masterfragen CSV (22 entries), Anwenderleitfaden PDF, 56 DS-Kit templates
- PageIndex benchmarked on IITR-STAGING: Qwen 3.5 9B viable on available hardware (RTX 4000 SFF Ada, 20 GB VRAM)
- Financial reset: prior work uninvoiced, clean start with direct client relationship (Stellmacher as technical contact, Kraska as business contact)
- IITR-STAGING requires cleanup before new deployment — architecture principle: one environment per project, separate compose stacks. ~70 containers running, only ~14 should remain (Navigation stack, Langfuse, OpenWebUI, MetaMCP)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Navigation system is production-ready: accurate answers with chapter-referenced navigation on the 29-question test set |
| **Success** | Test harness scores ≥26/29 (>90%) across 4 evaluation dimensions (see Test Rubric). Answers include chapter reference from tree traversal path. Stellmacher/Kraska validate answer quality via sample review at bi-weekly meetings. |
| **Done test** | Stellmacher sends a question to the navigation system, receives a chapter-referenced answer, and confirms it is correct — without developer involvement. |

---

## Approach

Four parts, ordered so each builds on the previous. Each part produces a testable artifact.

### Part 1: Stack

Deploy the retrieval and serving infrastructure on IITR-STAGING. This is a from-scratch deployment — PageIndex is not currently installed.

| Component | Role | Why |
|-----------|------|-----|
| **PageIndex** (MIT, open-source) | Tree index generation + retrieval | Vectorless, tree-based retrieval eliminates chunking. The LLM reasons through a document tree structure to find relevant sections — no embeddings, no vector DB. Produces traceable chapter references as a natural byproduct of tree traversal. |
| **Qwen 3.5 9B** via Ollama | Single model for all pipeline stages | Tree traversal (think:false, forced by Ollama structured output), answer generation (think:true, +25% instruction-following). Benchmarked on IITR hardware: 0.76s per traversal call, ~8.5s full pipeline. |
| **OpenWebUI** | Chat frontend | Already deployed with IITR Keycloak SSO integration. Familiar to client (Stellmacher has used it). Pipeline filter system enables RAG backend integration. Reconfigure from retired Typesense backend to PageIndex. |
| **RTX 4000 SFF Ada** (20 GB VRAM) | GPU inference | Available on IITR-STAGING. Sufficient for Qwen 3.5 9B inference. |

**Infrastructure prerequisite:** IITR-STAGING server cleanup (one environment per project principle). Remove abandoned experiments (~56 containers: Chunkr, SigNoz, OpenLIT, old DS-Kit prototypes, orphaned POCs). Retain: Navigation stack, Langfuse, OpenWebUI, MetaMCP.

### Part 2: Data Preparation

Feed all source documents to PageIndex for tree index generation. Ingest full documents, not cherry-picked by question. Text-only extraction, no visual elements.

**Universal principle:** All PageIndex input must have heading hierarchy. PageIndex `md_to_tree()` uses `#` characters to build the tree structure — flat text without headers produces a degenerate tree with poor retrieval. This applies to every source format: PDFs use `--pdf_path` (PageIndex handles structure natively), all other formats must be converted to markdown with proper heading hierarchy before ingestion.

**Quality gate:** Before PageIndex ingestion, validate that every converted document has at least H1/H2 structure. Flat or poorly structured files get re-converted, not ingested as-is.

| Source | Format | PageIndex Path | Notes |
|--------|--------|---------------|-------|
| Anwenderleitfaden | PDF | `--pdf_path` (native) | Primary knowledge base |
| Templates (56 total) | PDF/Word/Excel | PDF: `--pdf_path`; Word/Excel: convert to structured markdown → `--md_path` | Download from core.iitr.de chapter 4 (Keycloak auth) |
| Masterfragen | CSV/Excel | Convert to structured Q&A markdown → `--md_path` | 22 entries, knowledge source for 19/29 test questions |
| core.iitr.de chapters 1-12 | Web | Extract → one combined markdown (H1 per chapter) → `--md_path` | 7/29 test questions depend on this. Chrome DevTools MCP as extraction tool (proven in [#798](https://github.com/DaveX2001/deliverable-tracking/issues/798)). Fallback: [agent-browser](https://github.com/vercel-labs/agent-browser). |

**Data availability:** [Source Data Inventory (Google Drive)](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) — INPUT/ (knowledge base) and TEST_RUBRIK/ (evaluation rubric). Templates and web chapters require Keycloak authentication for download/extraction.

### Part 3: Test Rubric

Automated evaluation of the navigation system against the 29-question test set using an LLM judge.

**4 evaluation dimensions:**

| # | Dimension | Standard | PASS when |
|---|-----------|----------|-----------|
| 1 | **Content Accuracy** | User-intent standard (non-specialist perspective) | Key information present in the answer |
| 2 | **Format** | Dual-answer structure (general answer + navigation hint) | Both parts present |
| 3 | **Chapter Reference** | Answer text + tree traversal path verification | Chapter reference correct, no false positives |
| 4 | **Source Traceability** | Anti-hallucination grounding check | Answer grounded in source data |

**Scoring:** Binary PASS/FAIL per question. A question passes when content is accurate AND chapter reference is correct. Format and source traceability are quality signals tracked per question but don't independently fail a question.

**Evaluation split:**
- **AI (LLM judge):** Scores all 4 dimensions per question automatically on every test run. Existing test harness infrastructure in [IITR-RAG-Navigation](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Navigation/tree/main/infrastructure/dskit-rag/test-harness) provides the base (LLM judge via OpenRouter, CSV output). Adapt from 14 → 29 questions, add 4-dimension scoring.
- **Human (Stellmacher/Kraska):** Review CSV report at bi-weekly meetings (next: Mar 17). Qualitative sign-off — do answers actually help a non-specialist user navigate the DS-Kit?

**Output artifact:** CSV report with columns per dimension. Two modes: internal (with PASS/FAIL per dimension) and client-facing (full answers, no scoring). Extends the existing dual-CSV pattern from the legacy test harness.

**Special cases:** Q16 and Q28 redirect to support (no prices in DS-Kit) — intentional non-answers, scored as PASS if redirect is present.

**Ownership** (per [Dev Lead Witness & Review System](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/dev-lead-witness-review-system)): JA defines rubric dimensions and expected answers → Dev Lead provisions test infrastructure → Developer implements the LLM judge prompt and harness.

### Part 4: Evaluation & Iteration

Run the test harness, iterate toward ≥26/29. Tree index generation is a one-time step per document — regenerate only when source content changes. The iteration loop is:

1. Generate tree indexes from all prepared documents
2. Run test harness (29 questions × 4 dimensions)
3. Analyze failures — retrieval issue (wrong tree nodes selected) vs generation issue (right context, wrong answer)
4. Adjust: retrieval prompts (tree traversal instructions), answer generation prompts (format, language, detail level), or data preparation (heading hierarchy quality)
5. Re-run until ≥26/29

Present sample answers to Stellmacher at bi-weekly meeting (Mar 17) for qualitative validation.

---

## Source

**Data Files:**
- [`KI Testfragen (1).xlsx`](https://mail.google.com/mail/u/0/#all/19bfebdff5dcbf33) — 29 test questions with source references (Stellmacher, 2026-01-27)
- [`Fragen Navigationssystem.xlsx`](https://mail.google.com/mail/u/0/#all/19bfebdff5dcbf33) — 22 Masterfragen (Stellmacher, 2026-01-27)
- [Test Set with Quelle](https://docs.google.com/spreadsheets/d/1gKLPVazIDCVQtpZaR509-NbiVZbjEnmkY44raMdbM2A/edit?gid=492982273) — 29 questions with source references (Google Sheet)
- Portal: `https://core.iitr.de/tenant/3520/page/82173` — DS-Kit web interface chapters 1-12
- [Source Data Inventory (Drive)](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) — INPUT/ + TEST_RUBRIK/

**PageIndex:**
- [GitHub repo](https://github.com/VectifyAI/PageIndex) — MIT, open-source, vectorless tree-based RAG
- [Docs](https://docs.pageindex.ai/) — framework reference, cookbooks, tutorials

**IITR Repos:**
- [IITR-RAG-Navigation](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Navigation) — DS-Kit Navigation (this project)
- [IITR-RAG-Court-Judgments](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Court-Judgments) — Court rulings RAG
- [IITR-RAG-Masterfragen](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Masterfragen) — GDPR Q&A

**Reference Documents:**
- [Test Analysis](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-rag-test-analysis) — question-by-question verification (prior Typesense attempt)
- [IITR Materials Index](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/index)
- [Dev Lead Witness & Review System](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/dev-lead-witness-review-system) — test rubric ownership model
- [Zielkorridor (24-32h corridor)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-rag-estimate)

**Transcripts:**
- [2026-01-15 Client Meeting](https://app.fireflies.ai/view/01KF0N6JDANZB4JGW9WEP4GNMC) — data gap discussion + dual-answer decision with Stellmacher/Kraska
- [2026-03-04 Contract Meeting](https://drive.google.com/file/d/1wS9Z8jjH-hkeaCW5aQZ9aTXWBqIQ24H9/view) — Stellmacher cost corridor requirements, Kraska contract approval

**Sessions:**
- Original design: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-IITR--IITR-NAVIGATION/46a000cb-3044-40d7-adaf-e30987553859.jsonl
- Zielkorridor extraction: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/a868fafa-95e4-413d-95c8-f50bd3ff3ed4.jsonl
- Pass 1-5 extractions: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/{80abc9d7,ca51af04,96431cad,a084b417,d009a445}.jsonl
- Design doc rewrite: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/ebd33f3f-f258-4ab6-8ccd-5d8eef2061bd.jsonl
