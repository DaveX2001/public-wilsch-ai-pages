---
publish: true
---

# DS-Kit Navigation — Production Readiness Design
[[client-iitr]]

Rebuilding the DS-Kit Navigation system on IITR's on-premise infrastructure using a two-track approach: vector RAG (Track B) ships first for client-facing results, while tree-based retrieval (Track A) remains the target architecture.

---

## Problem Statement

IITR's DS-Kit product serves ~3,500 customers who regularly contact support with recurring questions (27-28/day per Stellmacher). A navigation system reduces this support load by answering common questions automatically.

A previous prototype (Typesense + chunked RAG) scored 17/29 on the test harness. The chunking approach proved fragile — vector-based RAG suffers from four structural limitations: lack of multi-hop reasoning, chunking breaking semantic integrity, low recall from embedding mismatch, and context confusion from redundant retrievals (see [RAG for Technical Manuals](https://pageindex.ai/blog/technical-manuals) for side-by-side evidence). That implementation is being retired.

This project rebuilds the navigation system using two parallel retrieval tracks. **Track B (vector RAG)** uses semantic chunking, local embeddings, and Chroma to deliver initial results for the March 17 client sync. **Track A (tree-based retrieval)** uses PageIndex tree generation with LlamaIndex TreeSelectLeafRetriever for level-by-level traversal — producing traceable, chapter-referenced answers as a natural byproduct of tree navigation. Track A remains the long-term aspiration; Track B is the pragmatic shipping path.

**This is NOT:**

IITR operates three separate RAG projects, each with distinct data and purpose. This design doc covers Navigation only.

| Project | Purpose | Data Input |
|---------|---------|------------|
| **DS-Kit Navigation** (this doc) | Support navigation — answer recurring customer questions with chapter references | Anwenderleitfaden PDF + 56 templates + core.iitr.de ch.1-12 + 29 test Q&A |
| [Court Judgments](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Court-Judgments) | Court rulings RAG (Urteile) | German legal documents |
| [Masterfragen](https://github.com/WILSCH-AI-SERVICES/IITR__IITR-RAG-Masterfragen) | GDPR Q&A (Masterfragen) | 22 Masterfragen CSV |

Court Judgments and Masterfragen are not replaced by PageIndex — different projects, different data, different retrieval.

However, all three projects share IITR-STAGING infrastructure. A unified infrastructure design (Part 5) ensures they coexist safely — shared services (Ollama, Langfuse), one OpenWebUI serving layer with per-project pipeline filters, and per-project compose files connected via shared Docker network. This prevents the collateral damage observed during #1112 repo migration, where changes to Navigation broke Court-Judgments and Masterfragen serving paths.

**Preconditions:**
- Design artifacts from prior attempt preserved: Pflichtenheft, test questions with source references (29 Q&A + Quelle column), Masterfragen CSV (22 entries), Anwenderleitfaden PDF, 56 DS-Kit templates
- PageIndex benchmarked on IITR-STAGING: Qwen 3.5 9B viable on available hardware (RTX 4000 SFF Ada, 20 GB VRAM)
- Financial reset: prior work uninvoiced, clean start with direct client relationship (Stellmacher as technical contact, Kraska as business contact)
- IITR-STAGING infrastructure consolidated — two-layer compose model with shared services (iitr-infrastructure) and per-project stacks connected via shared Docker network (see Part 5)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Navigation system is production-ready: accurate answers with chapter-referenced navigation on the 29-question test set |
| **Success** | Test harness scores ≥26/29 (>90%) across 4 evaluation dimensions (see Test Rubric). Answers include chapter reference from tree traversal path. Stellmacher/Kraska validate answer quality via sample review at bi-weekly meetings. |
| **Done test** | Stellmacher sends a question to the navigation system, receives a chapter-referenced answer, and confirms it is correct — without developer involvement. |

---

## Approach

Five parts, ordered so each builds on the previous. Parts 1-4 produce testable artifacts. Part 5 defines the infrastructure that hosts all IITR projects.

### Part 1: Stack

Deploy the retrieval and serving infrastructure on IITR-STAGING.

**Shared Infrastructure:**

| Component | Role | Why |
|-----------|------|-----|
| **Qwen 3.5 9B** via Ollama | Single model for all pipeline stages | Tree traversal (think:false, forced by Ollama structured output), answer generation (think:true, +25% instruction-following). Benchmarked on IITR hardware: 0.76s per traversal call, ~8.5s full pipeline. |
| **OpenWebUI** | Chat frontend | Already deployed with IITR Keycloak SSO integration. Familiar to client (Stellmacher has used it). Pipeline filter system enables RAG backend integration. |
| **RTX 4000 SFF Ada** (20 GB VRAM) | GPU inference | Available on IITR-STAGING. Sufficient for Qwen 3.5 9B inference. |
| **Langfuse** | Observability + tracing | One OpenWebUI conversation = one Langfuse session. Traces every query through retrieval and generation. Enables improvement loop: identify failure patterns, measure iteration progress, provide evidence for client reviews. Already running on IITR-STAGING. |

**Track B Components (focus — ships first):**

| Component | Role | Why |
|-----------|------|-----|
| **[Typesense](https://typesense.org/)** | Hybrid search (keyword + vector) | Proven at 17/29 (58.6%) with BGE-M3 embeddings. Hybrid search combines BM25 keyword matching with vector similarity — outperformed Chroma (2/29) on same data. |
| **[BAAI/bge-m3](https://huggingface.co/BAAI/bge-m3)** | Embedding model | MIT, 1024d, served via HuggingFace TEI. Matched V1 baseline score. Replaced Qwen3-Embedding-4B (2560d) which underperformed on German domain text. |
| **[Docling HybridChunker](https://ds4sd.github.io/docling/)** | Document chunking | Preserves heading hierarchy during chunking. Replaced Chonkie SemanticChunker which destroyed document structure (0/4 test answers). |

**Track A Components (parallel if bandwidth):**

| Component | Role | Why |
|-----------|------|-----|
| **PageIndex** (MIT, open-source) | Tree index generation | Converts PDFs and markdown into hierarchical tree JSON (node_id, title, summary, page_index, children). OSS library provides tree generation only — retrieval must be implemented separately (see PageIndex OSS Limitation below). |
| **[LlamaIndex TreeSelectLeafRetriever](https://github.com/run-llama/llama_index/blob/main/llama-index-core/llama_index/core/indices/tree/select_leaf_retriever.py)** | Tree-based retrieval | Framework-provided level-by-level LLM traversal on tree structure. |

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

#### PageIndex OSS Limitation

PageIndex OSS ([VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex), MIT, 21.5k stars) ships tree generation only. The retrieval mechanisms described in their documentation — level-by-level traversal, hybrid MCTS, value-based search — exist exclusively in their commercial API/dashboard. The [cookbook](https://github.com/VectifyAI/PageIndex/blob/main/cookbook/pageindex_RAG_simple.ipynb) demonstrates a workaround: pass the entire tree (~30KB for one document, ~10K tokens) in a single LLM prompt and ask the model to select relevant node IDs. This approach does not scale — with 60+ documents, combined tree context exceeds any local model's context window.

The intended retrieval design is level-by-level tree traversal: start at root nodes, LLM selects which children to descend into, recurse until leaf nodes. Each level requires one LLM call seeing only the current level's nodes (titles + summaries), not the entire tree. This is what makes tree-based retrieval superior to chunked vector RAG — the LLM reasons through document structure iteratively, producing traceable chapter references as a natural byproduct.

**The principle stands: tree-based structure-aware retrieval > chunked vector RAG.** The implementation must change.

#### Retrieval Approach — SA Decision

**Track B (vector RAG):** Typesense hybrid search + BAAI/bge-m3 embeddings + Docling HybridChunker. Current score: 17/29 (58.6%), matching V1 baseline. ~90% of #1112 infrastructure (Ollama, OpenWebUI, Langfuse, compose stack) survives regardless of track. Existing PageIndex tree on server: `/home/shared/projects/IITR-RAG-Navigation/index/anwenderleitfaden-datenschutz-kit-2025-03_tree.json`

**Track A (tree traversal):** LlamaIndex TreeSelectLeafRetriever on existing PageIndex tree JSON. Alternatives evaluated and discarded: RAPTOR (semantic clusters, loses heading hierarchy), GraphRAG/HiRAG (knowledge graph, not document tree), custom TreeRetriever (more code ownership but no framework support).

### Part 2: Data Preparation

**Incremental ingestion with rubric checkpoints:** Ingest all sources as-is — no content modification (noise removal, reformatting) before measuring. Add sources one at a time, running the test harness after each addition to observe which questions each source answers. Ingestion order is the Developer's choice. Content cleaning (removing irrelevant sections, deduplication) is a post-scores optimization pass — only after initial results reveal where noise actually hurts.

Ingest full documents, not cherry-picked by question. Text-only extraction, no visual elements.

#### Track B Pipeline (focus)

Extract text from all sources, chunk with Chonkie SemanticChunker, embed with Qwen3-Embedding, store in Chroma. The SemanticChunker groups sentences by semantic similarity — no heading hierarchy required.

**Quality gate:** Before ingestion, validate that text extraction succeeded (not empty, not garbled OCR artifacts) and content language is German. Truncated or corrupted extractions get re-extracted, not ingested as-is.

| Source | Format | Track B Pipeline | Notes |
|--------|--------|-----------------|-------|
| Anwenderleitfaden | PDF | Extract text → SemanticChunker → Chroma | Primary knowledge base |
| Templates (56 total) | PDF/Word/Excel | Extract text → SemanticChunker → Chroma | Download from core.iitr.de chapter 4 (Keycloak auth) |
| Masterfragen | CSV/Excel | Convert to text → SemanticChunker → Chroma | 22 entries, knowledge source for 19/29 test questions |
| core.iitr.de chapters 1-12 | Web | Extract → text → SemanticChunker → Chroma | 7/29 test questions depend on this. Chrome DevTools MCP as extraction tool (proven in [#798](https://github.com/DaveX2001/deliverable-tracking/issues/798)). Fallback: [agent-browser](https://github.com/vercel-labs/agent-browser). |

#### Track A Pipeline

All PageIndex input must have heading hierarchy. PageIndex `md_to_tree()` uses `#` characters to build the tree structure — flat text without headers produces a degenerate tree with poor retrieval. This applies to every source format: PDFs use `--pdf_path` (PageIndex handles structure natively), all other formats must be converted to markdown with proper heading hierarchy before ingestion.

**Quality gate:** Before PageIndex ingestion, validate that every converted document has at least H1/H2 structure. Flat or poorly structured files get re-converted, not ingested as-is.

| Source | Format | PageIndex Path | Notes |
|--------|--------|---------------|-------|
| Anwenderleitfaden | PDF | `--pdf_path` (native) | Primary knowledge base — zero conversion needed |
| Templates (56 total) | PDF/Word/Excel | PDF: `--pdf_path`; Word/Excel: convert to structured markdown → `--md_path` | Download from core.iitr.de chapter 4 (Keycloak auth) |
| Masterfragen | CSV/Excel | Convert to structured Q&A markdown → `--md_path` | 22 entries, knowledge source for 19/29 test questions |
| core.iitr.de chapters 1-12 | Web | Extract → one combined markdown with full heading hierarchy (H1/H2/H3+) → `--md_path` | 7/29 test questions depend on this. Preserve native heading depth from core.iitr.de. |

**Data availability:** [Source Data Inventory (Google Drive)](https://drive.google.com/drive/folders/1gnxBulrnkGh-Oyly_jabofNo4SVivQhR) — INPUT/ (knowledge base) and TEST_RUBRIK/ (evaluation rubric). Templates and web chapters require Keycloak authentication for download/extraction.

### Part 3: Test Rubric

Automated evaluation of the navigation system against the 29-question test set. Build the test harness **from scratch** — the existing harness in IITR-RAG-Navigation is inspiration only, not a base to extend. Use Sonnet 4.6 via OpenRouter as the LLM judge (external model evaluates local Qwen output — no self-evaluation).

**Two-tier scoring:**

| Tier | Dimensions | CSV Source | Evaluator | Scoring |
|------|-----------|------------|-----------|---------|
| **Pass/Fail** | 1. Content Accuracy, 2. Format | Column B (expected answer) | AI (LLM judge — Sonnet 4.6) | Semantic match against expected answer. These dimensions determine the score. |
| **Signal** | 3. Chapter Reference | Column C (Quelle) | Human (Stellmacher) | Warning signal — evaluate empirically with real outputs before defining detection rules. Stellmacher judges correctness at bi-weekly review. |
| **Signal** | 4. Source Traceability | Column C (Quelle) | AI (signal only) | Is the Quelle source present in retrieved tree nodes? Warning if not — does not independently fail a question. |

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

### Part 4: Evaluation & Iteration

Run the test harness, iterate toward ≥26/29.

#### Track B Iteration (focus)

The iteration loop:

1. Chunk all prepared documents via Chonkie, embed with Qwen3-Embedding, store in Chroma
2. Run test harness (29 questions × 4 dimensions)
3. Analyze failures using the diagnostic chain (see below)
4. Adjust retrieval knobs, re-run until ≥26/29

**Track B retrieval knobs:**
- **Chunker selection:** Chonkie offers multiple chunkers (SemanticChunker as start). Try different chunkers via test harness to find optimal retrieval quality.
- **Embedding model:** Qwen3-Embedding-8B → 4B → bge-m3. Stick with these three — sufficient playground.
- **Chunk parameters:** Size, overlap — tune based on failure analysis.
- **Prompts:** Answer generation instructions for Qwen 3.5 9B.

#### Track A Iteration

**Dual-model option:** Tree *traversal* (runtime) uses Qwen 3.5 9B locally. Tree *creation* (one-time) may use a stronger model (e.g., Sonnet 4.6 via OpenRouter) for higher-quality tree structure. Better trees = better retrieval without changing the runtime model.

**Expert knowledge injection:** Inject domain-specific routing rules directly into the tree search prompt — no embedding fine-tuning needed. Example: "If query mentions Löschfristen, prioritize Kapitel 3." Build a set of IITR-specific routing rules from the 29 test questions and their Quelle column, then inject relevant rules per query. See [LLM Tree Search](https://docs.pageindex.ai/tutorials/tree-search/llm) for the prompt template and preference retrieval pattern.

#### Diagnostic Chain (universal)

| Failure Type | Meaning | Fix | Owner |
|-------------|---------|-----|-------|
| **Data Source** | Answer data doesn't exist in ingested sources | Flag → Dev Lead → client provides missing data | Developer escalates |
| **Retrieval** | Data exists but retrieval missed it | Track B: chunker/embedding/chunk params. Track A: expert knowledge rules, tree quality (regenerate with stronger model), heading hierarchy fixes. | Developer |
| **Generation** | Right data retrieved, wrong answer produced | Prompt change (answer generation instructions) | Developer |

Dependency: Data Source → Retrieval → Generation. Fix data gaps first, then retrieval, then generation. This is a universal RAG diagnostic pattern.

Present sample answers to Stellmacher at bi-weekly meeting (Mar 17) for qualitative validation.

### Part 5: Unified Infrastructure

IITR-STAGING hosts three RAG projects that share infrastructure. This part defines the ownership model, deployment contract, and serving architecture so that changes to one project cannot break another.

#### Two-Layer Compose Model

Per SA directive ("why always deploy another one?"), cross-project services run as single shared instances. Project-specific services (pipeline filters, vector stores) live in their respective repos.

**Target architecture:** Mono repo with subproject structure. The four current repos (`iitr-infrastructure`, `IITR-RAG-Navigation`, `IITR-RAG-Court-Judgments`, `IITR-RAG-Masterfragen`) are already deployment-coupled via `iitr-shared-network` — multi-repo adds git overhead without isolation benefit. Mono repo enables single CLAUDE.md with subproject routing, eliminates port conflicts from duplicate service definitions, and simplifies developer onboarding (one clone, one compose context). Migration is not scheduled — this documents the target.

**Layer 1 — Shared Infrastructure** (`/home/shared/projects/iitr-infrastructure/`):

| Service | Container | Port | Notes |
|---------|-----------|------|-------|
| Ollama | rag-staging-ollama | 11436:11434 | Single GPU instance, all projects |
| Langfuse | langfuse-web + 5 backing | 3003:3000 | Observability for all projects |
| OpenWebUI | open-webui | 3006:8080 | Single instance, per-project Models (see Serving Layer) |
| Pipelines | pipelines | 9099:9099 | Pipeline sidecar, hosts all project filters |

**Layer 2 — Per-Project** (`/home/shared/projects/{project}/`):

| Project | Owns | Connects to shared via |
|---------|------|----------------------|
| IITR-RAG-Navigation | Typesense, TEI | `iitr-shared-network` |
| IITR-RAG-V1 (Masterfragen) | Qdrant, pipeline filter (future) | `iitr-shared-network` |
| IITR-RAG-V2 (Court-Judgments) | Pipeline filter (future) | `iitr-shared-network` |

OpenWebUI + Pipelines are shared infrastructure. Currently deployed in the Navigation compose stack — migration to `iitr-infrastructure/` pending. All projects contribute pipeline filters mounted into the Pipelines sidecar.

#### Serving Layer

One OpenWebUI instance serves all three projects via the [Models feature](https://docs.openwebui.com/features/ai-knowledge/models). Each project is an OpenWebUI **Model** — a preset that wraps the base Ollama model (Qwen 3.5 9B) and binds project-specific configuration:

| Model (preset) | Filter | Knowledge | System Prompt | Access |
|----------------|--------|-----------|---------------|--------|
| **DS-Kit Navigation** | `typesense_rag_filter` | Anwenderleitfaden + Templates + FAQ | Navigation-specific generation prompt | Stellmacher, Kraska, Wilsch AI |
| **Court Judgments** | `court_judgments_filter` (future) | Court rulings corpus | Legal domain prompt | TBD |
| **Masterfragen** | `masterfragen_filter` (future) | 22 Masterfragen Q&A | GDPR Q&A prompt | TBD |

Users select which project to query by choosing the corresponding Model in the OpenWebUI chat interface. Each Model's bound Filter routes the query through the correct pipeline.

**Per-model access control:** Each Model has visibility settings — clients see only their project's Model(s). Stellmacher and Kraska see "DS-Kit Navigation" only. Internal users (Wilsch AI) see all Models for testing and development.

**Standard pattern:** All projects use OpenWebUI pipeline filters bound to Models. Masterfragen's legacy Flask proxy (`simple_app.py`) will be converted to a pipeline filter to standardize the serving pattern.

**Track coexistence:** Navigation has two tracks (Track A tree traversal, Track B vector RAG). Both are pipeline filters, but only one is active at a time — explicit mode switching via Pipelines valve configuration, not Ollama VRAM auto-eviction. This prevents GPU memory conflicts on the 20 GB RTX 4000.

#### Directory Structure & Cleanup

Canonical path: `/home/shared/projects/`. Per [Ops Manual](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services) convention, each project has its own directory with compose + Makefile.

**Retain:**

| Directory | Purpose |
|-----------|---------|
| `iitr-infrastructure/` | Shared services (Ollama, Langfuse) |
| `IITR-RAG-Navigation/` | Navigation (canonical name) |
| `IITR-RAG-V1/` | Masterfragen (Qdrant data) |
| `metamcp/` | MCP gateway |

**Remove:**

| Directory | Reason |
|-----------|--------|
| `typesense/` | Old standalone Typesense instance (retired). Navigation now runs Typesense within its own compose stack. |
| `langfuse/` | Absorbed into iitr-infrastructure |
| `openlit/` | No running containers |
| `signoz/` | No running containers |
| `mcp-proxy/` | No running containers |
| `IITR-RAG-V2-issue-1112/` | Worktree leftover |

**Resolve:** `IITR-RAG-V2/` — verify if redundant with IITR-RAG-Navigation, remove if duplicate. Kill duplicate Ollama (`iitr-rag-v1-ollama` from V1 compose) — single instance runs from iitr-infrastructure.

#### Vector Stores

No consolidation. Each project owns its data store:

- **Typesense** — Navigation (current, 17/29 with BGE-M3). Hybrid search (keyword + vector). In Navigation compose.
- **Chroma** — Navigation Track B experiment (2/29, not active). Remains in Navigation compose for future evaluation.
- **Qdrant** — Masterfragen (in V1 compose)

TEI (text-embeddings-inference) serves BAAI/bge-m3 (1024d) for Typesense vector embeddings. Lives in Navigation compose.

#### Caddy Routing

Single domain pointing to one OpenWebUI instance. Project separation happens via OpenWebUI Models, not Caddy routing:

| Domain | Target | Purpose |
|--------|--------|---------|
| `rag-staging.iitr-cloud.de` | localhost:3006 | OpenWebUI (all projects via Models) |
| `langfuse.iitr-cloud.de` | localhost:3003 | Langfuse observability |

Remove stale `qdrant-staging.iitr-cloud.de` entry (internal service, should not be publicly exposed). No per-project subdomains needed — users select their project via Model selection in the UI.

#### Deployment Contract

`docker compose up -d` from each directory brings up that layer:

1. `cd /home/shared/projects/iitr-infrastructure && docker compose up -d` → shared services
2. `cd /home/shared/projects/IITR-RAG-Navigation && docker compose up -d` → Navigation serving layer
3. Masterfragen/Court-Judgments compose files start their project-specific services

Order matters: infrastructure first, then project stacks (they depend on shared network + Ollama).

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
- [LLM Tree Search](https://docs.pageindex.ai/tutorials/tree-search/llm) — prompt template + expert knowledge injection pattern (required reading)
- [RAG for Technical Manuals](https://pageindex.ai/blog/technical-manuals) — validates PageIndex over vector RAG, 4 failure modes (required reading)
- [Hybrid Tree Search](https://docs.pageindex.ai/tutorials/tree-search/hybrid) — AlphaGo-inspired parallel retrieval (Track A reference only)

**Track B (Vector RAG):**
- [Typesense](https://typesense.org/) — hybrid search engine (keyword + vector), current production approach
- [BAAI/bge-m3](https://huggingface.co/BAAI/bge-m3) — MIT embedding model (1024d), served via HuggingFace TEI
- [Docling](https://ds4sd.github.io/docling/) — document chunking (HybridChunker preserves heading hierarchy)
- [OpenWebUI Models](https://docs.openwebui.com/features/ai-knowledge/models) — per-project preset system (Filter, Knowledge, Access Control binding)

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
- [2026-03-14 Grooming](https://app.fireflies.ai/view/01KKPCF74TMEAYTM0PQEBBD5HW) — PageIndex OSS limitation discovery, retrieval approach discussion

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
- Pass 6 review (mono repo + Models feature): /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/ba15606f-d3d0-41c8-a0b1-4739f10d7f35.jsonl
