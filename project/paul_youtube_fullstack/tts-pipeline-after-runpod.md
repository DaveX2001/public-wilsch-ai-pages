---
publish: true
---

# TTS Pipeline: AFTER RunPod Migration
[[client-paul]]

Data flow diagram showing the target architecture after migrating TTS + Whisper validation to RunPod.

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────────┐
│                         LOCAL (Orchestration)                        │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   processed/narrative_transcript.txt                                 │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────────────────────────────────┐                           │
│   │  generate_voiceover_optimized()     │                           │
│   │  ├─ Normalize text                  │                           │
│   │  └─ Split into chunks               │                           │
│   └─────────────────────────────────────┘                           │
│       │                                                              │
│       │ chunks[] (e.g., 50-200 text chunks)                         │
│       ▼                                                              │
│   ┌─────────────────────────────────────┐                           │
│   │  Parallel Dispatch (asyncio)        │                           │
│   │  ├─ Send all chunks to RunPod       │                           │
│   │  └─ Collect results as they finish  │                           │
│   └─────────────────────────────────────┘                           │
│       │                                                              │
└───────┼──────────────────────────────────────────────────────────────┘
        │
        │ HTTP POST to RunPod endpoint (up to 20 concurrent)
        ▼
┌─────────────────────────────────────────────────────────────────────┐
│                    RUNPOD (20 Workers × 4090 GPUs)                   │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌──────────────────────────────────────────────────────────────┐  │
│   │  Worker 1                        Worker 2        ...Worker 20 │  │
│   │  ┌────────────────────────┐     ┌────────────┐   ┌──────────┐│  │
│   │  │ handler(chunk_1)       │     │ handler()  │   │ handler()││  │
│   │  │ ├─ Generate N cands    │     │    ...     │   │    ...   ││  │
│   │  │ ├─ faster-whisper val  │     │            │   │          ││  │
│   │  │ └─ Select best         │     │            │   │          ││  │
│   │  └────────────────────────┘     └────────────┘   └──────────┘│  │
│   └──────────────────────────────────────────────────────────────┘  │
│                                                                      │
│   If > 20 requests: Queue → workers pick up as they complete        │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
        │
        │ Returns: { audio_base64, best_score, metadata }
        ▼
┌─────────────────────────────────────────────────────────────────────┐
│                         LOCAL (Post-Processing)                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│   ┌─────────────────────────────────┐                               │
│   │  Collect Results                │                               │
│   │  └─ Decode audio_base64 → bytes │                               │
│   └─────────────────────────────────┘                               │
│       │                                                              │
│       ▼                                                              │
│   ┌─────────────────────────────────┐                               │
│   │  Concatenation (local)          │                               │
│   │  ├─ Add pauses between chunks   │                               │
│   │  ├─ Apply fades                 │                               │
│   │  └─ Join audio segments         │                               │
│   └─────────────────────────────────┘                               │
│       │                                                              │
│       ▼                                                              │
│   audio/audio_narrative_transcript.wav                              │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## What Moves to RunPod

| Step | Before | After |
|------|--------|-------|
| Load model | Local GPU (cold start each job) | Worker GPU (warm, persistent) |
| Generate candidates | Local, sequential | RunPod, parallel across workers |
| Whisper validation | Local Whisper | In-handler faster-whisper |
| Best selection | Local | In-handler |

---

## What Stays Local

| Step | Why |
|------|-----|
| Text normalization | CPU-only, trivial |
| Chunking | CPU-only, fast |
| Parallel dispatch | Orchestration, asyncio |
| Concatenation | CPU-only, librosa/numpy |
| Pause insertion | CPU-only, simple math |
| Metadata/segments | CPU-only |

---

## Handler Input/Output Schema

### Input (per chunk)
```json
{
    "text": "The quick brown fox jumps over the lazy dog.",
    "audio_prompt_url": "https://supabase.../voices/CH5-Zen.wav",
    "language_id": "en",
    "exaggeration": 0.3,
    "cfg_weight": 0.3,
    "n_candidates": 3,
    "whisper_threshold": 85,
    "max_retries": 2
}
```

### Output
```json
{
    "status": "success",
    "audio_base64": "UklGRv4...",
    "best_score": 92,
    "sample_rate": 24000,
    "audio_duration_seconds": 4.2,
    "metadata": {
        "candidates_generated": 3,
        "candidates_passed": 2,
        "all_scores": [92, 88, 72],
        "retries_used": 0,
        "generation_time_ms": 2800,
        "validation_time_ms": 600
    }
}
```

---

## Parallelization Model

```
Example: 50 chunks, 20 workers

Time 0:    Workers 1-20 each pick up chunks 1-20
Time T1:   Worker 3 finishes first → picks up chunk 21
Time T2:   Worker 7 finishes → picks up chunk 22
...
Time Tn:   Last worker finishes chunk 50

Total time ≈ (50 chunks × avg_time) / 20 workers
           ≈ 2.5× single-chunk time (vs 50× sequential)
```

### Queue Behavior
- **≤ 20 chunks:** All run in parallel, no queuing
- **> 20 chunks:** First 20 run immediately, rest queue
- **As workers complete:** They pick up next queued chunk
- **Result:** Near-linear speedup up to 20x

---

## Comparison: Before vs After

### Before (Local Sequential)
```
Chunk 1: [Generate 3 cands][Validate][Select] → 8s
Chunk 2: [Generate 3 cands][Validate][Select] → 8s
...
Chunk 50: [Generate 3 cands][Validate][Select] → 8s
─────────────────────────────────────────────────
Total: 50 × 8s = 400s (6.7 minutes)
```

### After (RunPod Parallel)
```
Chunks 1-20:  [All running in parallel] → 8s
Chunks 21-40: [All running in parallel] → 8s
Chunks 41-50: [Running in parallel]     → 8s
─────────────────────────────────────────────────
Total: 3 rounds × 8s = 24s + overhead ≈ 30-40s
```

**Speedup: ~10-15x for typical audiobook chapter**

---

## Implementation Checklist

### Handler Changes (handler.py)
- [ ] Add `n_candidates`, `whisper_threshold`, `max_retries` to TTSInput
- [ ] Load faster-whisper model (once per worker)
- [ ] Implement candidate generation loop
- [ ] Implement validation with faster-whisper
- [ ] Implement best-selection logic
- [ ] Return metadata with scores

### Local Code Changes (tts_generator.py)
- [ ] Remove local model loading
- [ ] Remove local candidate loop
- [ ] Add async parallel dispatch to RunPod
- [ ] Collect results and decode audio
- [ ] Keep concatenation logic (pauses, fades)

### Testing
- [ ] Verify audio quality matches local pipeline
- [ ] Verify exaggeration=0.3 produces same output
- [ ] Benchmark parallel speedup
- [ ] Test queue behavior with >20 chunks

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

