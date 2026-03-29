---
publish: true
---

# Main Flask Module Migration Plan
[[client-paul]]

**Heuristic:** Native Supabase = Supabase, Workaround = FastAPI

## Architecture Framing

> **Current vs Target:** Initial categorization was based on *current* Flask implementation (filesystem = FastAPI). After validation, categorization reflects *target* architecture (Supabase Storage = frontend direct, files move off filesystem).
>
> **Execution Scope Deferral:** Whether a specific endpoint should exist at all (vs being a direct Supabase query) is discovered during execution, not research. Research determines CAN it go to Supabase; execution determines HOW it should work.

## Summary
- **Supabase (native):** 45 endpoints
- **FastAPI (debuggable):** 59 endpoints
- **Code deletion (frontend direct):** ~15%
- **Total endpoints analyzed:** 104

---

## Endpoint Categorization by File

### main.py (the core - 104 routes)

#### CORS & Infrastructure

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `OPTIONS /api/<path:path>` | **Delete** | Handled by CORS middleware in FastAPI | Infrastructure |

#### Transcripts & Projects

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/transcripts` | **Supabase** | Pure CRUD list with filtering | Native |
| `POST /api/audio/generate/<transcript_id>` | **FastAPI** | RunPod TTS orchestration | Workaround |
| `GET /` | **Delete** | Health check, replace with FastAPI default | Infrastructure |
| `GET /api/projects` | **Supabase** | Pure CRUD list | Native |
| `GET /api/projects/<project_id>/transcript` | **Supabase** | Single record read | Native |
| `GET /api/projects/<project_id>/transcript/download` | **Supabase Storage** | File download | Native |
| `GET /api/projects/<project_id>/audio/<filename>` | **Supabase Storage** | File serving | Native |
| `DELETE /api/projects/<project_id>` | **Supabase** | Simple delete (RLS handles ownership) | Native |

#### Config Management

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/config/apikeys` | **Supabase** | Simple read from config table | Native |
| `POST /api/config/apikeys` | **Supabase** | Simple upsert | Native |
| `DELETE /api/config/apikeys/<provider>` | **Supabase** | Simple delete | Native |
| `GET /api/config/defaultmodel` | **Supabase** | Simple read | Native |
| `POST /api/config/defaultmodel` | **Supabase** | Simple upsert | Native |
| `GET /api/config/current` | **Supabase** | Simple read | Native |
| `PUT /api/config/current/tts` | **Supabase** | Simple update | Native |
| `GET /api/config/models` | **FastAPI** | Dynamic model discovery via OpenAI API | Workaround |

#### Audio/Voice Management

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/transcripts/process/model` | **FastAPI** | OpenAI/Anthropic API calls for processing | Workaround |
| `GET /api/audio/all-voice` | **FastAPI** | Filesystem read + RunPod voice list | Workaround |
| `GET /api/audio/supported-languages` | **Supabase** | Static config table | Native |
| `DELETE /api/audio/delete-voice/<voice_name>` | **FastAPI** | Filesystem deletion + RunPod sync | Workaround |
| `POST /api/audio/upload-voice` | **FastAPI** | Filesystem write + validation | Workaround |
| `GET /api/audio/voice/<voice_name>` | **FastAPI** | Filesystem serving | Workaround |
| `PUT /api/audio/voice/<voice_name>` | **FastAPI** | Filesystem update | Workaround |

#### Image Styles CRUD

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/image/styles` | **Supabase** | Pure CRUD list | Native |
| `POST /api/image/styles` | **Supabase** | Simple create | Native |
| `GET /api/image/styles/<id>` | **Supabase** | Single record read | Native |
| `PUT /api/image/styles/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/image/styles/<id>` | **Supabase** | Simple delete | Native |

#### Image Generation

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/image/generate` | **FastAPI** | Runware API integration, complex orchestration | Workaround |
| `GET /api/image/models` | **FastAPI** | Runware API model discovery | Workaround |
| `GET /api/image/video-models` | **FastAPI** | External API discovery | Workaround |

#### Prompts CRUD

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/prompts` | **Supabase** | Simple create | Native |
| `GET /api/prompts` | **Supabase** | Pure CRUD list | Native |
| `PUT /api/prompts/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/prompts/<id>` | **Supabase** | Simple delete | Native |
| `GET /api/prompts/<id>` | **Supabase** | Single record read | Native |

