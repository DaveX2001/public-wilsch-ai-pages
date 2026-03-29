---
publish: true
---

# OpenCV cudacodec: GPU Video Encoding with NVENC
[[client-paul]]

## Overview

OpenCV's `cudacodec` module provides direct GPU video encoding via NVIDIA's NVENC hardware encoder. This enables single-pass GPU pipelines where frames never leave GPU memory - from transformation through encoding.

**When to use:**
- High-volume video generation (100+ videos/day)
- Ken Burns pipelines with GPU transforms
- Any workflow currently using CPU encoding + FFmpeg re-encode

**Key benefit:** Eliminates double encoding (CPU mp4v → FFmpeg NVENC) by encoding directly on GPU.

## Problem It Solves

### Before (CPU Segment Pipeline)
```
frames → CPU warpAffine → CPU addWeighted → CPU mp4v encode → segment files
                                                                    ↓
final.mp4 ← FFmpeg NVENC encode ← FFmpeg decode ← FFmpeg concat ← disk
```

**Pain points:**
- Double encoding (mp4v then NVENC)
- 16 intermediate segment files (I/O overhead)
- CPU↔disk↔CPU↔GPU transfers

### After (GPU Single-Pass Pipeline)
```
frames → GPU warpAffine → GPU addWeighted → GPU cudacodec NVENC → video.mp4
                                                                      ↓
                                            final.mp4 ← FFmpeg audio mux (~2s)
```

**Improvements:**
- Single encode pass (direct NVENC)
- No intermediate files
- Data stays on GPU memory throughout

## Prerequisites

### Required
- NVIDIA GPU with NVENC support (GTX 10xx+, RTX series)
- NVIDIA Video Codec SDK **12.1.14** (not 12.2 - has compatibility issues)
- Custom OpenCV build with `-DWITH_NVCUVID=ON`

### Docker Image (Pre-built)
**Location:** `IITR-STAGING:~/opencv-cuda-build/`
**Image:** `opencv-cuda:4.9.0-cuda12.2.2-ubuntu22.04` (9.1GB)

**Contains:**
- OpenCV 4.9.0 with CUDA 12.2.2
- `cv2.cuda.warpAffine`
- `cv2.cuda.addWeighted`
- `cv2.cudacodec.createVideoWriter` (NVENC H264/HEVC)

## API Usage

### Correct Usage
```python
import cv2
import numpy as np

# Create GPU video encoder - use cudacodec codec constant!
writer = cv2.cudacodec.createVideoWriter(
    "output.mp4",
    (1920, 1080),
    cv2.cudacodec.H264,  # or cv2.cudacodec.HEVC
    30.0
)

# Process frames on GPU
image = cv2.imread("image.jpg")
gpu_image = cv2.cuda_GpuMat()
gpu_image.upload(image)

# Ken Burns transform (stays on GPU)
M = np.float32([[1.02, 0, -19.2], [0, 1.02, -10.8]])  # 2% zoom
gpu_frame = cv2.cuda.warpAffine(gpu_image, M, (1920, 1080))

# Encode directly from GPU memory
writer.write(gpu_frame)

writer.release()
```

### Common Mistake (Will Fail!)
```python
# WRONG - fourcc doesn't work with cudacodec
writer = cv2.cudacodec.createVideoWriter(
    "output.mp4",
    (1920, 1080),
    cv2.VideoWriter.fourcc('H', '2', '6', '4'),  # ❌ FAILS
    30.0
)
```

**Error:** `Unknown codec: cudacodec::VideoWriter only supports CODEC_VW::H264 and CODEC_VW::HEVC`

### Available Codec Constants
- `cv2.cudacodec.H264` - H.264/AVC encoding
- `cv2.cudacodec.HEVC` - H.265/HEVC encoding
- `cv2.cudacodec.H264_SVC` - Scalable Video Coding
- `cv2.cudacodec.H264_MVC` - Multiview Video Coding

## Performance Characteristics

### Benchmark Results (RTX 4000 Ada)
| Metric | Value |
|--------|-------|
| FPS | 377.6 |
| ms/frame | 2.65 |
| Throughput | ~31,770 frames in 1.4 min |

### Pipeline Comparison (353 images, 1080p)
| Pipeline | Time | Speedup |
|----------|------|---------|
| Main (FFmpeg zoompan) | ~33 min | baseline |
| Issue-349 (CPU segments) | ~3-5 min | 6-10x |
| **GPU cudacodec** | **~1.4 min** | **~23x** |

