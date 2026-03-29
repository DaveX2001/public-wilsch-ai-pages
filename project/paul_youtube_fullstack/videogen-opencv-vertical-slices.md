---
publish: true
---

# VideoGen OpenCV Integration - Vertical Slices
[[client-paul]]

Vertical slice approach for integrating OpenCV Ken Burns into the video generation pipeline.

## Why Vertical Slices

The horizontal approach (changing everything at once) makes debugging impossible. If video quality degrades, you can't tell if it's:
- OpenCV Ken Burns implementation
- Filter chain modifications
- Clip handoff to FFmpeg
- Timing/duration mismatches

Vertical slicing means: **one atomic change per slice, test after each**.

---

## The Pipeline (Reference)

```
Current:
  images → FFmpeg zoompan → xfade → encode → final.mp4
           (86% CPU, slow)

Target:
  images → OpenCV (parallel) → clips → FFmpeg xfade → encode → final.mp4
           (16 workers, fast)    (no zoompan)
```

Only Ken Burns moves to OpenCV. Everything else stays in FFmpeg.

---

## Design Decisions (Resolved)

| Question | Resolution |
|----------|------------|
| Script location | `paul_template_generation/backend/scripts/opencv_ken_burns.py` |
| Fallback strategy | Check at startup, fail early if OpenCV unavailable |
| Param passing | JSON config file (debuggable) |
| Error handling | Exit codes + stderr |

---

## Slice 1: Python Script (Standalone)

**Goal:** Create the OpenCV Ken Burns script that can run independently.

### Responsibility
- Read config from JSON file
- Generate Ken Burns clips with OpenCV (parallel, 16 workers)
- Output clip paths to stdout as JSON

### Deliverable
```
paul_template_generation/backend/scripts/opencv_ken_burns.py
```

### Input (config.json)
```json
{
  "images": ["/path/to/img1.jpg", "/path/to/img2.jpg"],
  "outputDir": "/tmp/clips",
  "duration": 2.67,
  "width": 1344,
  "height": 768,
  "fps": 30,
  "zoom": 8,
  "direction": "alternate",
  "pan": "auto",
  "workers": 16
}
```

### Output (stdout)
```json
["/tmp/clips/clip_0000.mp4", "/tmp/clips/clip_0001.mp4"]
```

### Test
```bash
# On nuca-systems
cd ~/projects/.../paul_template_generation/backend/scripts
echo '{"images": [...], ...}' > /tmp/test_config.json
python3 opencv_ken_burns.py --config /tmp/test_config.json

# Verify:
# 1. Clips exist in output directory
# 2. Clips have correct duration
# 3. Ken Burns effect looks correct (zoom + pan)
```

---

## Slice 2: TypeScript Integration (Call Python)

**Goal:** Add the TypeScript function that calls the Python script.

### Responsibility
- Startup check for Python/OpenCV availability
- Write config.json
- Spawn Python process
- Parse output, return clip paths

### Deliverable
```typescript
// generateSlideshow.ts ~L600
async function generateKenBurnsClips(
  images: string[],
  outputDir: string,
  config: KenBurnsConfig
): Promise<string[]>
```

### Startup Check
```typescript
// server.ts ~L50
function checkOpenCVAvailable(): boolean {
  try {
    execSync('python3 -c "import cv2"', { stdio: 'pipe' });
    return true;
  } catch {
    return false;
  }
}
export const OPENCV_AVAILABLE = checkOpenCVAvailable();
```

### Test
```typescript
// Unit test
const clips = await generateKenBurnsClips(
  ['/path/to/test.jpg'],
  '/tmp/test_clips',
  { duration: 3, width: 1344, height: 768, fps: 30, zoom: 8 }
);
assert(clips.length === 1);
assert(fs.existsSync(clips[0]));
```

---

## Slice 3: Filter Chain Modification

**Goal:** Modify FFmpeg filter chain to use pre-rendered clips instead of zoompan.

### Responsibility
- Insert OpenCV call after image discovery (L794)
- Modify filter chain to skip zoompan when clips exist (L1070)
- Change inputs from images to clips (L1477)

### Changes