#### Templates CRUD

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/templates` | **Supabase** | Simple create | Native |
| `GET /api/templates/update-stats` | **FastAPI** | Aggregation across tables, complex logic | Workaround |
| `POST /api/test/audio` | **FastAPI** | Debug/test endpoint | Workaround |
| `GET /api/templates` | **Supabase** | Pure CRUD list with joins | Native |
| `GET /api/templates/<id>` | **Supabase** | Single record with joins | Native |
| `PUT /api/templates/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/templates/<id>` | **Supabase** | Cascade delete (handled by FK) | Native |

#### Channels Management

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/channels` | **Supabase** | Pure CRUD list | Native |
| `POST /api/channels` | **Supabase** | Simple create | Native |
| `GET /api/channels/<id>` | **Supabase** | Single record read | Native |
| `DELETE /api/channels/<id>` | **FastAPI** | Token file cleanup + DB delete | Workaround |
| `GET /api/channels/<id>/auth-status` | **FastAPI** | Token validation, YouTube API check | Workaround |
| `POST /api/channels/<id>/auth` | **FastAPI** | OAuth token handling | Workaround |
| `POST /api/channels/<id>/upload` | **FastAPI** | YouTube API upload orchestration | Workaround |
| `GET /api/channels/<id>/oauth` | **FastAPI** | OAuth flow initiation | Workaround |
| `GET /api/channels/oauth/callback` | **FastAPI** | OAuth callback handling | Workaround |

#### Video Upload & Generation

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/drive-upload/<process_id>` | **FastAPI** | Google Drive API | Workaround |
| `POST /api/video/<id>/upload-to-youtube` | **FastAPI** | YouTube API upload orchestration | Workaround |
| `POST /api/upload_thumbnail/<id>` | **FastAPI** | Filesystem + YouTube API | Workaround |
| `POST /api/video/<id>/generate-description-and-tags` | **FastAPI** | OpenAI API for content generation | Workaround |

#### Queue & Video Pipeline

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/video/queue-status` | **FastAPI** | Background worker state, complex aggregation | Workaround |
| `GET /api/video/start-autopilot` | **FastAPI** | Queue worker management | Workaround |
| `POST /api/multi-vid` | **FastAPI** | Multi-step orchestration | Workaround |
| `GET /api/multi-vid/<id>` | **Supabase** | Single record read | Native |
| `GET /api/multi-vid` | **Supabase** | Pure CRUD list | Native |
| `DELETE /api/multi-vid/<id>` | **Supabase** | Simple delete | Native |
| `POST /api/multi-vid/<id>/process` | **FastAPI** | Pipeline orchestration | Workaround |

#### Video Process CRUD

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/video/update-download-checked/<id>` | **Supabase** | Simple boolean update | Native |
| `POST /api/video/update-require-thumbnail/<id>` | **Supabase** | Simple boolean update | Native |
| `POST /api/video/create` | **FastAPI** | Complex orchestration: folder creation, multi-table insert | Workaround |
| `POST /api/video/<id>/re-run-from-current-step` | **FastAPI** | Pipeline re-execution | Workaround |
| `POST /api/video/<id>/re-run-from-zero` | **FastAPI** | Full pipeline reset + re-execution | Workaround |
| `GET /api/video/<id>` | **Supabase** | Single record with joins | Native |
| `GET /api/video` | **Supabase** | Pure CRUD list with pagination/filtering | Native |
| `PUT /api/video/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/video/<id>` | **FastAPI** | Filesystem cleanup + cascade delete | Workaround |

#### Video Step Endpoints

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/video/<id>/step-2/editable-file` | **FastAPI** | Filesystem read | Workaround |
| `POST /api/video/<id>/step-2/upload` | **FastAPI** | Filesystem write + validation | Workaround |
| `GET /api/video/<id>/step-3/transcript` | **FastAPI** | Filesystem read | Workaround |
| `POST /api/video/<id>/step-3/upload` | **FastAPI** | Filesystem write | Workaround |
| `GET /api/video/<id>/preview` | **FastAPI** | Filesystem serving | Workaround |
| `GET /api/video/<id>/assets` | **FastAPI** | Filesystem directory listing | Workaround |
| `GET /api/video/<id>/image-style/<style_id>/demo` | **FastAPI** | Image generation demo | Workaround |
| `POST /api/video/<id>/image-style/<style_id>/footage` | **FastAPI** | Runware API for footage generation | Workaround |

