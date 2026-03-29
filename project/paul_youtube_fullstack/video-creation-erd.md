---
publish: true
---

# Video Creation ERD (v2 Target State)
[[client-paul]]

**Purpose:** Complete schema for video creation flow in v2 Supabase.

**Last Updated:** 2026-01-04

---

## Entity Relationship Diagram

```mermaid
erDiagram
    templates ||--o| voices : "has voice"
    templates ||--o| image_styles : "has style"
    templates ||--o| video_gen_templates : "has video template"
    templates ||--o| prompt_configurations : "has script prompts"
    templates ||--o| prompt_configurations : "has title prompts"
    templates ||--o| channels : "belongs to"

    video_process ||--o| templates : "uses template"
    video_process ||--o| channels : "for channel"
    video_process ||--o| voices : "uses voice"
    video_process ||--o| script_assignments : "from assignment"

    channels {
        uuid id PK
        text name
        text youtube_channel_id
        text display_name
        boolean is_active
        timestamptz created_at
        timestamptz updated_at
    }

    voices {
        uuid id PK
        text name
        text language
        text description
        text audio_url
    }

    image_styles {
        uuid id PK
        uuid user_id FK
        text name
        text description
        text prompt
        text image_url
        text image_model
        integer rule_0_30
        integer rule_30_60
        integer rule_60_600
        integer rule_600_3600
        integer rule_3600_plus
        boolean is_predefined
        timestamptz created_at
        timestamptz updated_at
    }

    video_gen_templates {
        uuid id PK
        text name
        jsonb json_data
        boolean is_predefined
        timestamptz created_at
        timestamptz updated_at
    }

    prompt_configurations {
        uuid id PK
        text title
        text role_prompt
        text script_structure
        text tone_style
        text retention_techniques
        text additional_information
        text type
        boolean is_predefined
        timestamptz created_at
        timestamptz updated_at
    }

    templates {
        uuid id PK
        text name
        uuid voice_id FK
        uuid image_style_id FK
        uuid video_gen_template_id FK
        uuid channel_id FK
        uuid prompt_configuration_id FK
        uuid title_prompt_configuration_id FK
        text footage_type
        numeric voice_speed
        boolean skip_image_processing
        text script_gen_type
        boolean is_predefined
        timestamptz created_at
        timestamptz updated_at
    }

    video_process {
        uuid id PK
        uuid template_id FK
        uuid channel_id FK
        uuid voice_id FK
        uuid assigned_script_id FK
        text title
        text thumbnail_title
        text script_type
        text video_url
        text script_file_url
        text description
        text preset_name
        text llm_model
        boolean autopilot
        boolean auto_upload
        boolean preview_enabled
        boolean require_thumbnail
        integer current_step
        text status
        boolean skip_image_processing
        boolean enable_llm
        text enhanced_script_url
        text generated_script_url
        text raw_transcript_url
        numeric voice_speed
        integer v1_process_id
        timestamptz created_at
        timestamptz updated_at
    }

    script_assignments {
        uuid id PK
        uuid script_id FK
        uuid user_id FK
        text status
        timestamptz assigned_at
        timestamptz started_at
        timestamptz completed_at
        text notes
        text file_path
        text original_filename
    }
```

---

## Table Summary

| Table | Purpose | Tier One Scope |
|-------|---------|----------------|
| **channels** | YouTube channel config | Unlimited, full CRUD |
| **voices** | TTS voice presets | Select only (~16 predefined) |
| **image_styles** | Image generation config | Predefined + custom CRUD |
| **video_gen_templates** | Video rendering config | Select only (predefined) |
| **prompt_configurations** | LLM prompt templates | Select only (predefined) |
| **templates** | Config bundle (hub) | Select only (predefined) |
| **video_process** | Video job records | Full CRUD |
| **script_assignments** | Script workflow | NOT IN Tier One |

---

## Key Relationships

### Templates as Hub
Templates bundles configuration from 6 other tables:
- `voice_id` → voices
- `image_style_id` → image_styles
- `video_gen_template_id` → video_gen_templates
- `prompt_configuration_id` → prompt_configurations (script prompts)
- `title_prompt_configuration_id` → prompt_configurations (title prompts)
- `channel_id` → channels

### video_process Flexibility
video_process can reference:
- `template_id` → Use template config (preset mode)
- OR direct FKs (`voice_id`, `channel_id`) → Wizard mode

This supports both template-based and wizard-based video creation.

---

## FDW Integration

For v1 backend to read v2 data via postgres_fdw:

| Foreign Table | Source | Purpose |
|---------------|--------|---------|
| voices_fdw | voices | TTS voice lookup |
| image_styles_fdw | image_styles | Image generation config |
| templates_fdw | templates | Full config bundle |
| video_gen_templates_fdw | video_gen_templates | Video rendering config |

---

## v1 Integration Constraint

**Critical:** v1 backend REQUIRES `template_id` (integer). All config flows through the template relationship.

| v1 Field Access | Source |
|-----------------|--------|
| voice_id, voice_speed | template.voice_id, template.voice_speed |
| image_style_id | template.image_style_id |
| prompt configs | template.prompt_configuration, template.title_prompt_configuration |

**v1 VideoProcess has NO direct config fields** - only template relationship.

**Tier One:** Simple mapping (v2 preset UUID → v1 template_id integer) works because templates are predefined/fixed.

**Future constraint:** If templates become customizable, requires architectural investment (FDW + v1 code changes, or sync mechanism).

---

## ID Strategy

All tables use **UUID** primary keys:
- Generated via `gen_random_uuid()`
- Enables distributed ID generation
- Compatible with Supabase Auth (`auth.users.id`)

---

## Source References

- [Tier One Feature Scope](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/light-tier-v2-feature-scope)
- [v2 Wizard Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/v2-wizard-architecture-snapshot-2026-01-04)
- Issue: DaveX2001/deliverable-tracking#421

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

