---
publish: true
---
# AI Avatar / Talking Head Options Research
[[client-paul]]

**Date:** 2026-01-11
**Context:** Paul video pipeline avatar integration assessment
**Use case:** Overlay talking head on slideshow videos for YouTube monetization compliance

## Summary

YouTube policy changes require on-screen human presence for monetization. This research covers options for adding AI-generated talking heads to automated video pipeline.

---

## Commercial Option: HeyGen API

| Aspect | Details |
|--------|---------|
| **Quality** | Gold standard (100% baseline) |
| **API Pricing** | Pro $99/mo (~$1/min), Scale $330/mo (~$0.50/min) |
| **Subscription** | $29/mo unlimited (web UI only, not automatable) |
| **Integration** | Simple API call |
| **Pros** | Best quality, no infrastructure, reliable |
| **Cons** | Ongoing cost, third-party dependency |

**Cost at scale:**
- 50 videos/mo × 10 min × $0.50 = ~$250/month
- 100 videos/mo = ~$500/month
- 200 videos/mo = ~$1,000/month

---

## Open-Source Options (Ranked)

### Tier 1: Production-Ready, Latest (2025)

#### 1. HunyuanVideo-Avatar (Tencent, May 2025) - RECOMMENDED
- **GitHub:** https://github.com/Tencent-Hunyuan/HunyuanVideo-Avatar
- **HuggingFace:** https://huggingface.co/tencent/HunyuanVideo-Avatar
- **Type:** Full-body + portrait
- **Features:**
  - Audio Emotion Module (AEM) for emotional expression
  - Multi-character support
  - Photorealistic, cartoon, 3D-rendered, anthropomorphic styles
  - 10GB VRAM variant (June 2025 update)
- **VRAM:** 10-40GB depending on variant
- **Quality:** ~85-90% of HeyGen

#### 2. EchoMimicV2 (Ant Group, CVPR 2025)
- **GitHub:** https://github.com/antgroup/echomimic_v2
- **HuggingFace:** https://huggingface.co/BadToBest/EchoMimicV2
- **Type:** Semi-body (half-body) animation
- **Features:**
  - 9x speedup optimization (Jan 2025)
  - ~50s for 120 frames on A100
  - Apache-2.0 license
- **VRAM:** 16GB+
- **Quality:** ~80-85% of HeyGen

#### 3. OmniAvatar (Alibaba/Zhejiang, June 2025)
- **GitHub:** https://github.com/Omni-Avatar/OmniAvatar
- **HuggingFace:** https://huggingface.co/OmniAvatar/OmniAvatar-1.3B
- **Type:** Full-body, adaptive body animation
- **Features:**
  - Pixel-wise multi-hierarchical audio embedding (superior lip-sync)
  - 1.3B model for consumer GPUs
  - LoRA-based approach
- **VRAM:** ~30GB total for all dependencies
- **Quality:** ~80-85% of HeyGen

#### 4. LAM (SIGGRAPH 2025)
- **GitHub:** https://github.com/aigc3d/LAM
- **ModelScope:** Damo_XR_Lab/LAM-20K
- **Type:** 3D Gaussian head from single image
- **Features:**
  - Real-time rendering
  - Mobile-capable
  - Audio2Expression model (April 2025)
- **Quality:** ~75-80% of HeyGen

### Tier 2: Proven, Stable

#### 5. Hallo3 (Fudan University, CVPR 2025)
- **GitHub:** https://github.com/fudan-generative-vision/hallo3
- **Type:** Portrait, 4K videos up to 1 hour
- **Features:**
  - DPO-powered emotional expression
  - Training data released on HuggingFace
- **Limitation:** English audio only
- **VRAM:** ~12GB
- **Quality:** ~80% of HeyGen

#### 6. V-Express (Tencent AI Lab, 2024)
- **GitHub:** https://github.com/tencent-ailab/V-Express
- **Type:** Portrait with pose control
- **Features:**
  - Progressive drop operations for signal balancing
  - D-ID-like quality
- **VRAM:** ~12GB
- **Quality:** ~75-80% of HeyGen

#### 7. Duix.Heygem (2024)
- **GitHub:** https://github.com/duixcom/Duix.Heygem
- **Type:** HeyGen clone, full system
- **Features:**
  - REST API at port 8383
  - Multi-language (EN, JP, KR, CN, FR, DE, AR, ES)
  - Fully offline
  - Voice cloning included
- **Requirements:** Docker, 8GB+ VRAM, 100GB disk (10GB lite)
- **Platform:** Windows + Ubuntu 22.04
- **Quality:** ~75-80% of HeyGen

### Tier 3: Older but Reliable

