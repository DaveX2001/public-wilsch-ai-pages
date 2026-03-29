# VideoGen Scaling Spike - Root Cause Analysis
[[client-paul]]

---
publish: true
date: 2025-12-19
updated: 2025-12-29
issue: 329
---

## The Root Cause

FFmpeg's `zoompan` filter rebuilds the entire scaling pipeline **per frame**:

```c
// Per frame in vf_zoompan.c:
s->sws = sws_alloc_context();        // allocate
sws_init_context(s->sws, ...);       // compute filter coefficients (EXPENSIVE)
sws_scale(s->sws, ...);              // actual scaling (fast)
sws_freeContext(s->sws);             // free everything
```

The 86% CPU isn't scaling pixels - it's recomputing bicubic filter coefficients 30 times per second.

## Key Findings

| Metric | Value | Implication |
|--------|-------|-------------|
| GPU utilization | 2% | Starved for frames, not the bottleneck |
| CPU utilization | 158% | Bottleneck |
| zoompan CPU share | 86% | Per-frame SwsContext rebuild |

**Conclusion:** GPU encoding is fast. CPU filter processing is slow. The GPU sat idle waiting for frames.

## Why OpenCV

OpenCV computes the affine transform matrix **once**, then applies it to all frames. No per-frame reallocation.

| Approach | 6hr video time | Why |
|----------|----------------|-----|
| FFmpeg zoompan | ~33 min | Per-frame SwsContext rebuild |
| OpenCV (16 workers) | ~5.4 min | Pre-computed transforms |

## Related Documents

- [VideoGen Pipeline Architecture](videogen-pipeline-architecture.md)
- [OpenCV Vertical Slices](videogen-opencv-vertical-slices.md)
- [GPU NVENC Parallelization](gpu-nvenc-parallelization.md)
- [cudacodec GPU Video Encoding](cudacodec-gpu-video-encoding.md)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