#### Utility Endpoints

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/reverse-image-prompt` | **FastAPI** | OpenAI vision API | Workaround |
| `POST /api/update-sop` | **FastAPI** | Filesystem write | Workaround |
| `GET /api/sop` | **FastAPI** | Filesystem read | Workaround |
| `POST /api/preview-pdf` | **FastAPI** | PDF generation, complex processing | Workaround |
| `POST /api/video/<id>/rerun` | **FastAPI** | Pipeline orchestration | Workaround |

#### Google Drive Integration

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/gdrive/auth` | **FastAPI** | OAuth flow | Workaround |
| `POST /api/gdrive/auth/finish` | **FastAPI** | OAuth callback | Workaround |
| `GET /api/gdrive/auth/status` | **FastAPI** | Token validation | Workaround |
| `GET /api/gdrive/auth/disconnect` | **FastAPI** | Token revocation | Workaround |

#### Video Generation Templates

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/video_gen_template` | **Supabase** | Simple create | Native |
| `PUT /api/video_gen_template/<id>` | **Supabase** | Simple update | Native |
| `GET /api/video_gen_template/<id>` | **Supabase** | Single record read | Native |
| `GET /api/video_gen_templates` | **Supabase** | Pure CRUD list | Native |
| `DELETE /api/video_gen_template/<id>` | **Supabase** | Simple delete | Native |

#### Final Video Endpoints

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/test-vid` | **Delete** | Debug endpoint | Delete |
| `POST /api/video/<id>/generate-video` | **FastAPI** | Express backend orchestration | Workaround |
| `GET /api/video/<id>/final-video` | **FastAPI** | Filesystem serving | Workaround |
| `GET /api/video/<id>/final-video-view` | **FastAPI** | Filesystem serving with headers | Workaround |
| `POST /api/video/<id>/cleanup-loading-file` | **FastAPI** | Filesystem cleanup | Workaround |
| `POST /api/video/<id>/generate-description-tags` | **FastAPI** | OpenAI API | Workaround |
| `GET /api/video/<id>/get-description-tags` | **FastAPI** | Filesystem read | Workaround |
| `GET /api/cleanup-loading-files` | **FastAPI** | Batch filesystem cleanup | Workaround |
| `GET /api/test-optimization` | **Delete** | Debug endpoint | Delete |

---

### account_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/accounts` | **Supabase** | Pure CRUD list | Native |
| `POST /api/accounts` | **Supabase** | Simple create with password hash | Native |
| `GET /api/accounts/<id>` | **Supabase** | Single record read | Native |
| `PUT /api/accounts/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/accounts/<id>` | **Supabase** | Simple delete | Native |
| `PATCH /api/accounts/<id>/toggle-active` | **Supabase** | Simple boolean toggle | Native |
| `POST /api/accounts/<id>/roles` | **Supabase** | Simple array update | Native |

---

### auth_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/auth/login` | **Supabase Auth** | Native auth flow | Native |
| `POST /api/auth/logout` | **Supabase Auth** | Native auth flow | Native |
| `GET /api/auth/status` | **Supabase Auth** | JWT verification | Native |
| `POST /api/auth/refresh` | **Supabase Auth** | Token refresh | Native |
| `POST /api/auth/create-user` | **Supabase Auth** | User creation | Native |
| `POST /api/auth/api-key` | **FastAPI** | API key generation (custom logic) | Workaround |
| `GET /api/auth/api-key` | **Supabase** | Simple read | Native |
| `DELETE /api/auth/api-key` | **Supabase** | Simple delete | Native |

---

### competition_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/competition/analyze-channel` | **FastAPI** | YouTube API scraping | Workaround |
| `GET /api/competition/analyses` | **Supabase** | Pure CRUD list | Native |
| `GET /api/competition/analyses/<id>` | **Supabase** | Single record read | Native |
| `DELETE /api/competition/analyses/<id>` | **Supabase** | Simple delete | Native |

---

### masterprompt_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/masterprompts` | **Supabase** | Pure CRUD list | Native |
| `POST /api/masterprompts` | **Supabase** | Simple create | Native |
| `GET /api/masterprompts/<id>` | **Supabase** | Single record read | Native |
| `PUT /api/masterprompts/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/masterprompts/<id>` | **Supabase** | Simple delete | Native |
| `POST /api/masterprompts/<id>/duplicate` | **Supabase** | Read + create (can be RPC) | Native |