#### 8. SadTalker (2023)
- **GitHub:** https://github.com/OpenTalker/SadTalker
- **Type:** Single image to talking head
- **Features:** Easy setup, reliable, CVPR 2023
- **VRAM:** ~8GB
- **Quality:** ~65-75% of HeyGen

#### 9. LivePortrait (2024)
- **GitHub:** https://github.com/KwaiVGI/LivePortrait
- **Type:** Portrait animation
- **Features:** 20x faster than competitors
- **Catch:** Needs driving video, not just audio
- **VRAM:** 6-8GB
- **Quality:** ~75-80% of HeyGen

#### 10. MuseTalk (2024)
- **GitHub:** https://github.com/TMElyralab/MuseTalk
- **Type:** Real-time lip-sync
- **Features:** 30+ FPS on V100, multi-language
- **Limitation:** Lip-sync only, no head movement, 256x256 face region
- **VRAM:** 4GB+
- **Quality:** ~70-80% of HeyGen

#### 11. Wav2Lip (2020)
- **GitHub:** https://github.com/Rudrabha/Wav2Lip
- **Type:** Lip-sync on existing footage
- **Best for:** Dubbing, not avatar generation
- **VRAM:** 12-16GB
- **Quality:** ~60-70% of HeyGen

#### 12. AniPortrait (2024)
- **GitHub:** https://github.com/Zejun-Yang/AniPortrait
- **Type:** Audio-driven portrait
- **Known issue:** Teeth artifacts
- **VRAM:** ~10GB
- **Quality:** ~70-75% of HeyGen

#### 13. GeneFace++ (2024)
- **GitHub:** https://github.com/yerfor/GeneFacePlusPlus
- **Type:** Domain-adaptable talking head
- **Status:** Research-focused
- **Quality:** ~70-75% of HeyGen

### Coming Soon

#### Avatar Forcing (Jan 2026)
- **Project:** https://taekyungki.github.io/AvatarForcing/
- **GitHub:** https://github.com/TaekyungKi/AvatarForcing (code coming)
- **Type:** Real-time interactive avatar
- **Features:** True interactive reaction, 500ms latency
- **Status:** Paper released, code/weights not yet available

### Not Available

#### EMO (Alibaba)
- **Status:** Paper only, code/weights never released
- **GitHub:** https://github.com/HumanAIGC/EMO (empty repo)

---

## Comparison Matrix

| Model | Quality | Full Body | Emotion | VRAM | Speed | License |
|-------|---------|-----------|---------|------|-------|---------|
| HunyuanVideo-Avatar | 85-90% | Yes | Yes | 10-40GB | Fast | Open |
| EchoMimicV2 | 80-85% | Semi | Basic | 16GB | Very Fast | Apache 2.0 |
| OmniAvatar | 80-85% | Yes | Basic | 30GB | Fast | Open |
| LAM | 75-80% | No | Basic | Varies | Real-time | Open |
| Hallo3 | 80% | No | Basic | 12GB | Fast | Open |
| V-Express | 75-80% | No | Basic | 12GB | Fast | Apache 2.0 |
| Duix.Heygem | 75-80% | No | Basic | 8GB | Fast | Open |
| SadTalker | 65-75% | No | Yes | 8GB | Fast | Apache 2.0 |

---

## Integration with Paul Pipeline

Current pipeline:
```
TTS (Chatterbox) → Scene Extraction (GPT) → Image Gen (FLUX) →
Video Assembly (FFmpeg/GPU) → Final Compositing → final.mp4
```

Avatar integration point:
```
TTS → audio.wav
       ↓
Avatar Service (NEW) → avatar.mp4
       ↓
Final Compositing → overlay avatar on slideshow → final.mp4
```

**Required inputs:** TTS audio (WAV), reference portrait image
**Output:** Avatar video file for FFmpeg overlay

---

## Cost Comparison (100 videos/month, 10 min each)

| Approach | Monthly Cost | Setup Effort | Quality |
|----------|--------------|--------------|---------|
| HeyGen API (Scale) | ~$500 | Low | 100% |
| Self-hosted (RunPod) | ~$25-50 GPU | High | 75-90% |

Break-even: Self-hosted becomes worthwhile at very high volume OR if third-party dependency is unacceptable.

---

## Tracking Resources

- [awesome-talking-head-generation](https://github.com/harlanhong/awesome-talking-head-generation)
- [Awesome-Talking-Head-Synthesis](https://github.com/Kedreamix/Awesome-Talking-Head-Synthesis)
- [talking-face-arxiv-daily](https://github.com/liutaocode/talking-face-arxiv-daily)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

