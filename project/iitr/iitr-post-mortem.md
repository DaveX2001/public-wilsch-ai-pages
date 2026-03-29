---
publish: true
---

# IITR DS-Kit Navigation — Post-Mortem Analysis
[[iitr-post-mortem]]

Post-mortem of the IITR DS-Kit Navigation project (March 9–17, 2026). Covers root cause chain, process failures, and corrective actions derived from the grooming transcript, issue comment history, and CCI observations.

---

## Problem Statement

The IITR DS-Kit Navigation project decomposed 6 issues (#1111–#1116) from a design doc that passed two SA reviews — then discovered 3 days into implementation that the core technical assumption (PageIndex OSS retrieval) was commercially licensed, not open-source. All 6 approved issues became partially or fully invalid. ~3 days of design, review, and implementation work was spent on a foundation that didn't exist.

This is NOT a failure of individual process components. The JA extraction passes produced a thorough design doc. The SA reviews caught real issues and improved the doc. The decomposition followed the established pipeline. Each step worked correctly in isolation. The gap was the absence of a validation step between "design approved" and "fully decomposed" — no mechanism to confirm that the core approach actually works before building on it.

**Preconditions:**
- JA→SA→Decompose pipeline had no spike gate for technical assumptions
- Two SA reviews created high confidence in the design doc
- Workflow default naturally produces full decomposition (all issues at once)
- March 17 client meeting created time pressure to show progress

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Shared understanding of why the IITR project went off-track and what to change |
| **Success** | Root cause chain documented, process failures identified, corrective actions agreed between SA and Dev |
| **Done test** | All corrective actions either implemented in the workflow or tracked as CCI issues — no remaining "we should do X" without a home |

---

## Approach

### Part 1 — Root Cause Chain

The failure cascaded through four stages:

**Stage 1: Unvalidated adoption (March 9–11).** The JA discovered PageIndex during research extraction passes. It looked promising from the paper. The approach entered the design doc as the core retrieval mechanism and survived two SA reviews — both focused on doc quality (too implementation-specific, missing Langfuse, scoring redesign), not on whether PageIndex retrieval actually works in OSS mode. No spike was run. No spikes existed in the workflow at this point.

**Stage 2: Full decomposition (March 11).** With the design doc approved, 6 issues (#1111–#1116) were decomposed in a single session. Pipeline: #1111→#1112→#1113→#1114→#1115/#1116 (parallel). All were designed with ACs, reviewed, and approved. The SA praised the decomposition as "done very well" and proposed it as the standard operator workflow.

**Stage 3: Foundation collapse (March 14).** Three days into implementation, David discovered PageIndex OSS ships tree generation only — retrieval (MCTS, level-by-level traversal) is commercial API. The core assumption was invalid. Separately, code had been committed to IITR-RAG-Court-Judgments (wrong repo) instead of IITR-RAG-Navigation across 5+ sessions — three similar IITR repos on the server confused both human and AI. David caught the repo error manually.

**Stage 4: Fast pivot, slow detection (March 14–15).** David escalated the blocking chain on #1093. Marius responded within ~24h with a two-track decision: Track A (LlamaIndex TreeRetriever) and Track B (vector search with Typesense). The SA decision was fast — the real cost was the 3 days building on a broken foundation before the problem surfaced.

### Part 2 — Process Failures

Five breakdowns compounded the root cause:

**Over-decomposition before validation.** The JA→SA→Decompose workflow naturally produces full breakdowns, and two SA reviews created high confidence. Six issues were created at once. The corrective: decompose only up to the trunk (2–3 issues), validate via spike, then decompose further. Don't create 6 approved issues on unvalidated assumptions — even when the design doc looks solid.

**Issue boundary collapse under pressure.** The dependency ordering was correct (#1113 Test Harness before #1114 Ingestion — validate the harness works, then feed data). But under time pressure, work migrated across issues: #1114's scope got absorbed into #1152 (Track B). Clean issue boundaries dissolved when the plan changed mid-execution.

**Stale issues accumulating.** #1115 and #1116 were approved with PageIndex-era ACs (e.g., "use page index native path" — which doesn't work). They sat on the board for days, approved but invalid. The disposition: close stale issues and recreate fresh when the next trunk decomposition reaches their scope. Trunk-first decomposition prevents this pattern from recurring.

**New issues vs. AC redesign.** When #1112 became fundamentally broken, David created #1152 instead of redesigning the ACs — pragmatic under the circumstances. The heuristic: minor pivots (ACs wrong but scope intact) → redesign ACs in place. Fundamental pivots (scope broken, comments polluted) → close and recreate. The #1112→#1152 transition worked well.

**Config knowledge dying between sessions.** The test harness failed 3 times across sessions 167–170: stale API key (→401), wrong model name (`qwen3.5-32k:latest` doesn't exist in Ollama), and wrong API parameter (`body["think"]=False` is Ollama-only, OpenRouter needs `reasoning_effort: "none"`). Each failure required rediscovery. Once stable config was reached, it was documented in the #1152 comment. The fix: persist working config (CLAUDE.md, .env, or compose) during iterative debugging, not just after stability.

### Part 3 — Corrective Actions

**Spike primitive in the JA workflow.** The JA is responsible for running spikes to validate technical assumptions before SA review. The spike is not a mandatory gate — it's a tool for when uncertainty exists. Clear, low-ambiguity decompositions don't need spikes. Spikes scale with context: a quick "can PageIndex do OSS retrieval?" takes hours; a full "deploy LibreChat so Dev Lead can test it" (#852) is a one-day issue. The deliverable is knowledge or a running environment — not screenshots. Dev Lead witnesses the spike before the design doc advances to SA review.

**Trunk-first decomposition.** Decompose only up to the first proof point (2–3 issues max). The proof point is context-dependent — it could be a technical assumption, a data dependency, or a client decision. The decomposer (JA) identifies the biggest unknown in the pipeline and draws the trunk boundary there. Validate via spike. If the spike passes, decompose further. If it fails, the blast radius is 2–3 issues, not 6.

**Stale issue disposition.** When a pivot invalidates issues: minor pivot (ACs wrong, scope intact) → redesign ACs in place. Fundamental pivot (scope broken, comments polluted) → close and create fresh. Pattern: #1112→#1152 (worked well). Don't accumulate approved-but-invalid issues on the board.

**Board cleanup (IITR-specific).** Close #1115, #1116 (stale PageIndex-era ACs — recreate when next trunk decomposition reaches data ingestion). Close #1113 (Test Harness V1, superseded by #1125 V2). Consider closing #1093 (all extraction passes complete, hub purpose served). Keep #1164, #1153, #1152, #1125, #1100, #1039, #1038.

**Mono-repo for IITR.** Three similar repos on IITR-STAGING (RAG-Navigation, RAG-Court-Judgments, Masterfragen) confused both AI and human across 5+ sessions. The design doc Pass 6 already targets mono-repo architecture with shared CLAUDE.md and routing. This prevents the wrong-repo failure mode.

**Persist working config.** When staging config works, write it to CLAUDE.md, .env, or compose immediately — not just after reaching stability. Config knowledge that dies between sessions costs rediscovery time.

---

## Source

- **Transcript:** [Grooming 2026-03-17](https://app.fireflies.ai/view/01KKXKGJB20Q1K0XDHXMH77BVM) — first 20 min post-mortem discussion
- **Design Doc Hub:** [#1093](https://github.com/DaveX2001/deliverable-tracking/issues/1093) — 78 comments, full extraction pass history
- **Issue Chain:** #1111, #1112, #1113, #1114, #1115, #1116 (original decomposition) → #1152, #1153 (post-pivot)
- **CCI Observations:** [#600](https://github.com/DaveX2001/claude-code-improvements/issues/600) (SE Epic), [#635](https://github.com/DaveX2001/claude-code-improvements/issues/635) (Operator Epic) — 6 IITR-related observations
- **Spike Reference:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852) — Archibus spike format definition
- **Post-Mortem Issue:** [#1169](https://github.com/DaveX2001/deliverable-tracking/issues/1169)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

