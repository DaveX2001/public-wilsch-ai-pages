---
publish: true
---

# Meeting-Agenda: Kernel Optimization Investment Decision
[[uwi-retainer]]

## Meeting Goal

The $3,500 kernel optimization assessment (#713) could add a compelling data series to the FSO Seminar feasibility graph — showing that Power10 performance can be improved through R&D investment. The assessment needs 3-5 days, and the graph needs data by March 20. This meeting decides whether to commission the assessment and whether the timing targets the seminar.

1. **Investment decision** — whether to commission the $3,500 Qwen 8B profiling assessment on Power10
2. **Timing alignment** — whether the assessment targets the March 27 seminar or runs on its own timeline

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Kernel optimization as seminar story element — R&D investment visibility
⏱️ 10 min

The feasibility quadrant shows each infrastructure as a dot (speed × quality). Without #713, Power10 has one dot. With #713, an arrow shows the improvement trajectory — from current performance (13.9 tok/s) toward the optimization target (500 tok/s). This arrow tells the R&D story: Wilsch AI doesn't just deploy, it optimizes.

- Assessment scope: 3-5 days Qwen 8B profiling on Power10, $3,500
- Current baseline: 55 tok/s prompt eval, 13.9 tok/s generation (5 cores)
- Optimization target: 10x improvement through kernel-level tuning (VSX/MMA)
- Timeline: Assessment must complete by ~March 17 to process results for the March 20 graph deadline
- Dual value: Internal infrastructure improvement AND seminar presentation material

**To resolve:** Whether the $3,500 investment is commissioned now with seminar timing, commissioned on its own timeline, or deferred.

## Meeting Format

- **Type:** Decision
- **Duration:** ~10 min
- **Expectation:** Familiar with #713 scope and the feasibility quadrant concept (Design Doc Part 4)
- **Outcome:** Go/no-go on assessment, timing confirmed

## Related

- **Issue:** [#713 — IBM Power AI Inference Optimization](https://github.com/DaveX2001/deliverable-tracking/issues/713)
- **Design Doc:** [FSO Seminar Feasibility Graph](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/fso-seminar-feasibility-graph) — Part 5 (Undefined marker)
- **Parent:** [#1083 — FSO Seminar March 27](https://github.com/DaveX2001/deliverable-tracking/issues/1083)
