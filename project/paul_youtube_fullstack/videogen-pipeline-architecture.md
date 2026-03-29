---
publish: true
---

# VideoGen Pipeline Architecture
[[client-paul]]

Complete documentation of the Video Generation pipeline from API request to final video output.

## Pipeline Overview

```
[API Request] → [Orchestration] → [Video Generation] → [Post-Processing] → [Final Video]
```

The pipeline transforms images + audio into a slideshow video with Ken Burns effects, transitions, overlays, and captions.

---

## Phase 1: REQUEST HANDLING

**Entry Point:** `POST /api/generate` in `server.ts:2068`

| Step | What Happens | Location |
|------|--------------|----------|
| 1. Receive Request | Parse `projectPath` and CLI-style args | L2068-2081 |
| 2. Convert Args | CLI args → template format via `cliArgsToTemplate()` | L2085 |
| 3. Map Overlays | Canvas elements → overlay objects | L2099-2120 |
| 4. Build Options | Construct `videoGenerationOptions` object | L2128-2160 |
| 5. Call makeVideo | Trigger orchestration layer | L2178 |

---

## Phase 2: ORCHESTRATION

**Entry Point:** `makeVideo()` in `makeVideoFunction.ts:133`

| Step | What Happens | Location |
|------|--------------|----------|
| 1. Validate Paths | Check `pathToFolder` and `rootCodePath` exist | L151-164 |
| 2. Normalize Config | Validate transition type, handle CUDA setting | L189-218 |
| 3. Setup Background Music | Build music config (volume, fade, loop) | L280-328 |
| 4. Call Generator | Invoke `generateSlideshow()` with normalized options | L221 |

---

## Phase 3: CORE GENERATION

**Entry Point:** `generateSlideshow()` in `generateSlideshow.ts:611`

### Stage 3.1: Media Discovery
**Location:** L611-794

- Probe audio file for duration (L672-684)
- Discover images from `footage/` folder via `listImages()` (L790-794)
- Separate images vs videos, probe video durations (L819-841)
- Calculate `perImageDuration` from audio length (L843-862)

### Stage 3.2: Encoder Selection
**Location:** L865-960

- Detect NVENC support (h264/hevc/av1) via `detectNvencSupport()` (L869)
- Select codec based on GPU capabilities and options
- RTX 5080 optimizations if detected (L905-913)
- CPU fallback (libx264) if no NVENC (L943-949)

### Stage 3.3: Ken Burns Effect
**Location:** `buildKenBurnsExpr()` at L537-595, applied at L1070

**This is the bottleneck (86% CPU).**

Zoom calculation:
- `zoomRange = zoomPercent / 100` (default: 8% = 0.08)
- Zoom In: `min(1.0 + inc*on, 1.0 + zoomRange)`
- Zoom Out: `max(1.0 + zoomRange - inc*on, 1.0)`

Pan calculation:
- Pan amount: 30% of available space
- Directions: left, right, up, down, none, auto

Filter applied at L1070:
```
scale=8000:-1,zoompan=z='${z}':x='${x}':y='${y}':d=${frames}:s=WxH
```

### Stage 3.4: Transitions (xfade)
**Location:** L1086-1115

- Iterate through slide pairs (L1092)
- Calculate offset: `currentTime + prevDuration - transitionDuration` (L1103)
- Apply xfade: `xfade=transition=${type}:duration=${dur}:offset=${offset}` (L1106)

### Stage 3.5: Overlays & Effects
**Location:** L1118-1273

- Dim overlay: `color=black@${dimAmt}` + overlay (L1118-1125)
- Overlays: sorted by zIndex, scale, colorkey, opacity (L1127-1168)
- Subtitles: SRT → ASS conversion, subtitles filter (L1170-1273)

### Stage 3.6: FFmpeg Execution
**Location:** L1380-1943

- Assemble inputs: audio, images (with `-loop 1`), overlays, background music
- Write `filter_complex.txt` to avoid command line limits (L1613-1621)
- Execute FFmpeg with progress tracking (L1864-1942)
- Monitor via `loading.txt` file (L1887)

