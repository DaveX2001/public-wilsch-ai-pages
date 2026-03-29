---
publish: true
---

# FSO Seminar March 27 — Cross-Infrastructure Feasibility Graph
[[uwi-retainer]]

Internal design doc for the feasibility graph artifact presented at the FSO Seminar on March 27, 2026. Covers benchmarking methodology, infrastructure matrix, quality framework, and graph design.

---

## Problem Statement

The February FSO Seminar (23 attendees from Ulrich's customer network) was an awareness event — Firmenvorstellung, Talk-to-Your-Data Demo, KI-Bilder. March 27 needs to advance the conversation: concrete proof that local AI inference works at application level across customer infrastructure. The audience are business decision-makers evaluating whether their existing hardware can run AI locally.

The proof must be a feasibility artifact, not a live demo. A standardized AI extraction task (#952 POC) runs on multiple customer-relevant machines, producing measured speed and quality data. The output is a graph showing where each infrastructure lands on the feasibility spectrum — enabling attendees to evaluate deployment options for their own hardware.

The sales message: "We can deploy AI ON your machines." The graph is the proof artifact that backs that conversation.

**Preconditions:**
- #952 POC validated — Ollama vision inference works on IBM Power 10 (ppc64le), benchmarks measured (13.9 tok/s generation, 24.3 tok/s prompt eval at 72 dpi)
- #1079 extraction script exists — three-lane Python orchestration + Ollama API, designed for project 35764 (239 pages)
- February seminar completed — 23 attendees, awareness format, potential buyers in audience (AVO, Rekers, Ostermann)
- Mac Studio purchase approved — M3 Ultra 256GB being acquired this week
- REKERS test data available — project 35764 as standardized benchmark workload with gold standard (8/9 criteria found by frontier model)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Feasibility graph artifact showing local AI inference viability across multiple customer-relevant infrastructures — ready for FSO Seminar presentation on March 27 |
| **Success** | Graph enables the sales conversation: attendees see where their hardware lands on the feasibility spectrum and can evaluate deployment options. Readable by non-technical business decision-makers without explanation |
| **Done test** | "Can I write a meeting agenda with open design questions?" → If NO → design is complete |

---

## Approach

### Part 1: Infrastructure Matrix — Where Does It Run?

The feasibility graph requires benchmark data from multiple machines. Machines are categorized as guaranteed (data by March 20) or stretch goals (data if access and timing align).

**Guaranteed:**

| Machine | Architecture | RAM | Acceleration | Operator | Access |
|---------|-------------|-----|-------------|----------|--------|
| Power10 WPH | ppc64le, 5 cores × 8 SMT | 123 GB | CPU (VSX/MMA) | Marius | SSH (wph-ki-tst) |
| Mac Studio M1 Ultra | ARM64 (Apple Silicon) | 128 GB | Metal GPU | Marius | Physical (coworking) |
| Mac Studio M3 Ultra | ARM64 (Apple Silicon) | 256 GB | Metal GPU | Marius | Physical (purchasing this week) |

Power10 benchmarks already exist from #952 validation. Both Mac Studios are data points — M1 Ultra is available now, M3 Ultra adds the scaling story when it arrives. Two Apple Silicon generations on the same graph show how hardware evolution affects inference.

**Stretch goals:**

| Machine | Architecture | Status | Blocker |
|---------|-------------|--------|---------|
| Ostermann Power machine | ppc64le (specs TBD) | Access undefined | #866 — documentation phase, not benchmark-ready |
| IITR staging | Unknown | "Maybe even" — exploratory | Access and specs undefined |

Stretch goals add data points if they materialize, but the graph works without them. The guaranteed set (Power10 + two Mac Studios) already tells the cross-infrastructure story: IBM server vs Apple desktop, CPU vs GPU, two price points.

### Part 2: Benchmark Methodology — How Do We Test?

**Approach:** Best-effort per platform. Each machine runs the same AI task at its optimal configuration. The comparison measures what each infrastructure CAN deliver, not what it does under artificial constraints.

**Constants (same on every machine):**

| Constant | Value | Why fixed |
|----------|-------|-----------|
| Model family | Qwen 9B vision (Qwen3.5-9B or Qwen3-VL 8B) | Same AI "brain" — isolates hardware as the variable |
| Project data | REKERS 35764 — 239 PDF pages | Same workload — makes speed/quality comparable |
| Extraction prompt | 6-criterion schema (Gebäudetyp, Höhe, Kran, Dachlasten, Baustoff, Dachbegrünung) | Same questions — quality comparison is apples-to-apples |
| Gold standard | Kriteriennachweis 35764 — 8/9 criteria found by frontier model | Same evaluation target |

**Variables (tuned per machine):**

| Variable | Power10 | Mac Studio |
|----------|---------|-----------|
| DPI | 72–150 (start 72, escalate if quality drops) | Same range, optimal TBD |
| Threading | 20T × 2 parallel (measured optimal) | Metal GPU handles parallelism natively |
| Quantization | Q4_K_M (fits 123 GB RAM) | Q4_K_M or higher (256 GB allows larger quant) |
| Acceleration | CPU-only (VSX/MMA instructions) | Metal GPU (unified memory) |

**Script portability:** The extraction script (#1079) is Python orchestration calling Ollama's HTTP API. The Python layer (pdftoppm rendering, file handling, JSONL output) runs on any platform. The Ollama layer handles hardware differences transparently — same API, different backend (libggml-cpu-power10.so on Power10, Metal on Apple Silicon). No script modifications needed between machines.

**Why the comparison is fair:** Same model, same input data, same questions, same evaluation criteria. The only variable is the hardware. Speed differences reflect genuine infrastructure capability. Quality differences (if any) reflect quantization and runtime implementation — expected to be minor for the same model weights.

### Part 3: Quality Framework — How Do We Measure Correctness?

Each machine produces a JSONL file: one line per page, containing extracted criteria (or empty array for noise pages). Quality is measured by comparing these extractions against the gold standard — Kriteriennachweis 35764, where the frontier model found 8 of 9 criteria.

**Metrics:**

| Metric | Method | Threshold |
|--------|--------|-----------|
| Criteria hit rate | Count criteria found (local model) vs found (frontier), per project | ≤20% degradation acceptable (e.g., 7/9 where frontier found 8/9) |
| Value correctness | Spot-check extracted values against source PDFs | Manual review on sample — not automated |

**Feasibility quadrant placement:** Each machine's dot on the graph is positioned by:
- **X-axis (speed):** tokens per second (generation speed) or total processing time
- **Y-axis (quality):** criteria hit rate as percentage of gold standard

A machine that processes slowly but finds all criteria lands top-left. A machine that is fast but misses criteria lands bottom-right. The feasibility zone is top-right — fast AND accurate.

**Runtime as measured metric:** Processing time per project is reported, not gated. Ulrich determines production viability from measured results — there is no predetermined "too slow" threshold. The graph shows the data; the audience decides what's acceptable for their use case.

### Part 4: Graph Design — The Feasibility Quadrant

The primary artifact is a scatter plot with a feasibility threshold:

```
Quality (criteria hit rate %)
  100% ┤  ★ M3 Ultra    ★ Power10+713
       │           ● Power10 RAW
   80% ┤                    ● M1 Ultra
       │  ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─
       │     "FEASIBLE"    threshold
   60% ┤
       └──────────────────────────────────
         slow ◄──── Speed (tok/s) ────► fast
```

**Elements:**
- Each guaranteed infrastructure = one dot (Power10, M1 Ultra, M3 Ultra)
- Stretch goal machines (Ostermann, IITR) = additional dots if data available
- #713 kernel optimization = arrow from Power10-RAW to Power10-Optimized (conditional — see Part 5)
- Feasibility threshold line at ~80% quality — machines above this line produce usable results

**Reading the graph:** Top-right = fast and accurate (ideal). The audience sees at a glance which hardware options are viable and how they compare. The arrow (if #713 data exists) shows that performance can be improved further through optimization investment.

**Format:** Presentation slide — shown during the FSO Seminar to all attendees. Must be readable without explanation at seminar distance. Clean, minimal, labeled axes, no technical jargon on the graph itself.

### Part 5: Kernel Optimization — Conditional Second Data Series

#713 (IBM Power AI Inference Optimization Initiative) is a $3,500 profiling assessment targeting 10x speedup on Power10 (55 → 500 tok/s). If commissioned and completed before the seminar, it adds a second data point for Power10 on the feasibility quadrant.

**With #713:** The graph shows an arrow from Power10-RAW (current performance) to Power10-Optimized (after kernel tuning). This tells the R&D investment story — "we can make it faster" — and demonstrates that optimization is a capability Wilsch AI offers, not just deployment.

**Without #713:** Power10 has a single dot. The graph still works — the cross-infrastructure comparison (Power10 vs Mac Studios) is the primary story. The optimization arrow is a bonus, not a requirement.

**Design for both:** The graph template accommodates the arrow. If #713 data arrives, add the optimized dot and arrow. If not, omit — no placeholder or "coming soon" on the presentation slide.

**Undefined:** Whether the $3,500 assessment is commissioned and whether timing aligns with the March 20 deadline. → [Meeting Agenda: Kernel Optimization Investment](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-fso-seminar-kernel-optimization)

### Part 6: Presentation Context — Audience, Message, Usage

**Audience:** Ulrich's customer network — business decision-makers attending the FSO Seminar. Not engineers. The February seminar had 23 attendees including potential buyers (AVO, Rekers, Ostermann). March expects similar or larger attendance.

**Message:** "We can deploy AI ON your machines." Wilsch AI positions as the service provider that makes local AI inference work on customer infrastructure. The graph is the proof artifact — concrete, measurable, not a promise.

**Usage:** Shown during the seminar presentation to all attendees. The quadrant must tell its story at a glance — no narration required to understand the basic message. Marius provides verbal context during the presentation, but the graph stands alone as a visual.

**What the graph does NOT show:** Implementation details, model names, token counts, threading configurations. These are internal benchmarking concerns (Parts 2-3). The presentation artifact abstracts to: infrastructure name, speed indicator, quality indicator, feasibility zone.

---

## Source

**Transcripts:**
- [FSO-Seminar 6. Februar](https://app.fireflies.ai/view/01KGS021VPV39W9J7XTC5A6PM9) — previous seminar (23 attendees, awareness format)
- March 9 sync transcript — lost (Fireflies login conflict)

**Artifacts:**
- [Meeting Agenda: FSO-Seminar Scoping](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-fso-seminar-scoping-2026-03-09) — pre-meeting agenda with 3 discussion topics
- [Design Doc Part 7 — POC Validation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report) — REKERS local model feasibility (Power10 benchmarks, script architecture)

**Issues:**
- [#1083 — FSO Seminar March 27](https://github.com/DaveX2001/deliverable-tracking/issues/1083) — tracking issue
- [#952 — Ollama Vision POC on Power10](https://github.com/DaveX2001/deliverable-tracking/issues/952) — infrastructure validation
- [#1079 — POC extraction script](https://github.com/DaveX2001/deliverable-tracking/issues/1079) — three-lane benchmark script
- [#713 — Kernel Optimization](https://github.com/DaveX2001/deliverable-tracking/issues/713) — conditional optimization series
- [#929 — Mac Studio purchase](https://github.com/DaveX2001/deliverable-tracking/issues/929) — hardware acquisition
- [#650 — UWI Retainer](https://github.com/DaveX2001/deliverable-tracking/issues/650) — parent epic

**Session:**
- /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-02-UWI--deliverable/61b87280-92ac-488b-8aa6-526eeba5ea2b.jsonl (extraction pass 1 — 7 uncertainties resolved)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

