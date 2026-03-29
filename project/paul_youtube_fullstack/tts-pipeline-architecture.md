---
publish: true
---

# TTS Pipeline Architecture
[[client-paul]]

Complete documentation of the Text-to-Speech pipeline from text input to final audio output.

## Pipeline Overview

```
[Transcript File] → [Preprocessing] → [TTS Generation] → [Post-Processing] → [Final Audio]
```

The pipeline transforms a narrative transcript into a validated, high-quality audio file with segment timing metadata.

---

## Phase 1: BEFORE (Text Preparation)

**Entry Point:** `generate_audio_from_transcript()` in `tts_generator.py:479`

| Step | What Happens | Location |
|------|--------------|----------|
| 1. Directory Setup | Creates `<transcript_dir>/audio/` | Line 493 |
| 2. Locate Transcript | Finds `processed/narrative_transcript.txt` | Lines 504-522 |
| 3. Text Preprocessing | Roman numerals → words, normalize whitespace | Line 528 |
| 4. Load Metadata | Reads `narrative_transcript_metadata.json` | Lines 530-540 |
| 5. Initialize TTSGenerator | Creates instance with config (voice, speed, language) | Line 588 |
| 6. Call generate_audio() | Triggers the main TTS flow | Line 590 |

### Pre-Generation in generate_audio()

| Step | What Happens | Location |
|------|--------------|----------|
| 7. Voice Resolution | Finds voice .wav file from config | Line 194 |
| 8. Text Preprocessing | Strips whitespace, expands abbreviations | Lines 197-200 |
| 9. GPU Wait | Polls until GPU utilization < 10% | Line 204 |
| 10. Load Parameters | Extracts optimization settings | Lines 214-225 |

---

## Phase 2: MAIN (Audio Generation)

**Core Function:** `generate_voiceover_optimized()` in `chatterbox/tts.py:905`

### Stage 2.1: Setup
- Load Chatterbox model onto GPU
- Normalize text for language
- Create output directory

### Stage 2.2: Chunking
- Split text into phrases using `split_by_lang()` (respects `max_phrase_len=300`)
- Merge short segments if `min_chars_merge > 0`
- Result: ~200-400 text chunks depending on input size

### Stage 2.3: Candidate Generation with Validation

**For each chunk:**
```
FOR candidate in range(n_candidates):  # default: 3
    FOR retry in range(max_retries):
        audio = model.generate(chunk)
        score = whisper_validate(audio)
        if score >= threshold:  # default: 85%
            break
    track_best_candidate()
SELECT best_candidate (by score or duration)
SAVE as voice_{idx}.wav
```

### Stage 2.4: Concatenation
- Load each `voice_{idx}.wav` chunk
- Apply silence trimming (skip for CJK languages)
- Apply fade effects to chunk edges
- Insert intelligent pauses between chunks
- Concatenate all chunks → `final_audio`
- Save to `audio/audio_narrative_transcript.wav`

---

## Phase 3: AFTER (Post-Processing)

**Location:** `tts_generator.py:273-320`

| Step | What Happens | Output |
|------|--------------|--------|
| 1. Verify Output | Check audio file was created | - |
| 2. Extract Metadata | Duration, sample rate from audio file | - |
| 3. Save Metadata JSON | Generation details | `*_metadata.json` |
| 4. Build Segments | Whisper-transcribe each chunk for timing | `segments.json` |
| 5. Return Result | Dict with all metadata | - |

---

## Data Flow Diagram

```
processed/narrative_transcript.txt
    │
    ▼
┌─────────────────────────────────┐
│  generate_audio_from_transcript │
│  ├─ Preprocess text             │
│  ├─ Load config (voice, lang)   │
│  └─ Create TTSGenerator         │
└─────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────┐
│  TTSGenerator.generate_audio    │
│  ├─ Resolve voice file          │
│  ├─ Wait for low GPU            │
│  └─ Load optimization params    │
└─────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────┐
│  generate_voiceover_optimized   │  ◄── CORE TTS
│  ├─ Load model                  │
│  ├─ Split into chunks           │
│  ├─ FOR each chunk:             │
│  │   ├─ Generate N candidates   │
│  │   ├─ Whisper validate        │
│  │   └─ Select best             │
│  ├─ Add pauses between chunks   │
│  └─ Concatenate → final.wav     │
└─────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────┐
│  Post-Processing                │
│  ├─ Save metadata JSON          │
│  └─ Build segments.json         │
└─────────────────────────────────┘
    │
    ▼
audio/audio_narrative_transcript.wav
audio/audio_narrative_transcript_metadata.json
audio/segments.json
```

---

## Key Parameters

| Parameter | Default | Purpose |
|-----------|---------|---------|
| `max_phrase_len` | 300 | Max characters per chunk |
| `n_candidates` | 3 | Audio attempts per chunk |
| `whisper_validate` | true | Enable quality validation |
| `whisper_threshold` | 85 | Minimum accuracy score |
| `pause_ms` | 200 | Base pause between chunks (ms) |
| `fade_ms` | 10 | Fade effect at chunk edges (ms) |
| `language` | "en" | Language code for processing |
| `exaggeration` | 0.3 | Voice expressiveness |
| `cfg_weight` | 0.3 | Classifier-free guidance |

---

## Files Involved

| File | Purpose |
|------|---------|
| `tts_generator.py` | Entry function, pre/post processing |
| `chatterbox/tts.py` | Core TTS generation, validation, concatenation |
| `processed/narrative_transcript.txt` | Input text |
| `audio/audio_narrative_transcript.wav` | **Final output** |
| `audio/*_metadata.json` | Generation metadata |
| `audio/segments.json` | Segment timing info |
| `data/voices/<name>.wav` | Voice reference audio |

---

## Planned Changes: RunPod Migration

**Current:** Local Chatterbox + Local Whisper (sequential)

**Target:** RunPod handler with Chatterbox + faster-whisper (parallel)

### Handler Encapsulation

The RunPod handler will encapsulate the candidate loop:

```python
# Input (per chunk)
{
    "text": "chunk text",
    "voice": "CH5-Zen.wav",
    "n_candidates": 3,
    "threshold": 85,
    "exaggeration": 0.3,
    "cfg_weight": 0.3
}

# Output
{
    "status": "success",
    "audio_base64": "...",
    "best_score": 92,
    "metadata": {
        "candidates_generated": 3,
        "all_scores": [92, 88, 72]
    }
}
```

### Calling Code Simplification

```python
# Fire all chunks in parallel
results = await asyncio.gather(*[
    call_runpod(chunk) for chunk in chunks
])

# Each result is pre-validated, pre-selected
audio_segments = [decode(r["audio_base64"]) for r in results]
concatenate(audio_segments)
```

### Benefits
- Single API call per chunk (cost efficient)
- True parallelization across chunks
- No local GPU needed
- Faster-whisper for quicker validation
- Same quality pipeline, encapsulated

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

