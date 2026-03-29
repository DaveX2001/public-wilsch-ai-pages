# TTS Optimization Learnings

[[client-paul]]

## 1. Success Criteria

- **Latency target:** End-to-end video generation < 10 minutes
- **Quality constraint:** Transcript quality maintained (measured by external party)
- **Internal metric:** Concatenated audio quality (no audible seams between chunks)

## 2. Evaluation Reference

| File | Path | Purpose |
|------|------|---------|
| Quality test | `/tests/thai-scammer-eval.txt` | Ground truth exists on YouTube for comparison |
| Chapter 1 test | `/Users/verdant/Desktop/chapter_1.txt` | 16,158 chars benchmark file |

## 3. Context

- **Goal:** Speed up audiobook TTS generation
- **Current system:** ChatterBox on RunPod serverless
- **Investigation date:** 2025-11-22

## 4. F5-TTS Investigation (Failed)

### What we validated

| Claim | Result |
|-------|--------|
| Native batch API | Does not exist (research confirmed) |
| 30s generation limit | Confirmed: ref + generated combined (~19s max with 11s ref) |
| Chunk optimization | Tested 850 chars, still produced 40-48s audio per chunk |
| Speed vs ChatterBox | F5 is SLOWER (~10 min vs ~5.5 min for Chapter 1) |

### Why F5-TTS failed for long-form

- "16x faster" only applies to single short inferences (< 30s output)
- For audiobooks requiring many chunks, inference time scales with text length
- No batch processing means no parallel speedup within single worker

## 5. ChatterBox Baseline (Sequential)

| Metric | Value |
|--------|-------|
| Chapter 1 processing | ~5.5 min |
| Audio output | 12.9 min |
| Chunks | 27 at 600 chars each |
| API mode | Sequential calls |
| Stability | Stable, no timeout issues |

## 6. Parallel TTS Results

### Test Results

| Test | Chunks | Sequential Est. | Parallel (3 workers) | Speedup |
|------|--------|-----------------|----------------------|---------|
| Chapter 1 (16K chars) | 29 | ~5.5 min | 3.0 min | 1.8x |
| Thai Full (40K chars) | 70 | ~10 min | 5.5 min | 2x |

### Scaling Projections (70 chunks)

| Workers | Estimated Time | Speedup |
|---------|----------------|---------|
| 1 | ~10 min | baseline |
| 3 | ~5.5 min | ~2x |
| 5 | ~3-4 min | ~3x |
| 10 | ~1-1.5 min | ~6-7x |

**Note:** Cost is the same regardless of worker count - more workers = faster completion, not more compute.

## 7. GPU Benchmark: 4090 with 5 Workers

### Test Details

- **Transcript:** Thai Scammer eval
- **Characters:** 40,029
- **Chunks:** 70

### Results

- **Workers:** 5
- **Time:** 2.8 min (170.81s)
- **Cost/sec:** $0.00031
- **Total cost:** ~$0.05

### Comparison (70 chunks, Thai Scammer)

| GPU | Workers | Time | Cost |
|-----|---------|------|------|
| L4 | 3 | 5.5 min | $0.06 |
| 3090 | 3 | 8.1 min | $0.09 |
| H200 | 3 | 4.7 min | $0.33 |
| **4090** | **5** | **2.8 min** | **$0.05** |

**Conclusion:** 4090 with 5 workers is fastest AND cheapest.

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

