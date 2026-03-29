---
publish: true
---

# GPU Ken Burns: 3-NVENC Parallelization Guide
[[client-paul]]

## 1. Executive Summary

**Problem:** Single GPU stream underutilizes L4's 3 NVENC hardware encoders.

**Solution:** ThreadPoolExecutor with 3 concurrent VideoWriters inside RunPod handler.

**Result:**

| Metric | Single Stream | 3 Parallel | Improvement |
|--------|---------------|------------|-------------|
| Effective FPS | 344 | ~1,030 | 3x |
| Time (358 images) | ~8.5 min | ~2.8 min | 2.7x faster |
| Cost per video | ~$0.07 | ~$0.02 | 3x cheaper |

## 2. Why Parallelize

### L4 GPU Has 3 NVENC Encoders

Each `cv2.cudacodec.createVideoWriter()` uses **one** NVENC encoder. To use all 3:

```python
# This uses 1 encoder:
writer = cv2.cudacodec.createVideoWriter(...)

# To use 3 encoders, create 3 concurrent writers:
writer1 = cv2.cudacodec.createVideoWriter("seg1.mp4", ...)
writer2 = cv2.cudacodec.createVideoWriter("seg2.mp4", ...)
writer3 = cv2.cudacodec.createVideoWriter("seg3.mp4", ...)
```

### Cost Comparison

| Path | Config | Time | Cost/video |
|------|--------|------|------------|
| CPU (Fargate) | 16 workers | ~6 min | ~$0.13 |
| GPU (L4) | 1 stream | ~8.5 min | ~$0.07 |
| **GPU (L4)** | **3 streams** | **~2.8 min** | **~$0.02** |

GPU with parallelization is **6x cheaper** than CPU and **2x faster**.

### GPU Tier Selection (RunPod)

| GPU | Encoders | $/hr | Recommendation |
|-----|----------|------|----------------|
| L4 | 3 | ~$0.50 | **Best value** |
| L40S | 3 | ~$0.79 | Same encoders, costs more |
| RTX 6000 Ada | 3 | ~$0.74 | Same encoders, costs more |
| A40 | 1 | ~$0.35 | Only 1 encoder, no parallelization benefit |

**Use L4** - same 3 encoders as L40S at lower cost.

## 3. Architecture

### Current: Sequential Single-Stream

```
358 images → GPU process sequentially → 1 NVENC encoder → video.mp4
                                        (344 fps)
                                        ~8.5 min
```

### Target: 3 Parallel Segments

```
358 images
    ├── [0:119]   → Thread 1 → NVENC #1 → seg1.mp4
    ├── [119:238] → Thread 2 → NVENC #2 → seg2.mp4
    └── [238:358] → Thread 3 → NVENC #3 → seg3.mp4
                                              ↓
                               FFmpeg concat → final.mp4
                                              ↓
                               Supabase upload → URL
```

**Key insight:** Same pattern as CPU path (16 segments), just 3 segments for 3 encoders.

## 4. Implementation

### Handler Changes (~75 lines)

Location: `runpod-ken-burns-gpu/handler.py`

#### 4.1 Add Segment Processing Function

```python
def process_segment(
    segment_id: int,
    images: list[np.ndarray],
    image_dimensions: list[tuple],
    output_path: str,
    config: dict,
) -> dict:
    """Process a segment of images on GPU with dedicated NVENC encoder."""
    import threading
    thread_name = threading.current_thread().name
    print(f"[Segment {segment_id}] START on {thread_name}")

    # Pre-upload segment images to GPU
    gpu_images = []
    for img in images:
        gpu_mat = cv2.cuda_GpuMat()
        gpu_mat.upload(img)
        gpu_images.append(gpu_mat)

    # Create VideoWriter (claims 1 NVENC encoder)
    writer = cv2.cudacodec.createVideoWriter(
        output_path,
        (config["width"], config["height"]),
        cv2.cudacodec.H264,
        float(config["fps"])
    )
    print(f"[Segment {segment_id}] NVENC session opened")

    # Process frames (existing ken_burns_frame_gpu logic)
    frames_written = 0
    for i, gpu_image in enumerate(gpu_images):
        # ... Ken Burns frame generation ...
        writer.write(gpu_frame)
        frames_written += 1

    writer.release()
    print(f"[Segment {segment_id}] DONE - {len(images)} images, {frames_written} frames")

    return {
        "segment_id": segment_id,
        "output_path": output_path,
        "frames_written": frames_written,
    }
```

#### 4.2 Add Parallel Orchestration

