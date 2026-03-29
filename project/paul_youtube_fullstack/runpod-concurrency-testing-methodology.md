# RunPod Concurrency Testing Methodology
[[client-paul]]

---
publish: true
---

*First iteration - will be updated with real test results.*

## Purpose

Validate `concurrency_modifier` optimization to reduce worker costs while maintaining throughput.

## Key Concepts

| Element | What it controls | Docs |
|---------|------------------|------|
| `concurrency_modifier` | Requests per worker (handler-side) | [RunPod Handler Concurrency](https://docs.runpod.io/serverless/workers/handlers/handler-concurrency) |
| `scalerValue` | When to add workers (platform-side) | [Endpoint Configurations](https://docs.runpod.io/serverless/endpoints/endpoint-configurations) |
| Worker count | Total GPU instances | Console → min/max workers |

**Alignment rule:** `scalerValue` should equal `concurrency_modifier` to prevent premature worker spin-up.

## Test Setup

**Control variables:**
- Lock workers: `min_workers = max_workers = N`
- Same video/chunks across runs
- Match `scalerValue` to concurrency level

**Measure:**
- Wall time (from `metrics.json`)
- Worker count (fixed by config)

## Test Matrix

| Run | Concurrency | scalerValue | Expected |
|-----|-------------|-------------|----------|
| Baseline | c=1 | 1 | Reference |
| Test A | c=2 | 2 | ~1.6-1.8x faster |
| Test B | c=4 | 4 | Diminishing returns? |

## Success Metrics

```
Throughput multiplier = baseline_wall_time / test_wall_time
Contention cost = 1 - (multiplier / concurrency)
```

**Decision threshold:** If throughput ≥ 0.7 × concurrency → worth using.

## Example Output

```
| Run | Concurrency | Wall Time | Multiplier | Contention |
|-----|-------------|-----------|------------|------------|
| A   | c=1         | 102.4s    | 1.0x       | 0%         |
| B   | c=2         | 61.2s     | 1.67x      | 16%        |
| C   | c=4         | 38.7s     | 2.65x      | 34%        |
```

**Cost comparison (same ~100s wall time):**

| Config | Workers | Worker-seconds |
|--------|---------|----------------|
| c=1 | 5 | 510 |
| c=4 | 2 | 194 (62% savings) |

## Applying to Different Workloads

| Workload | VRAM constraint | Expected sweet spot |
|----------|-----------------|---------------------|
| TTS | Low (~2-4GB) | c=3 or c=4 |
| Image Gen | High (~12-24GB) | c=1 or c=2 |

## Related Docs

- [tts-optimization-learnings.md](tts-optimization-learnings.md)
- [pipeline-scalability-analysis-2025-12-28.md](pipeline-scalability-analysis-2025-12-28.md)
- [tts-pipeline-after-runpod.md](tts-pipeline-after-runpod.md)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

