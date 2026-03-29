---
publish: true
---

# Avatar Integration Scope Proposal
[[client-paul]]

**Date:** 2026-01-16
**Status:** Scoping Complete, Spike Needed
**Issue:** [#474](https://github.com/DaveX2001/deliverable-tracking/issues/474)

---

## Context

YouTube demonetized Paul's fictional content channels (~€30k loss) due to new policy requiring on-screen human presence. Avatar integration may restore monetization eligibility.

---

## Model Choice

| Rank | Model | Quality | VRAM | Why |
|------|-------|---------|------|-----|
| **#1 Primary** | HunyuanVideo-Avatar (via Wan2GP) | 85-90% | 10GB | Best quality + low VRAM mode |
| **#2 Fallback** | EchoMimicV2 | 80-85% | 16GB | Apache 2.0, proven speed |

**Wan2GP:** https://github.com/deepbeepmeep/Wan2GP
**HunyuanVideo-Avatar:** https://github.com/Tencent-Hunyuan/HunyuanVideo-Avatar

---

## Pipeline

```
TTS Audio → Portrait Image → Avatar Gen → BG Removal → FFmpeg Overlay → Final Video
    ↓              ↓             ↓            ↓              ↓
  Exists      Paul decides    Wan2GP     Matanyone       Exists
```

| Step | Input | Output | Status |
|------|-------|--------|--------|
| 1. TTS | Script text | `audio_narrative_transcript.wav` | ✅ Exists |
| 2. Portrait | — | Reference image | ⚠️ Paul decides |
| 3. Avatar Gen | Audio + Portrait | Full video (with bg) | Wan2GP |
| 4. BG Removal | Full video | Transparent video | Matanyone |
| 5. Overlay | Slideshow + Avatar | Final video | FFmpeg |

---

## Model Configuration

**HunyuanVideo-Avatar:**

| Parameter | Options | Impact |
|-----------|---------|--------|
| `--use-fp8` | true/false | VRAM vs quality |
| `--cpu-offload` | true/false | Enables 10GB mode |
| `--sample-n-frames` | 129 (default) | ~4.3s per generation |
| `--infer-steps` | 50 (default) | Quality vs speed |
| `--image-size` | 704 | Output resolution (704×768) |

**Key constraint:** 1 generation = ~4.3s video. For 10-min audio → ~140 sequential generations.

---

## Infrastructure

| GPU | VRAM | Modal Price | HunyuanVideo? |
|-----|------|-------------|---------------|
| T4 | 16GB | ~$0.20/hr | ✅ (10GB mode) |
| A10G | 24GB | ~$0.60/hr | ✅ Comfortable |
| A100 40GB | 40GB | ~$2.50/hr | ✅ |

---

## Next Steps

| # | What | Why |
|---|------|-----|
| 1 | **Spike: Generation speed** | Is 10-min video feasible in reasonable time? |
| 2 | **Spike: BG removal quality** | Does Matanyone produce clean transparent output? |
| 3 | **Portrait spec** | What image format/size works best? |
| 4 | **Parallelization feasibility** | Can audio be split for parallel workers? |
| 5 | **Integration architecture** | Where does avatar service fit in pipeline? |
| 6 | **Discussion with Paul** | Avatar persona + reference image source |

---

## Open Questions for Paul

1. Avatar persona: Single persona for all videos, or per-channel?
2. Reference image source: Stock? AI-generated? Custom?

---

## Related Docs

- [Avatar Research](./avatar-talking-head-research.md) - Model options comparison

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