---

### multi_vid_api_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/multivid/projects` | **Supabase** | Pure CRUD list | Native |
| `POST /api/multivid/projects` | **FastAPI** | Multi-table orchestration | Workaround |
| `GET /api/multivid/projects/<id>` | **Supabase** | Single record with joins | Native |
| `PUT /api/multivid/projects/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/multivid/projects/<id>` | **Supabase** | Cascade delete | Native |
| `POST /api/multivid/projects/<id>/videos` | **FastAPI** | Batch video creation | Workaround |

---

### nav_links_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/nav-links` | **Delete** | Static config, move to frontend env | Delete |

---

### preset_api_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/presets` | **Supabase** | Migrate from filesystem to DB table | Native |
| `GET /api/presets/<id>` | **Supabase** | Single record read | Native |
| `POST /api/presets` | **Supabase** | Simple create | Native |
| `PUT /api/presets/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/presets/<id>` | **Supabase** | Simple delete | Native |
| `POST /api/presets/<id>/duplicate` | **Supabase** | Read + create (can be RPC) | Native |
| `POST /api/presets/generate-from-master` | **FastAPI** | OpenAI GPT-4 API call | Workaround |

---

### thumbnail_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/thumbnail/user-role` | **Supabase** | JWT claims extraction | Native |
| `GET /api/thumbnail/video-processes` | **Supabase** | Pure CRUD list with joins | Native |
| `PATCH /api/thumbnail/video-processes/<id>` | **Supabase** | Simple update | Native |
| `POST /api/thumbnail/upload/<id>` | **FastAPI** | Filesystem + optional YouTube upload | Workaround |
| `GET /api/thumbnail/image/<id>` | **Supabase Storage** | File serving | Native |
| `DELETE /api/thumbnail/delete/<id>` | **FastAPI** | Filesystem deletion | Workaround |
| `GET /api/thumbnail/storage` | **Supabase** | Pure CRUD list | Native |
| `POST /api/thumbnail/storage/upload` | **Supabase Storage** | Direct upload | Native |
| `GET /api/thumbnail/storage/image/<id>` | **Supabase Storage** | File serving | Native |
| `DELETE /api/thumbnail/storage/<id>` | **FastAPI** | Filesystem + DB delete | Workaround |
| `GET /api/thumbnail/storage/download/<id>` | **Supabase Storage** | File download | Native |
| `POST /api/thumbnail/vidrush/channels` | **Supabase** | Simple create | Native |
| `GET /api/thumbnail/vidrush/channels/<id>/oauth` | **FastAPI** | OAuth flow | Workaround |
| `DELETE /api/thumbnail/vidrush/channels/<id>` | **FastAPI** | Token cleanup + delete | Workaround |
| `GET /api/thumbnail/vidrush/channels` | **Supabase** | Pure CRUD list | Native |
| `GET /api/thumbnail/vidrush/channels/<id>/videos` | **FastAPI** | YouTube API | Workaround |
| `GET /api/thumbnail/vidrush/videos/<id>` | **FastAPI** | YouTube API | Workaround |
| `PUT /api/thumbnail/vidrush/videos/<id>/metadata` | **FastAPI** | YouTube API | Workaround |
| `PUT /api/thumbnail/vidrush/videos/<id>/thumbnail` | **FastAPI** | YouTube API | Workaround |
| `GET /api/thumbnail/thumbnail-processing` | **Supabase** | Pure CRUD list | Native |
| `POST /api/thumbnail/thumbnail-processing` | **Supabase** | Simple create | Native |
| `GET /api/thumbnail/thumbnail-processing/<id>` | **Supabase** | Single record read | Native |
| `DELETE /api/thumbnail/thumbnail-processing/<id>` | **FastAPI** | Filesystem cleanup | Workaround |
| `PATCH /api/thumbnail/thumbnail-processing/<id>/toggle-hidden` | **Supabase** | Simple update | Native |
| `PATCH /api/thumbnail/thumbnail-processing/<id>` | **Supabase** | Simple update | Native |
| `POST /api/thumbnail/thumbnail-processing/<id>/upload` | **FastAPI** | Filesystem write | Workaround |
| `GET /api/thumbnail/thumbnail-processing/<id>/thumbnail` | **Supabase Storage** | File serving | Native |
| `GET /api/thumbnail/thumbnail-processing/<id>/download` | **Supabase Storage** | File download | Native |

