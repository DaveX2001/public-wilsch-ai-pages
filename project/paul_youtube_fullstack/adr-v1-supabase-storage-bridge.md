# ADR: v1→Supabase Storage Bridge
[[client-paul]]

---
publish: true
date: 2026-01-04
status: decided
type: adr
lifecycle: bridge (temporary)
---

## Context

v2 Queue View (#414) needs access to completed videos for playback and download. Current state:
- **v1** stores generated files on nuca-systems filesystem (`{path_to_folder}/output/final.mp4`)
- **v2 frontend** is deployed separately, cannot access nuca-systems filesystem directly
- Options analyzed: (A) API proxy through v1, (B) Upload to Supabase Storage, (C) Signed URLs

## Decision

**Use Supabase Storage with v1 upload hook.**

v1 uploads `final.mp4` to Supabase Storage after video generation completes. v2 frontend accesses files directly via Supabase SDK with RLS enforcement.

### Why Not Proxy?

| Factor | Proxy | Storage |
|--------|-------|---------|
| Effort | ~1-1.5 hr | ~1 hr |
| v1 runtime dependency | Required | Not required |
| CDN benefits | None | Yes |
| Architecture cleanliness | Coupling | Clean separation |

Similar effort, but Storage is architecturally cleaner and aligns with "v2 owns data" principle.

## Storage Structure

```
Bucket: video-assets

Path: {user_id}/videos/{video_process_id}.mp4

Example:
video-assets/a1b2c3d4-uuid/videos/x9y8z7-uuid.mp4
```

**Why user_id prefix:** Enables simple RLS policy based on path. User isolation via `path[1] = auth.uid()`.

## Schema Changes

```sql
ALTER TABLE video_process ADD COLUMN final_video_url TEXT;
ALTER TABLE video_process ADD COLUMN video_ready BOOLEAN DEFAULT false;
ALTER TABLE video_process ADD COLUMN finished_at TIMESTAMPTZ;
```

## RLS Policy

```sql
CREATE POLICY "Users access own videos"
ON storage.objects FOR SELECT
USING (
  bucket_id = 'video-assets' AND
  (storage.foldername(name))[1]::uuid = auth.uid()
);
```

## v1 Hook Implementation

**Location:** `paul_queue/app/main.py` line ~769, after `call_make_video()` completes.

**Data available:**
- `process_id` (v1 integer)
- `custom_path_to_folder` (filesystem path)
- Can query Supabase for `video_process.id` and `channels.user_id`

**Hook flow:**
1. Query Supabase: `video_process` → `channel` → `user_id`
2. Read `{folder}/output/final.mp4` from filesystem
3. Upload to `{user_id}/videos/{video_process_id}.mp4`
4. Update `video_process.final_video_url`, `video_ready=true`, `finished_at=now()`

**Estimated effort:** ~40 lines new code, 1 new file (`utils/supabase_upload.py`)

## Lifecycle: Bridge Solution

```
NOW:    v1 generates → uploads to Supabase Storage → v2 reads
LATER:  v2 FastAPI generates → writes directly to Storage
THEN:   v1 hook becomes obsolete, can be removed
```

This is **temporary infrastructure** bridging v1 and v2 during strangler migration. When v2 backend handles video generation natively, this v1 hook is deleted.

## Constraints

- **User isolation required:** Different users must NOT access each other's videos
- **v1 is policy-frozen:** Minimal changes only (this qualifies as integration, not feature work)

## References

- [Video Creation ERD](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/video-creation-erd)
- [Strangler Pattern ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/adr-strangler-pattern-sequencing)
- [Light Tier Scope](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/light-tier-v2-feature-scope)
- Issue: DaveX2001/deliverable-tracking#414 (Queue View - blocked by this)
- Issue: DaveX2001/deliverable-tracking#421 (provides v1_process_id mapping)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