---

## Phase 4: POST-PROCESSING

**Location:** L1945-1977

| Step | What Happens | Output |
|------|--------------|--------|
| 1. Cleanup | Remove temp video loop files | - |
| 2. Write Config | Generation stats to `config.json` | `output/config.json` |
| 3. Return Result | outputPath, imageCount, audioSeconds, usedNvenc | API response |

---

## Complete Filter Pipeline

```
INPUT STAGE
├── [0:a] Audio narrative
├── [1:v] Image 1 → [img0]
├── [2:v] Image 2 → [img1]
├── [ov0] Overlay 1
└── [bg] Background music

PER-IMAGE PROCESSING (Ken Burns)
[img0] → scale → crop → scale=8000:-1 → zoompan → fps → format → [slide0]
[img1] → scale → crop → scale=8000:-1 → zoompan → fps → format → [slide1]

TRANSITIONS
[slide0][slide1] → xfade → [xf1]
[xf1][slide2] → xfade → [vslideshow]

DIM OVERLAY (optional)
[vslideshow] + [dim] → overlay → [vdimmed]

OVERLAYS (sorted by zIndex)
[vdimmed] + [ov0] → overlay → [vcomposited]

SUBTITLES (optional)
[vcomposited] → subtitles → [vout]

AUDIO MIXING
[narr] + [bg] → amix → [aout]

ENCODING
[vout] + [aout] → h264_nvenc → final.mp4
```

---

## Example FFmpeg Command

```bash
ffmpeg -v verbose \
  -hwaccel cuda -hwaccel_output_format cuda \
  -i "audio/audio_narrative_transcript.wav" \
  -loop 1 -i "footage/image_001.jpg" \
  -loop 1 -i "footage/image_002.jpg" \
  -filter_complex_script "output/filter_complex.txt" \
  -map "[vout]" -map "[aout]" \
  -r 30 -pix_fmt yuv420p -movflags +faststart \
  -c:v h264_nvenc -preset p1 -b:v 8M \
  -c:a aac -b:a 192k \
  "output/final.mp4"
```

---

## Key Parameters

| Parameter | Default | Purpose |
|-----------|---------|---------|
| `width` | 1344 | Output width |
| `height` | 768 | Output height |
| `fps` | 30 | Frame rate |
| `transitionDuration` | 0.5 | Transition overlap (seconds) |
| `zoomPercent` | 8 | Ken Burns zoom amount |
| `dimImages` | 0 | Background dim (0-1) |

---

## Files Involved

| File | Purpose |
|------|---------|
| `server.ts` | API endpoint, request handling |
| `makeVideoFunction.ts` | Orchestration, config normalization |
| `generateSlideshow.ts` | Core generation, filter construction |
| `footage/*.jpg` | Input images |
| `audio/audio_narrative_transcript.wav` | Input audio |
| `output/final.mp4` | **Final output** |
| `output/filter_complex.txt` | FFmpeg filter script |
| `output/config.json` | Generation metadata |

---

## Planned Changes: OpenCV Ken Burns

**Current:** FFmpeg zoompan filter (86% CPU, sequential)

**Target:** OpenCV CPU with parallel processing (6x speedup)

See: [Ken Burns Optimization Spike](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/opencv-cuda-ken-burns-solution)

### Integration Point

Insert OpenCV call between image discovery (L794) and filter chain building (L1009):

```
listImages() → [OpenCV generates clips in parallel] → filter_complex (no zoompan)
```

### Expected Impact

| Metric | Current | After OpenCV |
|--------|---------|--------------|
| Ken Burns time (6hr video) | ~33 min | ~5.4 min |
| Overall pipeline | ~33 min | ~10-11 min |
| CPU utilization | 158% (bottleneck) | Distributed across 16 workers |

---

## Sources

- Code investigation: Dec 23, 2024
- Issue: [#329 VideoGen Scaling Spike](https://github.com/DaveX2001/deliverable-tracking/issues/329)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

