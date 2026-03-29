---
publish: true
---

# DS-Kit Navigation — Production Readiness Design
[[client-iitr]]

Rebuilding the DS-Kit Navigation system on IITR's on-premise infrastructure using vector RAG: Typesense hybrid search with BGE-M3 embeddings, Docling document chunking, and Qwen 3.5 9B for answer generation. Current baseline: 17/29 on the test harness, iterating toward ≥26/29.

---

## Problem Statement

IITR's DS-Kit product serves ~3,500 customers who regularly contact support with recurring questions (27-28/day per Stellmacher). A navigation system reduces this support load by answering common questions automatically.

The initial prototype (Typesense + basic chunking + MiniLM embeddings) scored 17/29 (V1, January 2026). A rebuild with improved components — BGE-M3 embeddings (1024d), Docling HybridChunker (preserves heading hierarchy), and Qwen 3.5 9B via OpenRouter — matched the V1 score at 17/29 with zero errors in ~4 minutes. This is the current production baseline on IITR-STAGING.

A parallel evaluation of tree-based retrieval (PageIndex + LlamaIndex TreeRetriever) was explored and abandoned — the OSS library lacks retrieval implementation, making it unviable for production use. Vector RAG with the improved component stack is the confirmed approach going forward.

**This is NOT:**

IITR operates three separate RAG projects, each with distinct data and purpose. This design doc covers Navigation only.

| Project | Purpose | Data Input |
|---------|---------|------------|
| **DS-Kit Navigation** (this doc) | Support navigation — answer recurring customer questions with chapter references | Anwenderleitfaden PDF + 56 templates + core.iitr.de ch.1-12 + 29 test Q&A |
| [Court Judgments](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/main/court-judgments) | Court rulings RAG (Urteile) | German legal documents |
| [Masterfragen](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/main/masterfragen) | GDPR Q&A (Masterfragen) | 22 Masterfragen CSV |

Court Judgments and Masterfragen are separate projects with different data and retrieval — this design doc covers Navigation only.

However, all three projects share IITR-STAGING infrastructure. The Unified Infrastructure section ensures they coexist safely — shared services (Ollama, Langfuse), one OpenWebUI serving layer with per-project pipeline filters, and per-project compose files connected via shared Docker network. This prevents the collateral damage observed during #1112 repo migration, where changes to Navigation broke Court-Judgments and Masterfragen serving paths.