---

### script_writer_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `POST /api/script-writer/upload` | **FastAPI** | PDF/DOCX text extraction (PyPDF2, python-docx) | Workaround |
| `GET /api/script-writer/scripts` | **Supabase** | Pure CRUD list | Native |
| `GET /api/script-writer/scripts/<id>` | **Supabase** | Single record read | Native |
| `PUT /api/script-writer/scripts/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/script-writer/scripts/<id>` | **Supabase** | Storage delete + DB delete | Native |
| `GET /api/script-writer/scripts/<id>/download` | **Supabase Storage** | File download | Native |
| `GET /api/script-writer/scripts/<id>/text` | **Supabase** | Simple read | Native |

---

### script_job_endpoints.py

| Endpoint | Target | Why | Type |
|----------|--------|-----|------|
| `GET /api/scripts` | **Supabase** | Pure CRUD list with RLS | Native |
| `GET /api/scripts/<id>` | **Supabase** | Single record with RLS | Native |
| `POST /api/scripts` | **Supabase** | Simple create | Native |
| `PUT /api/scripts/<id>` | **Supabase** | Simple update | Native |
| `DELETE /api/scripts/<id>` | **Supabase** | Cascade delete | Native |
| `GET /api/scripts/<id>/assignments` | **Supabase** | Read with joins | Native |
| `POST /api/scripts/<id>/assign` | **Supabase** | Multi-insert (can be RPC) | Native |
| `DELETE /api/scripts/<id>/unassign/<user_id>` | **Supabase** | Simple delete | Native |
| `GET /api/my-assignments` | **Supabase** | List with RLS filter | Native |
| `POST /api/my-assignments/<id>/start` | **Supabase** | Simple update | Native |
| `POST /api/my-assignments/<id>/complete` | **Supabase** | Storage upload + status update | Native |
| `PUT /api/my-assignments/<id>/notes` | **Supabase** | Simple update | Native |
| `POST /api/my-assignments/<id>/upload` | **Supabase Storage** | File upload | Native |
| `GET /api/my-assignments/<id>/download` | **Supabase Storage** | File download | Native |
| `GET /api/scripts/available-users` | **Supabase** | Simple list with filter | Native |

---

## Supabase Configuration Needed

### RLS Policies
```sql
-- VideoProcess: Owner can CRUD, admin full access
CREATE POLICY "users_own_processes" ON video_process
  USING (user_id = auth.uid() OR is_admin());

-- Scripts: Admin creates, scripters read assigned
CREATE POLICY "scripters_read_assigned" ON scripts
  USING (EXISTS (
    SELECT 1 FROM script_assignments
    WHERE script_id = scripts.id AND user_id = auth.uid()
  ));

-- Channels: User owns their channels
CREATE POLICY "users_own_channels" ON channels
  USING (user_id = auth.uid() OR is_admin());
```

### Storage Buckets
| Bucket | Purpose | RLS |
|--------|---------|-----|
| `thumbnails` | Video thumbnails | Owner + admin read/write |
| `voices` | Custom TTS voices | Admin only |
| `scripts` | Uploaded script files | Owner + admin |
| `video-assets` | Generated images, audio | Owner + admin |
| `final-videos` | Completed videos | Owner + admin |

### Database Migrations
- Presets: Migrate from filesystem to `presets` table
- Voice metadata: Create `voices` table (files stay on filesystem for RunPod)
- Add `user_id` FK to all user-owned tables

---

## FastAPI Services Needed

### VideoGenerationService
**Purpose:** Orchestrate full video pipeline
**Why FastAPI:** Multi-step orchestration, RunPod TTS, Runware images, FFmpeg processing, filesystem management
**External dependencies:** RunPod API, Runware API, Template Backend (Express)

### TTSService
**Purpose:** Text-to-speech generation via RunPod
**Why FastAPI:** External API calls, audio file management, chunk processing
**External dependencies:** RunPod API

### ImageGenService
**Purpose:** Image generation via Runware
**Why FastAPI:** External API calls, complex prompt handling, model selection
**External dependencies:** Runware API