**L794 - Insert OpenCV call:**
```typescript
const images = listImages(footageDir);

let clipPaths: string[] | null = null;
if (OPENCV_AVAILABLE && kenBurns?.enabled !== false) {
  const clipDir = path.join(pathToFolder, 'temp_clips');
  clipPaths = await generateKenBurnsClips(images, clipDir, {
    duration: perImageDuration,
    width, height, fps,
    zoom: kenBurns.zoomPercent ?? 8,
    direction: kenBurns.direction ?? 'alternate',
    pan: kenBurns.pan ?? 'auto',
    workers: 16
  });
}
```

**L1070 - Skip zoompan if clips exist:**
```typescript
if (clipPaths && clipPaths[i]) {
  // Clip already has Ken Burns applied
  chain = `scale=${width}:${height},fps=${fps},format=yuv420p`;
} else {
  // Original zoompan path (fallback)
  chain = `${scaleCrop},scale=8000:-1,zoompan=...`;
}
```

**L1477 - Use clips as input:**
```typescript
if (clipPaths && clipPaths[i]) {
  cmd.input(clipPaths[i]);  // Already a video
} else {
  cmd.input(filePath).inputOptions(['-loop', '1']);  // Loop image
}
```

### Test
```bash
# Start staging backend
cd ~/projects/.../paul_template_generation/backend
npm run dev

# Generate video via API
curl -X POST http://localhost:3001/api/generate-video \
  -H "Content-Type: application/json" \
  -d '{"projectPath": "/path/to/test/project", ...}'

# Compare output with baseline (generated before changes)
ffprobe -v error -show_entries format=duration baseline.mp4
ffprobe -v error -show_entries format=duration new.mp4
# Durations should match

# Visual comparison: play both side-by-side
```

---

## Slice 4: End-to-End Validation

**Goal:** Full pipeline validation via frontend, Paul signs off on quality.

### Responsibility
- Test via actual frontend UI
- Compare old vs new pipeline output
- Get quality sign-off from Paul

### Test Projects
Available on nuca-systems:
- `/home/paul/root/paul_queue/app/data/transcripts/CH44_0003_vid_a8815003/`
- `/home/paul/root/paul_queue/app/data/transcripts/CH32_test_81f11b4b/`

### Validation Checklist
- [ ] Ken Burns zoom effect looks identical
- [ ] Ken Burns pan direction works correctly
- [ ] Transitions (xfade) work between clips
- [ ] Overlays render correctly
- [ ] Subtitles render correctly
- [ ] Audio sync is correct
- [ ] Video duration matches audio
- [ ] No visual artifacts

### Performance Validation
- [ ] 6hr video Ken Burns time: ~33 min → ~5.4 min
- [ ] Overall pipeline: ~33 min → ~10-11 min

---

## What Stays Constant

- **Phase 1** (request handling): Always in Express, unchanged
- **Phase 4** (post-processing): Always in Express, unchanged
- **xfade transitions**: FFmpeg, unchanged
- **Overlays**: FFmpeg, unchanged
- **Subtitles**: FFmpeg, unchanged
- **Audio mixing**: FFmpeg, unchanged
- **Encoding**: FFmpeg NVENC, unchanged

Only Ken Burns generation moves to OpenCV.

---

## Validation Protocol

After every slice:

1. Run the test described in that slice
2. If test fails → debug before next slice
3. If test passes → proceed to next slice
4. Never skip slices

---

## Rollback Strategy

If any slice causes issues:

```typescript
// generateSlideshow.ts
const USE_OPENCV_KEN_BURNS = process.env.OPENCV_KEN_BURNS !== 'false';

if (USE_OPENCV_KEN_BURNS && OPENCV_AVAILABLE && clipPaths) {
  // New path
} else {
  // Original zoompan path
}
```

Disable via environment variable: `OPENCV_KEN_BURNS=false`

---

## References

- [VideoGen Pipeline Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/videogen-pipeline-architecture)
- [Ken Burns Optimization Spike](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/opencv-cuda-ken-burns-solution)
- Issue: [#329 VideoGen Scaling Spike](https://github.com/DaveX2001/deliverable-tracking/issues/329)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