**Preconditions:**
- Design artifacts from prior attempt preserved: Pflichtenheft, test questions with source references (29 Q&A + Quelle column), Masterfragen CSV (22 entries), Anwenderleitfaden PDF, 56 DS-Kit templates
- Vector RAG baseline established: Typesense + BGE-M3 + Docling at 17/29 on IITR-STAGING (RTX 4000 SFF Ada, 20 GB VRAM)
- Target architecture: monorepo with trunk-based development (see [#1182](https://github.com/DaveX2001/deliverable-tracking/issues/1182))
- Financial reset: prior work uninvoiced, clean start with direct client relationship (Stellmacher as technical contact, Kraska as business contact)
- IITR-STAGING infrastructure consolidated — two-layer compose model with shared services (iitr-platform/infrastructure) and per-project stacks connected via shared Docker network (see Unified Infrastructure)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Navigation system is production-ready: accurate answers with chapter-referenced navigation on the 29-question test set |
| **Success** | Test harness scores ≥26/29 (>90%) across 4 evaluation dimensions (see Test Rubric). Answers include chapter reference from retrieval source metadata. Stellmacher/Kraska validate answer quality via CSV review at bi-weekly meetings — feedback feeds back into iteration. |
| **Done test** | Stellmacher sends a question to the navigation system, receives a chapter-referenced answer, and confirms it is correct — without developer involvement. |

---

## Approach

Five sections, ordered so each builds on the previous. The first four produce testable artifacts. Unified Infrastructure defines the hosting layer for all IITR projects.

### Stack

Deploy the retrieval and serving infrastructure on IITR-STAGING.

**Shared Infrastructure:**

| Component | Role | Why |
|-----------|------|-----|
| **Qwen 3.5 9B** via Ollama | Single model for answer generation | think:false + temperature:0 for deterministic RAG inference. Benchmarked on IITR hardware: 0.76s per call (RTX 4000 SFF Ada, 20 GB VRAM). |
| **OpenWebUI** | Chat frontend | Already deployed with IITR Keycloak SSO integration. Familiar to client (Stellmacher has used it). Pipeline filter system enables RAG backend integration. |
| **RTX 4000 SFF Ada** (20 GB VRAM) | GPU inference | Available on IITR-STAGING. Sufficient for Qwen 3.5 9B inference. |
| **Langfuse** | Observability + tracing | One OpenWebUI conversation = one Langfuse session. Traces every query through retrieval and generation. Enables improvement loop: identify failure patterns, measure iteration progress, provide evidence for client reviews. Already running on IITR-STAGING. |

**Retrieval Components:**

| Component | Role | Why |
|-----------|------|-----|
| **[Typesense](https://typesense.org/)** | Hybrid search (keyword + vector) | Proven at 17/29 (58.6%) with BGE-M3 embeddings. Hybrid search combines BM25 keyword matching with vector similarity — outperformed pure vector search (Chroma: 2/29) on same data. |
| **[BAAI/bge-m3](https://huggingface.co/BAAI/bge-m3)** | Embedding model | MIT, 1024d, served via HuggingFace TEI. Matched V1 baseline score. Replaced Qwen3-Embedding-4B (2560d) which underperformed on German domain text. |
| **[Docling HybridChunker](https://ds4sd.github.io/docling/)** | Document chunking | Preserves heading hierarchy during chunking. Replaced Chonkie SemanticChunker which destroyed document structure (0/4 test answers). |

**Infrastructure prerequisite:** IITR-STAGING server cleanup. Architecture principle: one environment per project, separate compose stacks.

| Action | Stack | Containers | Rationale |
|--------|-------|-----------|-----------|
| **Remove** | Chunkr | ~43 | Crash-looping, abandoned |
| **Remove** | rag-dev | 4 | Dev experiment, not in use |
| **Remove** | SigNoz + OpenLIT + OTEL | ~9 | Observability experiments, replaced by Langfuse |
| **Remove** | langfuse-poc | 3 | POC instance, separate from production Langfuse |
| **Remove** | typesense + dashboard | 2 | Old DS-Kit prototype, both unhealthy |
| **Remove** | dskit-* (exited) | 5 | Old DS-Kit prototype, already stopped |
| **Remove** | tei-qwen3 | 1 | Old embeddings experiment |
| **Retain** | rag-staging | — | Masterfragen project stack (separate project, separate data) |
| **Retain** | Langfuse | — | Production observability |
| **Retain** | MetaMCP | — | MCP gateway |
| **Retain** | OpenWebUI | — | Chat frontend (Keycloak SSO) |

#### Approach History

Tree-based retrieval (PageIndex + LlamaIndex TreeRetriever) was evaluated as an alternative. PageIndex OSS ships tree generation only — retrieval mechanisms exist exclusively in their commercial API. The evaluation concluded that vector RAG with improved components (Typesense hybrid search, BGE-M3 embeddings, Docling chunking) is the confirmed production approach. See [#1093 comments](https://github.com/DaveX2001/deliverable-tracking/issues/1093) for full evaluation history.

### Data Preparation

**Incremental ingestion with rubric checkpoints:** Ingest all sources as-is — no content modification (noise removal, reformatting) before measuring. Add sources one at a time, running the test harness after each addition to observe which questions each source answers. Ingestion order is the Developer's choice. Content cleaning (removing irrelevant sections, deduplication) is a post-scores optimization pass — only after initial results reveal where noise actually hurts.

Ingest full documents, not cherry-picked by question. Text-only extraction, no visual elements.

#### Ingestion Pipeline

Extract text from all sources, chunk with Docling HybridChunker (preserves heading hierarchy), embed with BGE-M3 via TEI, store in Typesense for hybrid search (keyword + vector).

**Quality gate:** Before ingestion, validate that text extraction succeeded (not empty, not garbled OCR artifacts) and content language is German. Truncated or corrupted extractions get re-extracted, not ingested as-is.

| Source | Format | Pipeline | Notes |
|--------|--------|----------|-------|
| Anwenderleitfaden | PDF | Extract text → Docling HybridChunker → BGE-M3 → Typesense | Primary knowledge base |
| Templates (56 total) | PDF/Word/Excel | Extract text → Docling HybridChunker → BGE-M3 → Typesense | Download from core.iitr.de chapter 4 (Keycloak auth) |
| Masterfragen | CSV/Excel | Convert to text → Docling HybridChunker → BGE-M3 → Typesense | 22 entries, knowledge source for 19/29 test questions |
| core.iitr.de chapters 1-12 | Web | Extract → text → Docling HybridChunker → BGE-M3 → Typesense | 7/29 test questions depend on this. Chrome DevTools MCP as extraction tool (proven in [#798](https://github.com/DaveX2001/deliverable-tracking/issues/798)). Fallback: [agent-browser](https://github.com/vercel-labs/agent-browser). |

**Data availability:** [Source Data Inventory (Google Drive)](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) — INPUT/ (knowledge base) and TEST_RUBRIK/ (evaluation rubric). Templates and web chapters require Keycloak authentication for download/extraction.

### Test Rubric

Automated evaluation of the navigation system against the 29-question test set. Build the test harness **from scratch** — the existing harness in iitr-platform/navigation is inspiration only, not a base to extend. Use Sonnet 4.6 via OpenRouter as the LLM judge (external model evaluates local Qwen output — no self-evaluation).

**Two-tier scoring:**

| Tier | Dimensions | CSV Source | Evaluator | Scoring |
|------|-----------|------------|-----------|---------|
| **Pass/Fail** | 1. Content Accuracy, 2. Format | Column B (expected answer) | AI (LLM judge — Sonnet 4.6) | Semantic match against expected answer. These dimensions determine the score. |
| **Signal** | 3. Chapter Reference | Column C (Quelle) | Human (Stellmacher) | Warning signal — evaluate empirically with real outputs before defining detection rules. Stellmacher judges correctness at bi-weekly review. |
| **Signal** | 4. Source Traceability | Column C (Quelle) | AI (signal only) | Is the Quelle source present in retrieved chunks? Warning if not — does not independently fail a question. |

**Column D (grooming discussion):** Consider adding a `chapter-reference-expected` (yes/no) column to the test CSV. Questions that reference a specific DS-Kit chapter (e.g., "Kapitel 8", "Kapitel 11, Unterpunkt 3") get `yes`. Support redirects and procedural answers get `no`. This lets the judge skip chapter reference evaluation for questions where no chapter applies. Observe chapter reference behavior empirically with real outputs first — bring findings to Stellmacher before committing to this structure.

**Golden example** — expected output format for a question WITH navigation hint:

```
Frage: Wo finde ich die Texte für die Webseitengestaltung?

Antwort: Texte für die Webseiten-Datenschutzerklärung finden Sie in Kapitel 8
des Datenschutz-Kit. Dort erhalten Sie eine deutsche und englische Vorlage
für die Erstellung einer Datenschutzerklärung.

📍 Navigation: DS-Kit → Kapitel 8: Webseiten-Datenschutz
```

Navigation hint appears only when the answer references a specific DS-Kit chapter. For support redirects (e.g., "Bitte wenden Sie sich an dskit@iitr.de") and procedural answers, no navigation hint is generated.

**Output artifact:** CSV report with columns per dimension, uploaded to Google Drive with date and time in filename (e.g., `dskit-nav-test-2026-03-15-14-30.csv`). Two modes: internal (with PASS/FAIL + signals per dimension) and client-facing (full answers, no scoring). Linked for Dev Lead witness review.

**Special cases:** Q16 and Q28 redirect to support (no prices in DS-Kit) — intentional non-answers, scored as PASS if redirect is present.

**Ownership** (per [Dev Lead Witness & Review System](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/dev-lead-witness-review-system)): JA defines rubric dimensions and expected answers → SA approves rubric definition → Developer creates the LLM judge harness → Dev Lead approves test results via witness review of CSV report.

### Evaluation & Iteration

Run the test harness, iterate toward ≥26/29.

#### Iteration Loop

1. Chunk all prepared documents via Docling HybridChunker, embed with BGE-M3, store in Typesense
2. Run test harness (29 questions × 4 dimensions)
3. Analyze failures using the diagnostic chain (see below)
4. Adjust retrieval knobs, re-run until ≥26/29

**Retrieval knobs:**
- **Chunker config:** Docling HybridChunker parameters — chunk size, overlap. Tune based on failure analysis.
- **Embedding model:** BGE-M3 (1024d) via TEI. Proven baseline — replaced Qwen3-Embedding-4B which underperformed on German domain text.
- **Search config:** Typesense hybrid search weighting (keyword vs vector balance).
- **Prompts:** Answer generation instructions for Qwen 3.5 9B.

#### Client Feedback Checkpoint

When confident in iteration results, send CSV report to Stellmacher for review. CSV columns: question, expected answer, RAG answer, expected source, actual source. Stellmacher provides feedback on semantic accuracy and format — feedback feeds back into the next iteration cycle. This is not every run; only when results warrant client review (e.g., after significant score improvement or before bi-weekly meeting).

#### Diagnostic Chain

| Failure Type | Meaning | Fix | Owner |
|-------------|---------|-----|-------|
| **Data Source** | Answer data doesn't exist in ingested sources | Flag → Dev Lead → client provides missing data | Developer escalates |
| **Retrieval** | Data exists but retrieval missed it | Chunker config, embedding parameters, Typesense search weighting | Developer |
| **Generation** | Right data retrieved, wrong answer produced | Prompt change (answer generation instructions) | Developer |

Dependency: Data Source → Retrieval → Generation. Fix data gaps first, then retrieval, then generation. This is a universal RAG diagnostic pattern.

### Unified Infrastructure

IITR-STAGING hosts three RAG projects that share infrastructure. This part defines the ownership model, deployment contract, and serving architecture so that changes to one project cannot break another.

#### Infra/App Compose Convention

Per SA directive ("why always deploy another one?") and [CCI #646 deployment convention](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design), the system splits into two compose files. The infra compose runs shared services (one instance, never duplicated per branch). The app compose runs the serving layer (isolated per branch for preview environments).

**Architecture: Mono-repo with trunk-based development** ([iitr-platform](https://github.com/WILSCH-AI-SERVICES/iitr-platform), [#1182](https://github.com/DaveX2001/deliverable-tracking/issues/1182)). Four source repos consolidated into `iitr-platform/` with subproject directories: `infrastructure/`, `navigation/`, `court-judgments/`, `masterfragen/`. Migration completed via [#1191](https://github.com/DaveX2001/deliverable-tracking/issues/1191).

**`docker-compose.infra.yml`** — Shared infrastructure (`infrastructure/`). Runs once on the server:

| Service | Port | Notes |
|---------|------|-------|
| Ollama | 11436 | Single GPU instance, all projects. Qwen 3.5 9B + 6 other models. |
| TEI (bge-m3) | 8080 | BAAI/bge-m3 (1024d) embedding service. Single endpoint for all projects. |
| Typesense | 8109 | Hybrid search (keyword + vector). All projects use separate collections via one instance. |
| Langfuse | — | Observability for all projects (web + worker + clickhouse + minio + redis + postgres). |

Creates the `iitr-infra` network. App compose connects to this network as external.

**`docker-compose.yml`** — Serving layer (app, isolated per branch via `COMPOSE_PROJECT_NAME`):

| Service | Port | Notes |
|---------|------|-------|
| OpenWebUI | 3006 | Chat frontend, serves all 3 projects via Models. Keycloak SSO. |
| Pipelines | — | Pipeline filter sidecar. All projects contribute filters via bind-mount. |

Staging runs one app instance. Preview environments get additional instances on separate ports (see Preview Environments).

**Per-Project Code** (subdirectories within `iitr-platform/`):

| Project | Owns | Connects to shared via |
|---------|------|----------------------|
| `navigation/` | Pipeline filter + prompts, collection `dskit_navigation` | Bind-mount into Pipelines |
| `masterfragen/` | Pipeline filter + prompts, collection `dskit_masterfragen` | Bind-mount into Pipelines |
| `court-judgments/` | Pipeline filter + prompts, collection `urteile` | Bind-mount into Pipelines |

#### Serving Layer

One OpenWebUI instance serves all three projects. Each project is a pipeline filter in the Pipelines sidecar. Users select which project to query via model selection in the OpenWebUI UI.

**Standard pattern:** All projects use OpenWebUI pipeline filters. Each project contributes a pipeline filter via volume mount into the shared Pipelines container. Pipeline filters handle retrieval (Typesense hybrid search) and prompt compilation per project.

**Per-model access control:** Each Model has visibility settings managed via OpenWebUI admin. By default, clients (Stellmacher, Kraska) see all production-ready Models across all three projects. Models under active development are hidden from clients until ready. Internal users (Wilsch AI) see all Models at all times.

#### Directory Structure & Cleanup

Canonical path: `/home/shared/projects/`. Mono-repo migration completed ([#1191](https://github.com/DaveX2001/deliverable-tracking/issues/1191)) — four source repos consolidated into `iitr-platform/` with subproject directories. Per [Ops Manual](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services) convention, compose + Makefile per subproject.

**Current state:**

| Directory | Purpose |
|-----------|---------|
| `iitr-platform/` | Mono-repo (infrastructure + navigation + court-judgments + masterfragen) |
| `metamcp/` | MCP gateway |

#### Vector Stores

All projects share one Typesense instance and one TEI endpoint (BGE-M3, 1024d) in shared infrastructure. Each project owns a separate collection — data is isolated, engine is shared.

| Collection | Project | Data |
|------------|---------|------|
| `dskit_navigation` | Navigation | Anwenderleitfaden PDF + templates + web chapters + Q&A |
| `dskit_masterfragen` | Masterfragen | 22 Masterfragen Q&A entries |
| `urteile` | Court Judgments | German court rulings |

Qdrant (previously used by Masterfragen) and Qwen3-Embedding-8B (previously used by Court Judgments) are retired. BGE-M3 is the standard embedding model across all projects (Marius directive).

#### Caddy Routing

Two domains. Project separation happens via OpenWebUI Models, not Caddy routing.

| Domain | Service |
|--------|---------|
| `rag-staging.iitr-cloud.de` | OpenWebUI (all 3 projects) |
| `langfuse.iitr-cloud.de` | Langfuse |



#### Deployment Contract

Git push is the only deployment interface. No SSH, no manual Docker commands. Per [CCI #646](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design): the push IS the deployment.

**Startup order:** Infra first (Ollama, TEI, Typesense, Langfuse), then app (OpenWebUI, Pipelines). Services depend on the `iitr-infra` network + GPU services.

**GHA workflow (3 triggers):**

| Trigger | Action | Script |
|---------|--------|--------|
| Push to feature branch | Deploy preview | `scripts/deploy-preview.sh` |
| PR closed/merged | Tear down preview | `scripts/cleanup-preview.sh` |
| Push to staging branch | Update staging | `scripts/deploy-staging.sh` |

**SSH enforcement:** Read-only for investigation (logs, inspect). All writes go through git push → GHA. The AI's SSH config uses a restricted `agent` user.

#### Health Checks

Back pressure per [CCI #646](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design). Each level returns an exit code — GHA reads it: 0 = success, non-zero = failure.

| Level | Check | What it catches |
|-------|-------|-----------------|
| 1 | `docker compose config` | Invalid compose file |
| 2 | `docker compose up --wait` | Container startup failures |
| 3 | `curl -f localhost:8109/health` | Typesense not responding |
| 4 | `curl -f localhost:8080/health` | TEI (BGE-M3) not loaded |
| 5 | `curl -f localhost:11436/api/tags` | Ollama models not available |
| 6 | `curl -f localhost:3006` | OpenWebUI not reachable |
| 7 | Smoke test: 1 question through pipeline filter | End-to-end RAG broken |

Pipeline filter startup depends on Typesense — if Typesense is not ready, the filter scores 0/29 silently. Level 3 gates Level 7.

#### Data Ingestion

Source data lives in git. GHA runs ingestion scripts when data files change. The commit that added data IS the audit trail.

| Project | Data in git | Size | Ingestion script | Trigger |
|---------|------------|------|-----------------|---------|
| Navigation | Templates, web chapter markdown, PDF, Q&A | ~5 MB | `navigation/scripts/ingest.py` | GHA on `navigation/data/**` change |
| Masterfragen | CSV (22 Q&A entries) | ~50 KB | `masterfragen/scripts/ingest.py` | GHA on `masterfragen/data/**` change |
| Court Judgments | `Urteile-DSGVO.zip` (2,635 HTML/PDF) | ~40 MB | `court-judgments/scripts/ingest.py` | GHA on `court-judgments/data/**` change |

CJ pipeline design is tracked in [#1234](https://github.com/DaveX2001/deliverable-tracking/issues/1234). The original Chunkr-based pipeline is gone — replacement uses lightweight HTML parsing (BeautifulSoup) since source files are HTML, not scanned PDFs.

#### Preview Environments

Per CCI #646 Part 4: every push to a feature branch triggers a preview environment on the server.

```
Server
├── infra (always running, one instance)
│   ├── Ollama (GPU, port 11436)
│   ├── TEI (BGE-M3, port 8080)
│   ├── Typesense (port 8109)
│   └── Langfuse
│
├── staging (app compose, main branch)
│   ├── OpenWebUI (port 3006)
│   └── Pipelines (mounts all project filters)
│
└── preview-issue-1234 (app compose, worktree branch)
    ├── OpenWebUI (port auto-assigned)
    └── Pipelines (mounts modified filters from branch)
```

Preview and staging share the same infra services (same Ollama, same Typesense collections, same TEI). The app compose clones OpenWebUI volumes from staging on first deploy. Pipeline filter code comes from the branch's working directory.

**Preview URL:** `issue-{number}.iitr-cloud.de` — Caddy config generated by deploy script, committed to git.

**Undefined:** Volume state promotion — when a preview's OpenWebUI volume contains state the developer wants on staging (e.g., new model configuration), how does it transfer on PR merge? Requires a real case to evaluate.

---

## Source

**Data Files:**
- [`KI Testfragen (1).xlsx`](https://mail.google.com/mail/u/0/#all/19bfebdff5dcbf33) — 29 test questions with source references (Stellmacher, 2026-01-27)
- [`Fragen Navigationssystem.xlsx`](https://mail.google.com/mail/u/0/#all/19bfebdff5dcbf33) — 22 Masterfragen (Stellmacher, 2026-01-27)
- [Test Set with Quelle](https://docs.google.com/spreadsheets/d/1gKLPVazIDCVQtpZaR509-NbiVZbjEnmkY44raMdbM2A/edit?gid=492982273) — 29 questions with source references (Google Sheet)
- Portal: `https://core.iitr.de/tenant/3520/page/82173` — DS-Kit web interface chapters 1-12
- [Source Data Inventory (Drive)](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) — INPUT/ + TEST_RUBRIK/

**Vector RAG Stack:**
- [Typesense](https://typesense.org/) — hybrid search engine (keyword + vector), current production approach
- [BAAI/bge-m3](https://huggingface.co/BAAI/bge-m3) — MIT embedding model (1024d), served via HuggingFace TEI
- [Docling](https://ds4sd.github.io/docling/) — document chunking (HybridChunker preserves heading hierarchy)
- [OpenWebUI Models](https://docs.openwebui.com/features/ai-knowledge/models) — per-project preset system (Filter, Knowledge, Access Control binding)

**IITR Repos:**
- [iitr-platform/navigation](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/main/navigation) — DS-Kit Navigation (this project)
- [iitr-platform/court-judgments](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/main/court-judgments) — Court rulings RAG
- [iitr-platform/masterfragen](https://github.com/WILSCH-AI-SERVICES/iitr-platform/tree/main/masterfragen) — GDPR Q&A

**Reference Documents:**
- [Test Analysis](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-rag-test-analysis) — question-by-question verification (prior Typesense attempt)
- [IITR Materials Index](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/index)
- [Dev Lead Witness & Review System](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/dev-lead-witness-review-system) — test rubric ownership model
- [Zielkorridor (24-32h corridor)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-rag-estimate)

**Transcripts:**
- [2026-01-15 Client Meeting](https://app.fireflies.ai/view/01KF0N6JDANZB4JGW9WEP4GNMC) — data gap discussion + dual-answer decision with Stellmacher/Kraska
- [2026-03-04 Contract Meeting](https://drive.google.com/file/d/1wS9Z8jjH-hkeaCW5aQZ9aTXWBqIQ24H9/view) — Stellmacher cost corridor requirements, Kraska contract approval
- [2026-03-14 Grooming](https://app.fireflies.ai/view/01KKPCF74TMEAYTM0PQEBBD5HW) — PageIndex OSS limitation discovery, retrieval approach discussion
- [2026-03-17 KI-Update IITR](https://app.fireflies.ai/view/01KKB4PD373XWHY3K0B0PDZMSC) — client sync: format confirmed, CSV feedback loop, staging consolidation
- [2026-03-18 Strategy Meeting](https://app.fireflies.ai/view/01KM0CAF30PJ3V27S2RDB15BHQ) — Vector B confirmed as approach, monorepo + trunk-based, issue cleanup
- [2026-03-18 Grooming](https://app.fireflies.ai/view/01KM0732PDYDAT62D5DFDAHV01) — trunk-first decomposition, spike pattern, design docs updated only after proof points
- [2026-03-22 Grooming](https://app.fireflies.ai/view/01KMB243RF95QP9ZFG9VGBWFX9) — CJ data loss discovered, CCI #646 deployment convention assignment, MF Typesense consolidation

**Deployment Convention:**
- [CCI #646 Design Doc — Deployment & Runtime State](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-runtime-state-design) — infra/app convention, GHA deploy, read-only SSH

**Sessions:**
- Original design: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-IITR--IITR-NAVIGATION/46a000cb-3044-40d7-adaf-e30987553859.jsonl
- Zielkorridor extraction: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/a868fafa-95e4-413d-95c8-f50bd3ff3ed4.jsonl
- Pass 1-5 extractions: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/{80abc9d7,ca51af04,96431cad,a084b417,d009a445}.jsonl
- Design doc rewrite: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/ebd33f3f-f258-4ab6-8ccd-5d8eef2061bd.jsonl
- SA Review v2 pass: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/99e9faee-c74a-40ab-ba0f-358b8537b16d.jsonl
- SA Review v2 final corrections: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/775ac9fa-bc9e-4dd5-953c-77ce27518e71.jsonl
- PageIndex limitation extraction pass: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/66749a56-5f2d-4554-b902-cc4a76dd89f6.jsonl
- SA Decision + Track B extraction: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude_projects-03-IITR--deliverable/a1ca960f-e5da-47cd-bfcf-7a59d1ce8859.jsonl
- Pass 6 (Unified Infrastructure): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/5347bf5c-62cd-49e1-83dd-fdda0c893b26.jsonl
- Pass 7 (Vector B + monorepo update): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/3deb3c69-4606-426f-9f23-0a0af76ede45.jsonl
- Pass 8 (SA review — access control fix): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/65c03d49-b808-4011-b16e-7607e99f1b9a.jsonl
- Pass 10 (mono-repo reference update): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/e2d015aa-d5c4-4c0b-9372-9a83783d9d3f.jsonl
- Pass 11 (MF Typesense consolidation): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/85060f90-01bf-4532-9e1e-5425e5ccd92e.jsonl
- Pass 12 (CCI #646 deployment convention): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/d13491e9-d102-4b36-a846-54774834c414.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

