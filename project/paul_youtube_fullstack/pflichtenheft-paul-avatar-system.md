---
publish: true
---

# Pflichtenheft: Paul Avatar System
[[client-paul]]

---

## 1. Cover Page

| | |
|---|---|
| **Project** | Paul Avatar System |
| **Client** | nuca systems |
| **Contractor** | Wilsch AI Services OÜ |

---

## 2. Goal Determination

### Must (Contract Deliverables)

**Phase 1: Speed + Feasibility Spike**
- Deploy EchoMimicV3 on Modal → working endpoint
- Deploy HunyuanVideo-Avatar on Modal → comparison benchmark (conditional on EU licensing resolution)
- Build portrait preprocessing pipeline → pose-alignment automation
- Create input interface → API accepting audio + portrait image
- Run benchmark using real project audio → timing + quality comparison
- Test background removal (Matanyone) → transparent avatar video output
- Go/No-Go evaluation with Paul based on empirical results

**Phase 2: Quality + Design**
- Portrait spec discovery → optimal image format/size for each model
- Quality review → send avatar snippets to Paul for approval
- Integration architecture design → define how avatar fits into existing pipeline

**Phase 3: Integration + Deployment**
- Create avatar service (Modal for dev → RunPod for production)
- Pipeline integration → avatar generation parallel to video segments after TTS
- FFmpeg overlay → composite avatar using existing 11 position presets
- Video overlay extension → extend existing image overlay system to support video overlays
- Portrait upload UI → user-facing interface for avatar image management
- Parallelization (if needed for performance)

### Deferred (Future Contracts)

- Multi-channel persona management UI (beyond single portrait upload)
- Advanced composition templates beyond existing 11 presets
- Multi-language audio support
- Performance optimization beyond initial parallelization

### Out of Scope