### YouTubeService
**Purpose:** YouTube upload and OAuth management
**Why FastAPI:** OAuth flows, token management, YouTube Data API calls
**External dependencies:** YouTube Data API v3, Google OAuth

### GoogleDriveService
**Purpose:** Google Drive integration
**Why FastAPI:** OAuth flows, file uploads
**External dependencies:** Google Drive API

### ScriptProcessingService
**Purpose:** PDF/DOCX text extraction
**Why FastAPI:** File processing, PyPDF2, python-docx libraries
**External dependencies:** None (local processing)

### CompetitionAnalysisService
**Purpose:** YouTube channel analysis/scraping
**Why FastAPI:** External API calls, data aggregation
**External dependencies:** YouTube Data API

### ConfigService
**Purpose:** Dynamic model discovery
**Why FastAPI:** OpenAI API calls for model listing
**External dependencies:** OpenAI API

### FileSystemService
**Purpose:** Manage project folders, assets, videos
**Why FastAPI:** Filesystem operations not suitable for Supabase Storage (large video files, FFmpeg processing)
**External dependencies:** Local filesystem on GPU server

---

## Migration Order

### Phase 1: Low Risk - Pure CRUD (2-3 weeks)
**Target: 45 endpoints to Supabase**

1. **Auth migration** - Supabase Auth replaces custom JWT
2. **Accounts** - Direct Supabase access with RLS
3. **Presets** - Migrate filesystem to DB, then Supabase direct
4. **Masterprompts** - Pure CRUD
5. **Image Styles** - Pure CRUD
6. **Prompts** - Pure CRUD
7. **Templates** - Pure CRUD with joins
8. **Config tables** - Simple key-value

### Phase 2: Medium Risk - File Handling (2-3 weeks)
**Target: Supabase Storage + remaining CRUD**

1. **Thumbnail storage** - Supabase Storage
2. **Script files** - Supabase Storage for downloads
3. **Script jobs** - CRUD + assignments
4. **Video process CRUD** - Read/list/update operations
5. **Channels CRUD** - Without OAuth

### Phase 3: High Risk - Core Pipeline (4-6 weeks)
**Target: FastAPI services for orchestration**

1. **TTSService** - RunPod integration
2. **ImageGenService** - Runware integration
3. **YouTubeService** - OAuth + uploads
4. **VideoGenerationService** - Full pipeline
5. **Queue management** - Background workers

### Phase 4: Cleanup (1-2 weeks)
1. Delete deprecated endpoints
2. Remove Flask app
3. Update frontend API calls
4. End-to-end testing

---

## Risk Assessment

| Risk Level | Items |
|------------|-------|
| **Low** | Pure CRUD endpoints, config management, presets, masterprompts, image styles |
| **Medium** | Auth migration, file uploads to Supabase Storage, thumbnail handling |
| **High** | Video pipeline, TTS generation, YouTube OAuth, queue workers |
| **Critical** | Data migration from filesystem to Supabase, maintaining pipeline stability during migration |

---

## Key Architectural Decisions

### 1. Filesystem vs Supabase Storage
**Decision:** Keep video files on filesystem (GPU server), use Supabase Storage only for smaller assets

**Rationale:**
- Video files are processed by FFmpeg on GPU server
- Large file uploads to Supabase Storage have bandwidth costs
- Template Backend (Express) reads from local filesystem
- RunPod needs local voice files

### 2. Queue Management
**Decision:** Keep background workers in FastAPI, use Supabase for queue state

**Rationale:**
- Workers run on GPU server
- Need access to local filesystem
- Supabase can store job status/progress
- Consider Supabase Realtime for progress updates

### 3. OAuth Token Storage
**Decision:** Keep token files on filesystem, store metadata in Supabase

**Rationale:**
- OAuth tokens need secure filesystem storage
- Supabase stores channel metadata
- Token refresh happens on backend only

---

## Code Deletion Estimate

| Category | Lines | Percentage |
|----------|-------|------------|
| Flask boilerplate | ~500 | 10% |
| CRUD endpoints (replaced by Supabase) | ~1500 | 30% |
| Auth middleware (replaced by Supabase Auth) | ~200 | 4% |
| **Total reduction** | **~2200** | **~44%** |

The remaining ~56% becomes FastAPI services handling complex orchestration that cannot be simplified to Supabase direct access.

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

