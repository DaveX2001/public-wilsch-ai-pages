# v2 Wizard Architecture Snapshot
[[client-paul]]

---
publish: true
date: 2026-01-04
type: snapshot
status: may-change
source: rubber-duck session #380
---

> **Snapshot Notice:** This document captures architecture decisions from a specific point in time. Decisions may evolve as implementation progresses.

## 1. Data Flow Philosophy

- **v2 owns user data:** Frontend writes config directly to Supabase
- **v1 as backend service:** v2 calls v1 API for GPU processing (RunPod)
- **FDW bridge:** v1 reads Supabase tables directly via postgres_fdw - no v1 code changes

## 2. Step Structure (Data-Driven)

- **Step 3 renamed "Configuration":** Collects Voice + ImageStyle + VideoGenTemplate + Template
- **Timing constraint:** All config must be selected BEFORE its pipeline stage runs
- **Step 4 narrowed:** Now just image demo/preview - config selection moved to Step 3

## 3. v2 Frontend Features Status

| Feature | Exists | Path |
|---------|--------|------|
| channel-management | ✅ | `frontend/src/features/channel-management/` |
| create-video | ✅ | `frontend/src/features/create-video/` |
| image-style | ✅ | `frontend/src/features/image-style/` |
| queue | ✅ | `frontend/src/features/queue/` |
| voice | ✅ | `frontend/src/features/voice/` |
| Template | ❌ | Not built |
| VideoGenTemplate | ❌ | Not built |
| PromptConfiguration | ❌ | Form-only, not persisted |

## 4. Entity-to-Step Matrix

| Entity | Step 1 | Step 2 | Step 3 | Step 4 | Step 5 | Tier One |
|--------|:------:|:------:|:------:|:------:|:------:|:--------:|
| channels | ✓ | — | — | — | ✓ | ✅ IN |
| video_process | ✓ | ✓ | ✓ | ✓ | ✓ | ✅ IN |
| voices | — | — | ✓ | — | — | ✅ select |
| image_styles | ✓ | — | — | ✓ | — | ✅ IN |
| templates | ✓ | ✓ | ✓ | ✓ | — | ✅ select |
| video_gen_templates | ✓ | — | — | — | ✓ | ✅ select |
| prompt_configurations | ✓ | ✓ | — | — | — | ✅ optional |

## 5. Entity Status vs Tier One Scope

| Entity | In Supabase | v2 UI | Tier One Need |
|--------|-------------|-------|---------------|
| channels | ✅ | ✅ | Unlimited |
| video_process | ✅ | ✅ | Core workflow |
| voices | ✅ | ✅ | Select only (~16 predefined) |
| image_styles | ✅ | ✅ | Predefined + custom |
| templates | ❌ | ❌ | Select only (predefined) |
| video_gen_templates | ❌ | ❌ | Select only (predefined) |
| prompt_configurations | ❌ | Form only | Optional (use defaults) |

## 6. FDW Pattern

- **Direction:** v1 PostgreSQL reads FROM Supabase (not the reverse)
- **Connection:** Session pooler `aws-1-eu-central-1.pooler.supabase.com`, SSL required
- **Username format:** `postgres.{projectref}` (e.g., `postgres.owftrowgzjlkjjtraici`)
- **Validated:** Spike successful on local PostgreSQL

## 7. v2→v1 Integration

- **No FastAPI needed:** v1 Flask already has RunPod integration
- **Flow:** v2 frontend → v1 `/api/video/create` → returns `process_id`
- **ID mapping:** Store `{supabase_video_id, v1_process_id}` in Supabase for tracking

### v1 Template Requirement (Critical Constraint)

**Discovery:** v1 REQUIRES `template_id` (integer). All configuration flows through the template relationship.

| v1 Behavior | Implication |
|-------------|-------------|
| `template_id` required | Processing silently fails without it |
| Config via template only | voice_id, image_style_id, prompts all read from template |
| No direct config fields | VideoProcess has no voice_id/image_style_id columns in v1 |

**Tier One Solution (predefined templates):**
- Simple mapping: v2 preset UUID → v1 template_id (integer)
- Zero v1 code changes needed
- Works because template set is fixed and known

**Future Tier Constraint (if templates become customizable):**

| Scenario | Problem | Solution Required |
|----------|---------|-------------------|
| Custom template in v2 | No v1 record exists | Architectural investment |
| Edit template in v2 | v1 record out of sync | Same |

**Options if templates become non-fixed:**
1. `templates_fdw` + v1 code changes to read via FDW
2. Sync mechanism to auto-create v1 templates from v2
3. Translation layer that creates v1 template on-demand

**Source:** Investigation of `paul_queue/app/main.py:3872-4139` and `models.py`

## 8. Issue Structure

- **#380 Update:** Step 4 = image demo only (config moved to Step 3)
- **New issue:** Step 3 Expansion - UI + Supabase tables for Template/VideoGenTemplate
- **New issue:** FDW + v2→v1 Integration - infrastructure connecting systems
- **Close #411:** LLM Migration not needed (v1 handles LLM via existing endpoints)

## 9. Key Architectural Decisions

**Autopilot Modes:**
- Both ON and OFF need the SAME entities - mode doesn't change dependencies
- Autopilot just changes orchestration, not data requirements

**Template Abstraction:**
- v1 uses Template indirection (Template → Voice, ImageStyle, etc.)
- v2 currently wizard-only (config stored directly on video_process)
- For Tier One: Introduce Template as "select only" presets

**UI Placement:**
- All config selection (Voice, ImageStyle, Template, VideoGenTemplate) → Step 3 "Configuration"
- Reference implementation: Voice selection (`Step3Voice.tsx`, `VoiceSelectionDialog.tsx`)

**Database Sync Pattern:**
- Chose FDW over: dual-write, sync worker, CDC
- Reason: Zero v1 code changes, v1 queries unchanged, real-time reads

**No FastAPI Layer:**
- v1 Flask already has RunPod integration for TTS, ImageGen
- FastAPI would be unnecessary middleman for VideoCreate scope
- YouTube OAuth is out of scope for now

## References

- [Strangler Pattern ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/adr-strangler-pattern-sequencing)
- [Light Tier Scope](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/light-tier-v2-feature-scope)
- [Unified Script Step ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/adr-wizard-unified-script-step)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

