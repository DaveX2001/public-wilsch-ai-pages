---
publish: true
---

# TTS Vertical Slices
[[client-paul]]

Vertical slice approach for migrating TTS generation from local GPU to RunPod serverless.

## Why Vertical Slices

The horizontal approach (building all layers at once) failed because when audio quality issues appeared, it was impossible to isolate whether the problem was in:
- Chunking logic
- RunPod handler
- Whisper validation
- Candidate selection
- Concatenation

Vertical slicing means: **one atomic change per slice, test after each**. If a slice breaks audio quality, you know exactly what caused it.

## The Pipeline (Reference)

```
Phase 1: Text Preparation (Flask - unchanged)
├─ Directory setup, locate transcript, preprocess text

Phase 2: Audio Generation
├─ Stage 2.1: Setup (model loads in handler)
├─ Stage 2.2: Chunking (local CPU)
├─ Stage 2.3: Generation ← RUNS ON RUNPOD (parallel dispatch)
└─ Stage 2.4: Concatenation (local CPU)

Phase 3: Post-Processing (Flask - unchanged)
├─ Verify output, save metadata, build segments
```

## Stage 1: Handler Development (Slices 1-4)

Keep Flask code structure. Replace only the `model.generate()` call with a RunPod handler call. Handler absorbs more functionality with each slice.

| Slice | Handler Responsibility | Flask Responsibility | Test |
|-------|----------------------|---------------------|------|
| 1 | `model.generate()` only | Candidate loop, validation, selection, concatenation | AssemblyAI transcribe → compare |
| 2 | Generate N candidates, return all | Validate each locally, select best locally | Same |
| 3 | Generate N + Whisper validate each | Select best from scores | Same |
| 4 | Generate N + Validate + Select best | Just receive 1 audio per chunk, concatenate | Same |

**After Slice 4:** Handler is complete. It accepts text, returns best audio. Flask loops through chunks and concatenates results.

## Stage 2: Parallel Dispatch (Slice 5)

**Decision:** Stay in Flask[async] instead of migrating to FastAPI. FastAPI is deferred to future work.

| Slice | Change | Why |
|-------|--------|-----|
| 5 | Flask[async] + `asyncio.gather()` for parallel dispatch | Performance gain without framework migration |
| 6+ | FastAPI migration (deferred) | Architectural modernization when needed |

**Why Flask[async] instead of FastAPI:**
- Performance gain is the goal, not architectural modernization
- Flask 2.0+ supports native async views
- Simpler implementation, less migration risk
- FastAPI can be added later when needed

### Slice 5 Implementation Details

**Concurrency Control:**
- `asyncio.Semaphore(25)` - 20 workers + 5 buffer to keep queue fed
- Limits orphaned requests on cancellation

**Error Handling:**
- Transient errors (5xx, timeout, network): Retry with backoff [0.5s, 1s, 2s]
- Permanent errors (4xx, decode): Fail immediately
- Max 3 retries before job failure

**Code Structure:**
```python
# Async function for parallel dispatch
async def generate_voiceover_optimized_async(...):
    semaphore = asyncio.Semaphore(25)
    async with httpx.AsyncClient() as client:
        tasks = [process_chunk_with_retry(semaphore, client, ...) for chunk in chunks]
        results = await asyncio.gather(*tasks)
    # Concatenate results...

# Sync wrapper for backward compatibility
def generate_voiceover_optimized(...):
    return asyncio.run(generate_voiceover_optimized_async(...))
```

**Expected Performance:**
- Sequential: ~126s for typical chapter (10 chunks)
- Parallel: ~20s (limited by slowest chunk + overhead)
- Speedup: ~6x (depends on chunk count and RunPod queue)

## What Stays Constant Throughout

- **Phase 1** (text preparation): Always in Flask, never changes
- **Phase 3** (post-processing): Always in Flask, never changes
- **Stage 2.2** (chunking): Stays local CPU, logic unchanged
- **Stage 2.4** (concatenation): Stays local CPU, logic unchanged

Only Stage 2.3 (generation) migrates, and even then the LOGIC stays the same - just WHERE it runs and HOW it's dispatched changes.

## Validation Protocol

After every slice:

1. Generate audio with new slice code
2. Run `validate_tts.py` (AssemblyAI transcription)
3. Compare transcript to original text
4. If content loss, stuttering, or garbling → debug before next slice
5. If clean → proceed to next slice

The validation script saves transcript to `<audio>.transcript.txt` for comparison.

## Handler Configuration

RunPod handler image: `mariuswilsch/chatterbox-runpod:slice4-v1`

**Input:**
```json
{
  "input": {
    "text": "chunk text",
    "audio_prompt_url": "https://supabase.../voices/voice.wav",
    "n_candidates": 3,
    "whisper_threshold": 85,
    "max_retries": 3,
    "exaggeration": 0.3,
    "cfg_weight": 0.3
  }
}
```

**Output:**
```json
{
  "output": {
    "status": "success",
    "audio_base64": "...",
    "validation": {
      "selected_score": 92,
      "selected_passed": true
    }
  }
}
```

## References

- [TTS Pipeline Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/tts-pipeline-architecture) - Full pipeline documentation
- [TTS Pipeline AFTER RunPod](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/tts-pipeline-after-runpod) - Target architecture
- Issue #198 (closed) - Documents why horizontal approach failed
- Issue #257 - TTS RunPod Migration - Vertical Slices (current work)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

