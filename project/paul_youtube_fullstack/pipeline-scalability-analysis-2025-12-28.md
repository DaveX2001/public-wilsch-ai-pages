# Pipeline Scalability Analysis
[[client-paul]]

---
publish: true
date: 2025-12-28
status: snapshot
---

## Pipeline Stages

| Stage | Runs On | Parallel? | Status |
|-------|---------|-----------|--------|
| 1. Fetch Transcript | YouTube API | No | ⚠️ Fast, acceptable |
| 2. Process Script | LiteLLM | Chunked | ⚠️ Sequential LLM calls |
| 3. TTS Generation | RunPod | ✅ Yes | ✅ Scalable |
| 4. Whisper (RunPod) | RunPod | ✅ Yes | ✅ Inside TTS handler |
| 5. Whisper (Local) | nuca-systems | ❌ No | ❌ **REDUNDANT** |
| 6. Scene Extraction | LiteLLM | ✅ Yes | ✅ Scalable |
| 7. Image Gen (FLUX) | RunPod | ✅ Yes | ✅ Scalable |
| 8. Video Assembly | nuca-systems | ❌ No | ❌ **BLOCKER** |

## Blockers

| Blocker | Why | Fix |
|---------|-----|-----|
| Local Whisper (Step 5) | Handler already transcribes but discards timing | Return segments from handler → eliminate local step |
| Video Assembly (Step 8) | FFmpeg merge is inherently sequential | Harder problem - out of scope for now |

## Key Findings

| Finding | Implication |
|---------|-------------|
| Cold starts hurt more than contention | Parallel keeps workers warm |
| `MAX_CONCURRENT_TASKS=2` works | Both videos run stages in parallel |
| Handler change is small | Preserve segment timing instead of discarding |

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