- HeyGen commercial API integration (cost prohibitive at scale: ~$500/month at 100 videos)
- Rebuilding existing overlay/template infrastructure (extend, don't rebuild)
- Real-time interactive avatar features
- Avatar forcing/live reaction capabilities

---

## 3. Functional Requirements

### Must (Contract Deliverables)

**Phase 1: Speed + Feasibility Spike**

- **FR-01:** System shall deploy EchoMimicV3 model on Modal with API endpoint accepting audio WAV and portrait image inputs
- **FR-02:** System shall deploy HunyuanVideo-Avatar on Modal for quality benchmarking (conditional on EU licensing; auto-exclude if legally blocked)
- **FR-03:** System shall provide portrait preprocessing pipeline that automatically handles pose alignment for input images
- **FR-04:** System shall generate benchmark metrics (processing time, quality assessment) using real project audio files
- **FR-05:** System shall integrate Matanyone for background removal, producing transparent avatar video suitable for overlay
- **FR-06:** System shall support Go/No-Go decision gate based on empirical benchmark results presented to Paul

**Phase 2: Quality + Design**

- **FR-07:** System shall discover optimal portrait specifications (format, resolution, aspect ratio) for each model variant
- **FR-08:** System shall generate avatar video snippets for Paul's quality approval before full integration
- **FR-09:** System shall produce integration architecture document defining avatar service position in existing pipeline

**Phase 3: Integration + Deployment**

- **FR-10:** System shall deploy avatar service on RunPod for production workloads
- **FR-11:** System shall integrate avatar generation to run parallel with video segment generation after TTS completion
- **FR-12:** System shall composite avatar video onto slideshow using existing FFmpeg overlay system with 11 position presets (corners, edges, center)
- **FR-13:** System shall extend existing `video_pipeline_cpu.py` overlay function to support video overlays (not just static images)
- **FR-14:** System shall provide user-facing portrait upload interface for avatar image management
- **FR-15:** Users shall be able to select avatar position from existing preset options (bottom-right, bottom-left, top-right, top-left, center, etc.)
- **FR-16:** Each channel/user shall have independent avatar persona (per-user, not bound to service)

### Deferred (Future Contracts)

- **FR-17:** System shall provide multi-channel persona management UI for users with multiple channels
- **FR-18:** System shall support custom composition templates beyond existing 11 presets

---

## 4. Non-Functional Requirements

### Performance

- Avatar generation processing time shall be measured empirically in Phase 1; Go/No-Go threshold determined by Paul based on benchmark results
- System shall support parallelization if sequential processing exceeds acceptable turnaround

### Security

- User-uploaded portrait images shall be stored securely with access limited to owning user
- API endpoints shall require authentication consistent with existing Paul system

### Compatibility

- Avatar service shall integrate with existing video pipeline (`video_pipeline_cpu.py`)
- Avatar overlay shall use existing FFmpeg compositing infrastructure
- Portrait upload UI shall integrate with existing v2 frontend (React + Supabase)

### Usability

- Avatar position selection shall use existing 11 preset options (no custom coordinate input required)
- Portrait upload shall accept standard image formats (JPG, PNG)

---

## 5. Acceptance Criteria

| Requirement | Acceptance Test | Expected Outcome |
|-------------|-----------------|------------------|
| FR-01 | Given audio WAV and portrait image, when API called, then avatar video generated | Working endpoint returns MP4 |
| FR-02 | Given EU licensing status, when blocked, then HunyuanVideo-Avatar auto-excluded | Conditional deployment works |
| FR-05 | Given avatar video with background, when Matanyone applied, then transparent output | Clean alpha channel on avatar |
| FR-06 | Given benchmark results, when presented to Paul, then Go/No-Go decision made | Decision documented |
| FR-08 | Given avatar snippets sent to Paul, when reviewed, then quality approved or rejected | Paul sign-off obtained |
| FR-12 | Given slideshow + avatar video, when FFmpeg overlay applied, then composite renders correctly | Avatar visible at selected position |
| FR-13 | Given video overlay in overlay array, when pipeline runs, then video composited frame-by-frame | Video overlay works like image overlay |
| FR-14 | Given user uploads portrait, when saved, then portrait available for avatar generation | Portrait persisted and retrievable |
| FR-16 | Given two users on different channels, when each uploads portrait, then each has independent avatar | Per-user isolation verified |

---

## 6. Technical Constraints

### Deployment Environment

- **Development/Spike:** Modal (serverless GPU)
- **Production:** RunPod (serverless GPU)
- **Existing Infrastructure:** Current pipeline on WILSCH-AI-SERVER

### Required Integrations

- **TTS System:** Chatterbox (provides audio input for avatar)
- **Video Pipeline:** `video_pipeline_cpu.py` (OpenCV-based compositing)
- **Overlay System:** Existing `apply_overlays()` function with 11 position presets
- **FFmpeg:** Stream copy + overlay compositing
- **Background Removal:** Matanyone model
- **Frontend:** React + Supabase (v2 stack)

### Technology Stack Decisions

| Component | Technology | Notes |
|-----------|------------|-------|
| Primary Avatar Model | EchoMimicV3 | 12GB VRAM (quantized), Apache 2.0 license |
| Comparison Model | HunyuanVideo-Avatar | 10GB mode, conditional on EU licensing |
| Background Removal | Matanyone | Transparent video output |
| GPU Platform (Dev) | Modal | Serverless, pay-per-use |
| GPU Platform (Prod) | RunPod | Serverless endpoints |
| Video Compositing | OpenCV + FFmpeg | Extend existing pipeline |

### Model Constraints

- EchoMimicV3: Generates ~4.3s video per inference → 10-min video requires ~140 sequential generations
- HunyuanVideo-Avatar: EU licensing restriction may block production use (benchmark only)
- Both models output full-frame video → post-processing required for positioning

---

## 7. Data Model

### Entities

| Entity | Key Fields | Relationships |
|--------|------------|---------------|
| Avatar Portrait | id, user_id, image_path, created_at | belongs_to User |
| Avatar Config | id, user_id, position_preset, opacity | belongs_to User |
| Video Process | id, avatar_enabled, avatar_portrait_id | has_one Avatar Portrait |

### Avatar Position Presets (Existing)

```
center, left, right,
top-left, top-right, top-center,
bottom-left, bottom-right, bottom-center,
left-center, right-center
```

---

## 8. Domain Glossary

| Term | Definition |
|------|------------|
| EchoMimicV3 | Open-source audio-driven avatar model from Ant Group (Apache 2.0 license) |
| HunyuanVideo-Avatar | Tencent's avatar model with EU licensing restrictions |
| Matanyone | Background removal model producing transparent video output |
| Portrait Preprocessing | Automated pose alignment and formatting of input images for avatar models |
| Video Overlay | Extension of existing image overlay system to support video compositing frame-by-frame |
| Go/No-Go Gate | Decision point after Phase 1 benchmarking to continue or pivot based on results |

---

## 9. Evaluation Infrastructure

### Benchmark Dataset

- **Source:** Real project audio files from existing video pipeline
- **Format:** WAV files from Chatterbox TTS output
- **Representative Samples:** 10-minute and 1-hour video audio tracks

### Success Metrics

| Metric | Threshold | Notes |
|--------|-----------|-------|
| Processing Time | Empirical (Paul decides) | Measured in Phase 1, no hard threshold upfront |
| Lip-sync Quality | Paul approval | Subjective quality gate in Phase 2 |
| Visual Artifacts | None visible | Paul approval required |
| Background Removal | Clean alpha channel | Transparent output without artifacts |

### Fallback Strategy

- **If both models fail quality bar:** Pivot to alternative models (additional scope)
- **If HunyuanVideo-Avatar legally blocked:** Proceed with EchoMimicV3 only (conditional clause)

### Input/Output Specifications

**Input:**
- Audio: WAV format from TTS (any duration)
- Portrait: JPG/PNG, user-provided AI avatar image

**Output:**
- Avatar Video: MP4 with transparent background (after Matanyone)
- Final Video: Slideshow + avatar composite at selected position preset

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