### Why It's Faster
1. **No double encoding** - Direct NVENC vs mp4v→decode→NVENC
2. **No intermediate files** - Single-pass, no 16 segment files
3. **Data stays on GPU** - No CPU↔GPU memory transfers during processing
4. **Dedicated hardware** - NVENC runs parallel to CUDA compute cores

## Reproduction Commands

### 1. Verify Docker Image Exists
```bash
ssh IITR-STAGING "docker images | grep opencv-cuda"
# Expected: opencv-cuda 4.9.0-cuda12.2.2-ubuntu22.04
```

### 2. Quick Validation Test
```bash
ssh IITR-STAGING 'docker run --rm --gpus all opencv-cuda:4.9.0-cuda12.2.2-ubuntu22.04 python3 -c "
import cv2
import numpy as np

writer = cv2.cudacodec.createVideoWriter(\"/tmp/test.mp4\", (1920, 1080), cv2.cudacodec.H264, 30.0)
for i in range(30):
    frame = np.zeros((1080, 1920, 3), dtype=np.uint8)
    gpu_frame = cv2.cuda_GpuMat()
    gpu_frame.upload(frame)
    writer.write(gpu_frame)
writer.release()
print(\"SUCCESS: cudacodec.VideoWriter works!\")
"'
```

### 3. Full Pipeline Benchmark
```bash
ssh IITR-STAGING 'docker run --rm --gpus all opencv-cuda:4.9.0-cuda12.2.2-ubuntu22.04 python3 -c "
import cv2, numpy as np, time

width, height, fps = 1920, 1080, 30.0
num_images, frames_per_image, transition_frames = 10, 90, 22

gpu_images = []
for _ in range(num_images):
    gm = cv2.cuda_GpuMat()
    gm.upload(np.random.randint(0, 255, (height, width, 3), dtype=np.uint8))
    gpu_images.append(gm)

writer = cv2.cudacodec.createVideoWriter(\"/tmp/bench.mp4\", (width, height), cv2.cudacodec.H264, fps)
M = np.float32([[1.02, 0, -19.2], [0, 1.02, -10.8]])
total_frames = 0

start = time.perf_counter()
for i, gpu_img in enumerate(gpu_images):
    main = frames_per_image - (transition_frames if i < len(gpu_images)-1 else 0)
    for _ in range(main):
        writer.write(cv2.cuda.warpAffine(gpu_img, M, (width, height)))
        total_frames += 1
    if i < len(gpu_images) - 1:
        for t in range(transition_frames):
            alpha = t / transition_frames
            a = cv2.cuda.warpAffine(gpu_img, M, (width, height))
            b = cv2.cuda.warpAffine(gpu_images[i+1], M, (width, height))
            writer.write(cv2.cuda.addWeighted(a, 1-alpha, b, alpha, 0))
            total_frames += 1
writer.release()
elapsed = time.perf_counter() - start

print(f\"FPS: {total_frames/elapsed:.1f}, Projected 353 images: {elapsed * 35.3 / 60:.1f} min\")
"'
```

## Integration Notes

### Files to Modify
- `paul_template_generation/backend/scripts/opencv_ken_burns_transitions.py`
  - Replace `cv2.warpAffine` → `cv2.cuda.warpAffine`
  - Replace `cv2.addWeighted` → `cv2.cuda.addWeighted`
  - Replace `cv2.VideoWriter` → `cv2.cudacodec.createVideoWriter`

### FFmpeg Still Needed For
- Audio muxing: `ffmpeg -i video.mp4 -i audio.wav -c:v copy -c:a aac final.mp4`
- Overlays and subtitles (if not moved to GPU)
- This step is fast (~2 seconds) since video is stream-copied

### Rollback Strategy
- Keep CPU pipeline code behind feature flag
- Environment variable: `OPENCV_GPU_ENCODE=true/false`
- Can switch back instantly if issues arise

## Build Notes (If Rebuilding Image)

### NVIDIA Video Codec SDK
- Download from: https://developer.nvidia.com/video-codec-sdk-archive
- Version: **12.1.14** (12.2 has OpenCV compatibility issues)
- Requires free NVIDIA Developer account

### Key CMake Flags
```cmake
-DWITH_CUDA=ON
-DWITH_NVCUVID=ON      # Enables cudacodec (VideoReader + VideoWriter)
-DCUDA_ARCH_BIN=8.9    # RTX 4000 Ada compute capability
-DOPENCV_EXTRA_MODULES_PATH=/path/to/opencv_contrib/modules
```

### Build Directory
`IITR-STAGING:~/opencv-cuda-build/` contains:
- `Dockerfile` - Complete build configuration
- `Video_Codec_SDK_12.1.14.zip` - NVIDIA SDK
- `test_cudacodec_videowriter.py` - Validation script

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