```python
from concurrent.futures import ThreadPoolExecutor, as_completed

def generate_video_gpu_parallel(
    images: list[np.ndarray],
    output_dir: str,
    config: dict,
    num_streams: int = 3,
) -> dict:
    """Generate video using multiple NVENC encoders in parallel."""

    # Split images into segments
    segment_size = math.ceil(len(images) / num_streams)
    segments = []
    for i in range(num_streams):
        start = i * segment_size
        end = min(start + segment_size, len(images))
        if start < len(images):
            segments.append(images[start:end])

    print(f"[GPU] Split {len(images)} images into {len(segments)} segments")

    # Process segments in parallel
    segment_paths = []
    with ThreadPoolExecutor(max_workers=num_streams) as executor:
        futures = {
            executor.submit(
                process_segment,
                i,
                seg,
                [(img.shape[1], img.shape[0]) for img in seg],
                os.path.join(output_dir, f"segment_{i:02d}.mp4"),
                config,
            ): i
            for i, seg in enumerate(segments)
        }

        for future in as_completed(futures):
            result = future.result()
            segment_paths.append((result["segment_id"], result["output_path"]))

    # Sort by segment ID and concat
    segment_paths.sort(key=lambda x: x[0])
    ordered_paths = [p[1] for p in segment_paths]

    final_path = os.path.join(output_dir, "final.mp4")
    concat_segments(ordered_paths, final_path)

    return {"output_path": final_path}
```

#### 4.3 Add FFmpeg Concat

```python
import subprocess

def concat_segments(segment_paths: list[str], output_path: str):
    """Concatenate video segments using FFmpeg concat demuxer."""
    # Create concat list file
    list_path = output_path + ".txt"
    with open(list_path, "w") as f:
        for path in segment_paths:
            f.write(f"file '{path}'\n")

    # FFmpeg concat with stream copy (no re-encode)
    cmd = [
        "ffmpeg", "-y",
        "-f", "concat",
        "-safe", "0",
        "-i", list_path,
        "-c", "copy",
        output_path
    ]

    subprocess.run(cmd, check=True, capture_output=True)
    os.remove(list_path)

    # Clean up segment files
    for path in segment_paths:
        os.remove(path)
```

## 5. Validation

### 5.1 Check NVENC Session Count

```bash
# Run during processing
watch -n 0.5 nvidia-smi --query-gpu=encoder.stats.sessionCount --format=csv
```

Expected output during parallel processing:
```
encoder.stats.sessionCount
3
```

### 5.2 Timing Validation

```
Sequential (broken):
  [seg1] START
  [seg1] DONE - 170s
  [seg2] START
  [seg2] DONE - 170s
  [seg3] START
  [seg3] DONE - 170s
  Total: ~510s

Parallel (working):
  [seg1] START
  [seg2] START
  [seg3] START
  [seg2] DONE - 168s
  [seg1] DONE - 170s
  [seg3] DONE - 171s
  Total: ~175s
```

### 5.3 Log Pattern Verification

All segments should START within milliseconds of each other. If sequential, you'll see each segment complete before the next starts.

## 6. Deployment

### Docker Image

Use existing image: `mariuswilsch/opencv-cuda:4.9.0-cuda12.2.2-ubuntu22.04`

Contains:
- cv2.cuda.warpAffine
- cv2.cuda.addWeighted
- cv2.cudacodec.createVideoWriter
- FFmpeg for concat

### RunPod Configuration

- **GPU Type:** L4 (24GB tier)
- **Max Workers:** 1 (parallelization is internal)
- **Timeout:** 600s (10 min buffer for long videos)

### Rollback Strategy

Add environment variable:

```python
NUM_NVENC_STREAMS = int(os.environ.get("NUM_NVENC_STREAMS", "3"))

if NUM_NVENC_STREAMS == 1:
    result = generate_video_gpu(...)  # Original single-stream
else:
    result = generate_video_gpu_parallel(..., num_streams=NUM_NVENC_STREAMS)
```

Set `NUM_NVENC_STREAMS=1` to disable parallelization instantly.

## 7. References

- Handler location: `IITR-STAGING:/root/projects/runpod-ken-burns-gpu/handler.py`
- Docker image: `mariuswilsch/opencv-cuda:4.9.0-cuda12.2.2-ubuntu22.04`
- Issue: [#357 Ken Burns RunPod Serverless](https://github.com/DaveX2001/deliverable-tracking/issues/357)
- Related: [cudacodec-gpu-video-encoding.md](./cudacodec-gpu-video-encoding.md)
- Conversation: Dec 29, 2024

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

