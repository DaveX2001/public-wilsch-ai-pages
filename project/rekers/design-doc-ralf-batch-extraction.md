---
publish: true
---

# RALF Batch Extraction — 14 Projects on Power 10 + DGX Spark
[[client-rekers]]

Design doc for running the REKERS extraction pipeline at scale across all 14 test projects on multiple hardware platforms via Ralph (autonomous Claude Code agent), producing the benchmark comparison dataset for the FSO seminar.

---

## Problem Statement

The extraction pipeline (extract.py) is proven on a single project — project 35764 on both Power 10 (#1132: 33 pg/hr) and DGX Spark (#1205: 672 pg/hr Ollama, up to 1696 pg/hr vLLM). Quality is validated: 9/9 criteria match across platforms.

The problem is scaling this to all 14 REKERS test projects across every hardware × engine × model combination, producing a benchmark comparison dataset that proves the hardware investment thesis at scale — not just a single-project anecdote.

This is NOT about building a new extraction pipeline. This IS about orchestrating the proven pipeline at scale via Ralph (autonomous Claude Code agent), managing data transfer across machines, and producing the FSO seminar comparison artifact.

**Preconditions:**
- extract.py proven on project 35764 — Power 10 (#1132) and DGX Spark (#1205, all 4 ACs passed)
- DGX Spark Phase 2 spikes passed — vLLM serves vision models (#1220, #1222, #1229 closed)
- 14 test projects delivered (565 L0 files + 1,322 L2 EML attachments)
- EML extraction completed for all 14 projects (`_extracted/` dirs)
- Measurement framework established: TTFT (pre-fill) + TpS (decode) per page
- FSO seminar deadline: March 27, 2026

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | 14-project benchmark comparison dataset across all hardware × engine × model combinations, proving the hardware investment thesis at scale |
| **Success** | (1) JSONL extraction results for all 14 projects on every config. (2) Quality spot-check on 3 projects matches #1205 baseline (9/9 criteria). (3) FSO comparison artifact produced with measured data — no TBD cells remaining for in-scope hardware. |
| **Done test** | "Can I fill every DGX Spark cell in the FSO presentation with measured 14-project data?" → If YES → done |

---

## Approach

### Part 1 — Pre-Work (before Ralph runs)

Three prerequisites must be satisfied before autonomous extraction begins.

**1. Data Transfer**

Only project 35764 currently exists on Power 10 and DGX Spark 1. The remaining 13 project directories (with `_extracted/` EML attachment dirs) must be transferred to every target machine via Tailscale SSH.

| Machine | Current state | Action |
|---------|--------------|--------|
| Power 10 (wph-ki-tst) | 1 project (35764) | Transfer 13 remaining |
| DGX Spark 1 | 1 project (35764) | Transfer 13 remaining |
| DGX Spark 2 | No data | Transfer all 14 |

**2. Manifest Generation**

Per-project JSON manifests (file inventory + hash dedup) exist only for project 35764. Generate manifests for all 14 projects using the same hash-dedup process — content hashing (md5), no filename-based suffix stripping. Output: page counts per project, enabling accurate runtime projection.

**3. Hardware Readiness**

| Machine | Status | Verification |
|---------|--------|-------------|
| Power 10 | ✅ Ready | Ollama running, extract.py proven |
| DGX Spark 1 | ✅ Ready | Ollama + vLLM proven, models pulled |
| DGX Spark 2 | **Undefined** | "Partially configured" — needs verification before allocation |

**Undefined:** DGX Spark 2 setup status — what's missing, can it run Ollama + vLLM? → Meeting agenda item.

### Part 2 — Benchmark Matrix

Ralph executes one configuration at a time — all 14 projects through one model on one hardware, then the next configuration. Each completed configuration produces a JSONL result set that Ralph verifies before proceeding.

**DGX Spark configurations (5):**

| # | Engine | Model | Measured pg/hr (1 project) |
|---|--------|-------|---------------------------|
| 1 | Ollama | qwen3-vl:8b | 672 |
| 2 | Ollama | qwen3.5:9b | 725 |
| 3 | Ollama | qwen3.5:4b | 948 |
| 4 | vLLM | Qwen3-VL-8B BF16 | 1,474 |
| 5 | vLLM | Qwen3-VL-30B-A3B NVFP4 | 1,696 |

**Power 10 configurations (3, Ollama only — no vLLM on ppc64le):**

| # | Engine | Model | Measured pg/hr (1 project) |
|---|--------|-------|---------------------------|
| 6 | Ollama | qwen3-vl:8b | 33 |
| 7 | Ollama | qwen3.5:9b | ~33* |
| 8 | Ollama | qwen3.5:4b | ~45* |

*Power 10 benchmarks only measured for qwen3-vl:8b (#1132). Other models estimated — actual measurement is part of this issue.*

**Total: 8 configurations × 14 projects = 112 extraction runs.**

With 2× DGX Spark (7 projects each): Spark configurations run in parallel, halving Spark wall time. Power 10 runs sequentially — one configuration at a time.

**Per-run output:** JSONL file with per-page entries: `{"file", "page", "response": {criteria}, "timing": {ttft_ms, tps, wall_s}}`. Same format as #1205.

### Part 3 — Runtime Projection

Projections use measured single-project baselines extrapolated linearly. Extraction cost per page is constant (no cross-page state, fresh context per call). Exact page counts per project will be known after manifest generation (Part 1).

**Estimate: 14 projects × ~275 pages avg = ~3,850 pages total.**

**DGX Spark (2× units, 7 projects each ≈ 1,925 pages per Spark):**

| Config | pg/hr | Per Spark | Calendar time (parallel) |
|--------|-------|-----------|--------------------------|
| Ollama vl:8b | 672 | 2.9h | 2.9h |
| Ollama 3.5:9b | 725 | 2.7h | 2.7h |
| Ollama 3.5:4b | 948 | 2.0h | 2.0h |
| vLLM 8B BF16 | 1,474 | 1.3h | 1.3h |
| vLLM 30B NVFP4 | 1,696 | 1.1h | 1.1h |
| **Spark total** | | | **~10h** (5 configs sequential, 2 Sparks parallel per config) |

**Power 10 (1× unit, all 14 projects = 3,850 pages, sequential):**

| Config | pg/hr | Wall time |
|--------|-------|-----------|
| Ollama vl:8b | 33 | ~117h (~5 days) |
| Ollama 3.5:9b | ~33 | ~117h (~5 days) |
| Ollama 3.5:4b | ~45 | ~86h (~3.5 days) |
| **P10 total** | | **~320h (~13 days)** |

**Total calendar time: ~13.5 days** — Power 10 is the bottleneck. DGX Spark finishes in under a day.

**Risks:**
- **Thermal throttling:** #1205 temps were 44–77°C across ~26 min runs. Sustained 3h runs per config on DGX Spark may throttle — monitor `nvidia-smi` temps, add cooldown between configs if needed.
- **KEEP_ALIVE deadlock:** Resolved in #1205 (set to 1h), but must be applied on all machines.
- **SSH drops:** #1205 found SSH drops under concurrent load on DGX Spark — use `tmux`/`screen` for long runs.

### Part 4 — Comparison Artifact

The benchmark produces two output tiers.

**Tier 1 — FSO Summary Table** (for Thomas/Ulrich):

|  | IBM Power 10 | 1× DGX Spark | 2× DGX Spark |
|--|-------------|-------------|--------------|
| 1 Projekt (~275 S.) | ~7h | ~21 min | ~10 min |
| 14 Projekte | ~4 Tage | ~5h | ~2.5h |
| Best config | Ollama 3.5:4b | vLLM 30B NVFP4 | vLLM 30B NVFP4 |

Values filled with measured data after benchmark completion. The existing single-project artifact (#1205, veloxforce/claude-user-configs) is the template — the 14-project version replaces projections with measured aggregates.

**Tier 2 — Detailed Benchmark Data** (per project × per config):

| Project | Pages | Config | Wall time | TTFT avg | TpS avg | Errors | GPU temp max |
|---------|-------|--------|-----------|----------|---------|--------|-------------|
| 35764 | 294 | Spark/Ollama/vl:8b | 26m | 323ms | 40.2 | 1 | 77°C |
| ... | ... | ... | ... | ... | ... | ... | ... |

112 rows total (8 configs × 14 projects). Source: JSONL extraction results aggregated by Ralph.

**Quality Verification:**

Spot-check 3 projects against gold-standard Kriteriennachweis (frontier model output from feasibility report). Threshold: ≤20% criteria degradation per project (consistent with #1205 AC2 methodology). Error rate threshold: <2% per configuration.

### Part 5 — Ausblick: Page Index (Step 2)

The extraction benchmark (Parts 1–4) is Step 1 of a two-step pipeline. Step 2 — similarity analysis and Kriteriennachweis generation — depends on Page Index infrastructure.

**What Page Index is:**

[PageIndex](https://github.com/VectifyAI/PageIndex) (VectifyAI, 18k stars) generates a hierarchical JSON tree from documents. Unlike vector RAG (chunk → embed → cosine similarity), PageIndex builds a navigable tree structure. Retrieval works by LLM tree traversal — walking level-by-level from root to leaf, selecting the most relevant branch at each level.

**How it applies to REKERS:**

After extraction produces per-page JSONL with criteria, Page Index would create a tree structure:

```
Root (14 projects)
├── 35764 — Werkhalle van Eckendonk
│   ├── Gebäudetyp: Hallengebäude
│   ├── Höhe: 14.35m
│   ├── Kran: ZLK 40t/10t
│   └── ...
├── 36185 — Lagergebäude GGM Gastro
│   └── ...
└── ...
```

Query: "Finde Projekte mit Kran + Dachbegrünung + Höhe >12m" → LLM traverses the tree, returns matching projects ranked by criteria overlap.

**IITR Lessons Learned:**

| Issue | What happened | Implication for REKERS |
|-------|--------------|----------------------|
| Retrieval is commercial-only | OSS PageIndex only generates trees — retrieval component locked behind paid product | Need alternative retriever (LlamaIndex TreeSelectLeafRetriever was planned for IITR) |
| Tree quality | Duplicate summaries, wrong language, no sub-chapter decomposition | Tree generation prompts must be domain-specific and validated |
| Baseline misleading | IITR's Typesense score came from FAQ CSV, not PDF retrieval | REKERS validation must test against extraction output, not supplementary data |

**Recommendation:** Spike before implementation. Time-boxed validation: generate a tree from 3 projects' extraction JSONL, attempt traversal queries, measure retrieval quality. Risks from IITR are domain-transferable.

**Undefined:** Which retriever replaces the commercial PageIndex retrieval? LlamaIndex TreeRetriever was abandoned in IITR — needs fresh evaluation.

---

## Source

**Design Docs:**
- [Feasibility Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report) — pipeline architecture, POC results, criteria extraction evidence
- [DGX Spark Infrastructure Platform](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-dgx-spark-infrastructure) — hardware, serving engines, phased deployment
- [FSO Presentation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/fso-praesentation-ki-performance) — target artifact for benchmark data

**Issues:**
- [#1207](https://github.com/DaveX2001/deliverable-tracking/issues/1207) — this issue
- [#1205](https://github.com/DaveX2001/deliverable-tracking/issues/1205) — Phase 1 DGX Spark Ollama benchmark (all 4 ACs passed)
- [#1132](https://github.com/DaveX2001/deliverable-tracking/issues/1132) — Power 10 extraction baseline
- [#1080](https://github.com/DaveX2001/deliverable-tracking/issues/1080) — DGX Spark infrastructure (design epic)
- [#629](https://github.com/DaveX2001/deliverable-tracking/issues/629) — KI Projektähnlichkeit POC (parent epic)
- [#1229](https://github.com/DaveX2001/deliverable-tracking/issues/1229) — vLLM vision extraction spike (passed)
- [#1232](https://github.com/DaveX2001/deliverable-tracking/issues/1232) — vLLM full extraction benchmark

**Artifacts:**
- [Comparison artifact (1 project)](https://github.com/veloxforce/claude-user-configs/blob/main/hippocampus/project/rekers/dgx-spark-benchmark-comparison.html)
- [Ralph — autonomous Claude Code agent](https://github.com/snarktank/ralph)
- [PageIndex — VectifyAI](https://github.com/VectifyAI/PageIndex)

**Session:** `ebae5bfb-5cea-46a4-baba-1c80e1afd001`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

