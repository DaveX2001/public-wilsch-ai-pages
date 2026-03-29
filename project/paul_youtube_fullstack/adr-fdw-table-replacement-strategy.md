# ADR: FDW Table Replacement Strategy (v1/v2 Database Unification)
[[client-paul]]

---
publish: true
date: 2026-01-07
status: decided
type: adr
lifecycle: bridge (temporary until v1 retirement)
---

## Context

v1 (Flask/SQLAlchemy) and v2 (React/Supabase) have separate databases with incompatible schemas:

| Aspect | v1 Local PostgreSQL | v2 Supabase |
|--------|---------------------|-------------|
| Primary Keys | INTEGER (auto-increment) | UUID |
| Auth | Basic auth | Supabase RLS |
| Tables | Local tables | Cloud tables |

**Problem:** v1 video pipeline needs to read configuration data (templates, voices, prompts, etc.), but data is being managed in v2 Supabase. Currently works only because manually-seeded local data exists.

**Alternatives Considered:**

| Option | Impact |
|--------|--------|
| Change v1 to UUID | 46+ Flask routes, all FK columns, data migration |
| Change Supabase to INTEGER PKs | Breaks v2 frontend, RLS (auth.uid() is UUID) |
| Bridge columns | Supabase schema changes only, zero code changes |

## Decision

**Use bridge columns + FDW table replacement.**

1. Add bridge columns (`v1_*_id INTEGER`) to all Supabase tables
2. Align Supabase schema to match v1 expectations (column names, missing columns)
3. Drop v1 local tables, replace with foreign tables pointing to Supabase
4. Foreign table definitions map bridge column → `id`

### Why Bridge?

- Zero v1 code changes (production safe)
- Zero v2 frontend changes
- Single source of truth (Supabase)
- Translation layer handles INTEGER ↔ UUID

## Tables Affected

### Leaf Tables (5)

Each needs bridge column + schema alignment:

| Table | Bridge Column | Column Changes |
|-------|---------------|----------------|
| `voices` | `v1_voice_id` (ADD) | `audio_url` → `path_to_voice` |
| `image_styles` | `v1_image_style_id` (exists) | `name` → `title`, add `use_video_model`, `video_model` |
| `video_gen_templates` | `v1_video_gen_template_id` (exists) | Add `is_video_loop` |
| `prompt_configuration` | `v1_prompt_config_id` (exists) | None (schema matches) |
| `channels` | `v1_channel_id` (ADD if needed) | TBD |

### Templates Table (Special Case)

Templates has FKs to all other tables. Needs **both** UUID and INTEGER FK columns:

| Relation | UUID FK (v2 uses) | INTEGER FK (v1 uses) |
|----------|-------------------|----------------------|
| voice | `voice_id` | `v1_voice_id` |
| image_style | `image_style_id` | `v1_image_style_id` |
| video_gen_template | `video_gen_template_id` | `v1_video_gen_template_id` |
| channel | `channel_id` | `v1_channel_id` |
| youtube prompt | `youtube_prompt_config_id` | `prompt_configuration` |
| title prompt | `title_gen_prompt_config_id` | `title_prompt_configuration` |

**Total: 6 UUID + 6 INTEGER = 12 FK columns**

Plus missing v1 columns: `voice_name`, `footage_type`, `voice_speed`, `skip_image_processing`, `script_gen_type`, `channelurl`, `statistics`

## Implementation

### Phase 1: Schema Alignment

For each table, add to Supabase:
- Bridge column (`v1_*_id INTEGER`)
- Missing columns from v1 schema
- Rename mismatched columns (or handle in FDW)

### Phase 2: v2 CRUD Updates

When creating/updating records, populate both:
```typescript
// Example: saving template
voice_id: selectedVoice.id,              // UUID for v2
v1_voice_id: selectedVoice.v1_voice_id,  // INTEGER for v1
```

### Phase 3: FDW Table Replacement

In v1 local PostgreSQL:

```sql
-- 1. Drop local table
DROP TABLE voice;

-- 2. Create foreign table with column mapping
CREATE FOREIGN TABLE voice (
  id INTEGER OPTIONS (column_name 'v1_voice_id'),
  name TEXT,
  path_to_voice TEXT OPTIONS (column_name 'path_to_voice'),
  language TEXT
) SERVER supabase_fdw
OPTIONS (schema_name 'public', table_name 'voices');
```

### Phase 4: Verify Video Creation

1. v2 frontend passes `v1_template_id` (INTEGER) to v1 API
2. v1 queries `Template.query.get(template_id)` - hits foreign table
3. v1 follows INTEGER FKs to other foreign tables
4. Video pipeline works unchanged

## Schema Mismatch Reference

### voice
```
Local v1:                    Supabase (after changes):
id INTEGER PK         →      v1_voice_id INTEGER (bridge)
name TEXT             →      name TEXT
path_to_voice TEXT    →      path_to_voice TEXT (renamed from audio_url)
language TEXT         →      language TEXT
```

### image_style
```
Local v1:                    Supabase (after changes):
id INTEGER PK         →      v1_image_style_id INTEGER (exists)
title TEXT            →      title TEXT (renamed from name)
prompt TEXT           →      prompt TEXT
image_model TEXT      →      image_model TEXT
rule_* INTEGER        →      rule_* INTEGER
use_video_model BOOL  →      use_video_model BOOLEAN (ADD)
video_model TEXT      →      video_model TEXT (ADD)
```

### video_gen_template
```
Local v1:                    Supabase (after changes):
id INTEGER PK         →      v1_video_gen_template_id INTEGER (exists)
name TEXT             →      name TEXT
json_data JSONB       →      json_data JSONB
is_video_loop BOOL    →      is_video_loop BOOLEAN (ADD)
```

### prompt_configuration
```
Local v1:                    Supabase:
id INTEGER PK         →      v1_prompt_config_id INTEGER (exists)
All other columns     →      Match exactly
```

## Consequences

**Positive:**
- Single source of truth (Supabase)
- v1 and v2 read same data
- Zero v1 code changes
- RLS preserved for v2

**Negative:**
- Dual FK columns in templates (complexity)
- Must populate bridge columns in v2 CRUD
- FDW adds network latency to v1 queries

**Risks:**
- If Supabase down, v1 video pipeline fails
- Bridge column population must be consistent

## Related

- [ADR: v1→Supabase Storage Bridge](adr-v1-supabase-storage-bridge.md) - File storage bridge
- [Supabase Migration Framework](supabase-migration-framework.md)
- [Strangler Pattern Sequencing](adr-strangler-pattern-sequencing.md)
- Issue #446: v2 Data Independence
- Issue #421: v2-v1 Integration (FDW setup)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

