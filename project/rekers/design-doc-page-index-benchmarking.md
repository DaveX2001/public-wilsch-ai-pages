---
title: "Page Index Benchmarking — Tree Gen + Retrieval on Power 10 & DGX Spark"
description: "Benchmarking rubric for Page Index tree-based extraction vs script-based extraction on REKERS project data"
issue: "#1137"
parent: "#629 — KI Projektähnlichkeit POC"
status: "Draft"
publish: true
---

# Page Index Benchmarking — Tree Gen + Retrieval on Power 10 & DGX Spark

## Part 1 — Context & Problem Statement

### What Is Page Index?

Page Index (VectifyAI, MIT license, 18k+ GitHub stars) is a vectorless RAG framework. Instead of chunking documents and embedding them into a vector database, it builds a **hierarchical tree index** from a document — essentially an AI-generated table of contents with summaries at each node.

The tree enables two operations:
- **Tree generation:** Read a document → produce a JSON tree with section titles, page ranges, and summaries
- **Tree search (retrieval):** Given a query, navigate the tree to find relevant sections without reading every page

### What We Built

Page Index was deployed on IITR-STAGING (#1112) as part of the DS-Kit Navigation rebuild:
- Full stack: Page Index + Ollama (Qwen 3.5 9B) + OpenWebUI + Langfuse
- Pipeline filter rewritten around Page Index tree search
- Ingestion script (`ingest.sh`) created for Anwenderleitfaden PDF

### What Failed

**Two critical failures:**

1. **JSON quality with small models.** Page Index was designed for GPT-4-quality JSON output. Qwen 3.5 9B (and qwen3:14b) produced malformed JSON — missing commas, broken delimiters, missing keys. Error: `KeyError: 'table_of_contents'` at `page_index.py:296`. Four defensive patches were applied; ingestion eventually completed but quality remained uncertain.

2. **OSS retrieval gap.** Discovered during #1112 AC verification (2026-03-14): the OSS library ships **tree generation only**. The "retrieval" shown in the official cookbook dumps the entire tree (~30KB, ~10K tokens) into a single LLM prompt — not real traversal. Level-by-level traversal, MCTS, and hybrid search exist only in the commercial API ($30-100/month).

**Test result:** First real test with Page Index scored **1/29 PASS (3.4%)** on the IITR test rubric. The pipeline produced no usable answers because there was no real retrieval mechanism.

### What Was Tried After

Two tracks were created as alternatives:
- **Track A (#1153):** LlamaIndex `TreeSelectLeafRetriever` as OSS retrieval replacement. **Never built, never tested** — deprioritized and abandoned.
- **Track B (#1152):** Vector RAG with Chonkie SemanticChunker + Chroma. Scored **2/29 (6.9%)**. Reverting to Typesense + BGE-M3 matched the old 17/29 baseline — no improvement.

Both tracks were superseded by a clean architecture restart (#1182). **Everything Page Index was deleted.**

### Why This Issue Exists

The REKERS project needs to benchmark tree-based extraction against the proven script-based extraction. The script (`extract.py`) reads every page sequentially and extracts 9 similarity criteria. It works — 9/9 quality, measured at 1,319 pages/hour on DGX Spark (#1232).

The question: can tree-based approach (navigate to relevant pages, extract criteria) match the script's quality while being faster? And can the DGX Spark's better models solve both the JSON quality problem and the retrieval gap?

### References

| Issue | Title | State | Relevance |
|-------|-------|-------|-----------|
| #1112 | Deploy PageIndex + Langfuse on IITR-STAGING | Closed | Infrastructure built, failures documented |
| #1093 | SA Review — OSS limitation discovered | Closed | Retrieval gap identified |
| #1153 | Track A — LlamaIndex TreeRetriever | Closed | Never implemented |
| #1152 | Track B — Vector RAG | Closed | Scored 2/29 with Chroma |
| #1132 | Power 10 extraction baseline | Closed | 9/9 quality, 33 pg/hr |
| #1205 | DGX Spark Ollama benchmark | Closed | 725 pg/hr baseline |
| #1232 | DGX Spark vLLM benchmark | Closed | 1,319 pg/hr sequential, 6,853 parallel |

## Part 2 — Self-Hosted Retrieval Solutions

Three approaches exist for solving the Page Index retrieval gap without a commercial API. Each uses the OSS tree generation but implements traversal differently.

### Option A: Custom LLM Tree Search

**How it works:** Use the Page Index OSS library to generate the tree JSON, then implement the LLM tree search prompt directly. The prompt template is available in the [official cookbook](https://github.com/VectifyAI/PageIndex/blob/main/cookbook/pageindex_RAG_simple.ipynb):

1. Generate tree from document (`page_index_main()`)
2. Strip `text` field from tree (keep structure + summaries only — reduces tokens by 80-95%)
3. Send query + stripped tree to LLM: "Which nodes contain the answer? Return `{thinking, node_list}`"
4. Map returned node IDs to full text via `create_node_mapping()`
5. Feed extracted text to LLM for criteria extraction

**Community implementations:**
- [b-d055/PageIndex fork](https://github.com/b-d055/PageIndex) — adds `local_rag.py` for fully local retrieval
- [funnamer/pageIndex-rag-chat](https://github.com/funnamer/pageIndex-rag-chat) — FastAPI + ReAct Web UI, works with any OpenAI-compatible API (including local vLLM/Ollama)

**Effort:** ~100-150 lines of Python. Simplest path.

**Strengths:**
- No external dependencies beyond Page Index OSS + an LLM
- Proven community implementations to reference
- Uses the exact same prompt pattern as the commercial API (minus MCTS optimization)
- Works with any OpenAI-compatible LLM server (vLLM, Ollama)

**Weaknesses:**
- Single-pass node selection — no iterative refinement
- Quality depends entirely on the LLM's ability to reason about tree structure
- No embedding-based fallback if LLM reasoning fails

**Undefined:** Does Qwen3-VL-30B produce valid JSON for tree generation? This is the core hypothesis — untested.

---

### Option B: LlamaIndex TreeSelectLeafRetriever Adapter

**How it works:** Use Page Index OSS for tree generation, then convert the tree JSON into LlamaIndex's `IndexGraph` format. LlamaIndex's `TreeSelectLeafRetriever` handles the traversal.

1. Generate tree from document (Page Index OSS)
2. Walk the PageIndex nested JSON recursively
3. Create LlamaIndex `TextNode` objects for each node (summary as text, page ranges as metadata)
4. Build `IndexGraph` with parent→children relationships
5. Re-materialize leaf content from the source document
6. Instantiate `TreeIndex` from pre-built `IndexGraph`
7. Query via `TreeSelectLeafRetriever` (LLM-guided top-down traversal)

**How LlamaIndex traversal works:**
- Starting at root nodes, the LLM is prompted with the query and children text at each level
- LLM selects the most relevant child node(s) (controlled by `child_branch_factor`)
- Repeats recursively until leaf nodes are reached
- Supports both LLM-guided (`SELECT_LEAF`) and embedding-based (`SELECT_LEAF_EMBEDDING`) modes

**Effort:** ~200-400 lines of adapter code. Medium complexity.

**Strengths:**
- Mature retrieval framework (LlamaIndex core)
- Level-by-level traversal — more precise than single-pass node selection
- Embedding fallback mode available (`SELECT_LEAF_EMBEDDING`)
- Well-documented API with evaluation tooling

**Weaknesses:**
- Format mismatch: PageIndex JSON → LlamaIndex IndexGraph requires non-trivial mapping
- Must re-materialize leaf content from source (PageIndex stores page ranges, not full text at leaves)
- LlamaIndex's tree is built bottom-up (adjacent-chunk summarization) while PageIndex builds top-down (TOC-based) — conceptual impedance
- No existing adapter; would be first implementation

**Undefined:** Does level-by-level traversal outperform single-pass node selection for REKERS document structure?

---

### Option C: RAPTOR (Recursive Abstractive Processing for Tree-Organized Retrieval)

**How it works:** RAPTOR (Stanford, ICLR 2024) builds a hierarchical tree through semantic clustering, then retrieves via collapsed tree search. This is a fundamentally different architecture — it uses embeddings, not Page Index.

1. Split document into ~100-token chunks (sentence-boundary-preserving)
2. Embed with SBERT (`multi-qa-mpnet-base-cos-v1`)
3. Reduce dimensions with UMAP
4. Cluster with Gaussian Mixture Models (soft clustering — nodes can belong to multiple clusters)
5. Summarize each cluster with LLM (72% compression ratio)
6. Re-embed summaries and repeat until no further clustering is feasible
7. **Collapsed tree retrieval:** Flatten entire tree, cosine similarity across all nodes, retrieve top-k

**Key finding:** RAPTOR's recommended retrieval mode ("collapsed tree") is better than tree traversal because traversal forces early commitment at the root level. Collapsed tree lets the retriever match questions to the most relevant granularity without path dependency.

**Benchmarks (peer-reviewed, ICLR 2024):**
- QuALITY (multi-step): RAPTOR+GPT-4 = **82.6%** vs prior SOTA 62.3% (+20% absolute)
- NarrativeQA: RAPTOR+UnifiedQA = **19.1 METEOR** vs prior SOTA 10.6

**Effort:** Moderate. [GitHub implementation](https://github.com/parthsarthi03/raptor) exists. Integration into RAGFlow also available.

**Strengths:**
- Best-benchmarked tree-based RAG approach (peer-reviewed)
- Catches cross-section relationships that adjacency-based approaches miss (soft clustering)
- Collapsed tree retrieval outperforms traversal
- No dependency on Page Index at all — standalone

**Weaknesses:**
- Requires embeddings (not vectorless — needs SBERT or equivalent)
- Requires UMAP + GMM clustering infrastructure
- Not directly comparable to Page Index tree gen (different tree structure)
- May be over-engineered for REKERS's structured offer documents (9 known criteria)

**Undefined:** Is RAPTOR's semantic clustering advantage relevant when the criteria are well-defined and always appear in specific document sections?

---

### Comparison Matrix

| Dimension | Option A: Custom LLM Search | Option B: LlamaIndex Adapter | Option C: RAPTOR |
|-----------|----------------------------|------------------------------|------------------|
| **Effort** | ~100-150 lines | ~200-400 lines | Moderate (existing impl) |
| **Dependencies** | Page Index OSS + LLM | Page Index OSS + LlamaIndex + LLM | SBERT + UMAP + GMM + LLM |
| **Embeddings needed** | No | Optional (fallback mode) | Yes (required) |
| **Traversal method** | Single-pass LLM reasoning | Level-by-level LLM-guided | Collapsed cosine similarity |
| **Benchmarks** | 98.7% FinanceBench (commercial) | No published numbers | 82.6% QuALITY (ICLR 2024) |
| **Page Index reuse** | Full (tree gen + search) | Partial (tree gen only) | None |
| **Risk** | JSON quality with local models | Adapter complexity | Over-engineering |
| **Best for** | Simple, fast validation | Production-grade retrieval | Complex multi-hop queries |

## Part 3 — Benchmarking Rubric

This section defines the concrete spec David executes. Every parameter is specified — no ambiguity.

### Test Data

**Project:** 35764 (same data used in #1079, #1132, #1205, #1232)
**Document:** REKERS offer document (~240-294 pages)
**Ground truth:** 9 similarity criteria extracted by `extract.py` with 9/9 quality score

### Hardware Matrix

| Platform | Config | Serving | Notes |
|----------|--------|---------|-------|
| **Power 10** | IBM LPAR, 5-10 cores | Ollama | Thomas may bump to 8-10 cores |
| **DGX Spark** | Single unit, SM121 GPU | Ollama + vLLM | Proven 80.7 t/s decode with vLLM |

### Model Matrix

Test multiple models to validate the hypothesis that larger models solve the JSON quality problem:

| Model | Params | Quant | Server | Hypothesis |
|-------|--------|-------|--------|------------|
| qwen3.5:9b | 9B | Q4_K_M | Ollama | Baseline — known to fail JSON in IITR |
| qwen3-vl:8b | 8B | Q4_K_M | Ollama | Vision model — used in extraction benchmarks |
| qwen3:14b | 14B | Q4_K_M | Ollama | Slightly better — still failed JSON in IITR |
| Qwen3-VL-30B-A3B NVFP4 | 30B (3B active) | NVFP4 | vLLM | Best candidate — 80.7 t/s, 9/9 extraction quality |

### Metrics

**Phase 1 — Tree Generation:**

| Metric | What it measures | How to measure |
|--------|-----------------|----------------|
| **JSON validity** | Tree gen produces parseable JSON without errors | Binary pass/fail — does `json.loads()` succeed? |
| **Structure depth** | Tree correctly identifies document sections | Manual inspection: does the tree TOC match the actual document structure? |
| **Node coverage** | All document pages are represented in the tree | Check: `max(end_index)` covers all pages |
| **Generation time** | Time to build the tree | Wall clock: start to tree JSON written |
| **Token cost** | LLM tokens consumed during tree generation | Count from LLM API response |

**Phase 2 — Retrieval (per approach):**

| Metric | What it measures | How to measure |
|--------|-----------------|----------------|
| **Criteria hit rate** | How many of the 9 criteria are found via tree retrieval | Compare retrieved criteria against `extract.py` ground truth (X/9) |
| **Precision** | Retrieved nodes actually contain relevant criteria | Manual check: of the nodes returned, how many contain criteria? |
| **Retrieval time** | Time from query to extracted text | Wall clock per criterion query |
| **Total extraction time** | Time to extract all 9 criteria via tree approach | Sum of tree gen + 9 retrieval queries |

**Phase 3 — Comparison vs Script:**

| Metric | Tree Approach | Script Approach (baseline) |
|--------|--------------|---------------------------|
| **Quality** | X/9 criteria found | 9/9 (proven) |
| **Speed (P10)** | Tree gen + retrieval time | 33 pg/hr (#1132) |
| **Speed (DGX Ollama)** | Tree gen + retrieval time | 725 pg/hr (#1205) |
| **Speed (DGX vLLM)** | Tree gen + retrieval time | 1,319 pg/hr (#1232) |
| **Parallel scaling** | **Undefined** | 6,853 pg/hr at --parallel 8 (#1232) |

### Success Criteria

The benchmark answers three questions:

1. **Does tree gen work with local models?** At least one model produces valid, structurally correct tree JSON without parse errors.
2. **Does tree retrieval find the criteria?** At least one approach + model combination achieves ≥8/9 criteria hit rate (comparable to script's 9/9).
3. **Is it faster than the script?** Total tree gen + retrieval time < total script extraction time on the same hardware.

**Minimum viable outcome:** Question 1 answered. Even if retrieval doesn't work, knowing which models produce valid trees is valuable for future iterations.

### Execution Sequence

```
Step 1: Tree Generation (all models × both hardware)
  → For each model on each platform:
     - Run page_index_main() on project 35764 document
     - Record: JSON validity, structure depth, generation time
     - If JSON fails: document the error, move to next model
  → Output: model × hardware matrix of tree gen results

Step 2: Retrieval (passing models × 3 approaches)
  → For each model that passed tree gen:
     - Option A: Custom LLM tree search (query each criterion)
     - Option B: LlamaIndex adapter (if implemented)
     - Option C: RAPTOR (if implemented)
     - Record: criteria hit rate, precision, retrieval time
  → Output: approach × model matrix of retrieval results

Step 3: Comparison
  → Compare best tree approach result against script baselines
  → Document: quality delta, speed delta, trade-offs
  → Output: recommendation for SA decision
```

### What This Benchmark Does NOT Cover

- Production deployment architecture
- Multi-project scaling (all 14 REKERS projects)
- Integration with the similarity matching pipeline
- Cost analysis for ongoing operation

These are follow-up work after the benchmark validates (or invalidates) the tree-based approach.

## Part 4 — Appendix

### Page Index Tree JSON Schema

The OSS library produces a `_structure.json` file with this node schema:

```json
{
  "node_id": "0006",          // Zero-padded 4-digit ID, depth-first order
  "title": "2. Methodology",  // Section title from TOC
  "start_index": 21,          // First page (1-based)
  "end_index": 22,            // Last page (inclusive)
  "summary": "This section...",// LLM-generated summary (leaf nodes)
  "text": "Full text...",      // Full section text (stripped before search)
  "nodes": [...]               // Child nodes (recursive, absent on leaves)
}
```

### LLM Tree Search Prompt (from OSS cookbook)

```
You are given a question and a tree structure of a document.
Each node contains a node id, node title, and a corresponding summary.
Your task is to find all nodes that are likely to contain the answer to the question.

Question: {query}

Document tree structure:
{tree_without_text}

Please reply in the following JSON format:
{
    "thinking": "<Your thinking process>",
    "node_list": ["node_id_1", "node_id_2", ..., "node_id_n"]
}

Directly return the final JSON structure. Do not output anything else.
```

### Key Community Resources

| Resource | URL | What it provides |
|----------|-----|-----------------|
| Page Index OSS | [github.com/VectifyAI/PageIndex](https://github.com/VectifyAI/PageIndex) | Tree generation library |
| b-d055 fork (local RAG) | [github.com/b-d055/PageIndex](https://github.com/b-d055/PageIndex) | `local_rag.py` — self-hosted retrieval |
| funnamer RAG chat | [github.com/funnamer/pageIndex-rag-chat](https://github.com/funnamer/pageIndex-rag-chat) | FastAPI + ReAct Web UI |
| pageindex-ts | [github.com/piyush-hack/pageindex-ts](https://github.com/piyush-hack/pageindex-ts) | TypeScript reimplementation with retrieval |
| RAPTOR | [github.com/parthsarthi03/raptor](https://github.com/parthsarthi03/raptor) | Stanford ICLR 2024 tree RAG |
| LlamaIndex TreeIndex | [docs.llamaindex.ai](https://docs.llamaindex.ai/en/stable/api_reference/indices/tree/) | TreeSelectLeafRetriever API |
| MCTS-RAG (Yale) | [github.com/yale-nlp/MCTS-RAG](https://github.com/yale-nlp/MCTS-RAG) | Academic MCTS + retrieval |
| DEV.to Bedrock pattern | [dev.to article](https://dev.to/aws-builders/vectorless-rag-with-aws-bedrock-and-pageindex-cl8) | DIY retrieval bridge tutorial |
| PageIndex docs | [docs.pageindex.ai](https://docs.pageindex.ai/tutorials/tree-search/llm) | Official tree search tutorial |

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

