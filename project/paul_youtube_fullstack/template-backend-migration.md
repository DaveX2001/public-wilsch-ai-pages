---
publish: true
---

# Template Backend (Express/TypeScript) Migration Analysis
[[client-paul]]

> **Caveat:** This analysis depends on #329 (VideoGen Scaling Spike). Architecture decisions may change based on scaling investigation - multi-tenant encoding strategy, parallelization, etc.

## Summary

| Metric | Count |
|--------|-------|
| **Total Endpoints** | 24 |
| **Supabase Native** | 1 (4%) |
| **Client-Side Movable** | 1 (4%) |
| **Needs Backend** | 22 (92%) |
| **Estimated Code Deletion** | ~5% |

**Conclusion:** This is a specialized video generation backend that MUST remain as a backend service. The vast majority of functionality requires:
- GPU server filesystem access
- FFmpeg with CUDA acceleration
- Large file handling (100GB uploads)
- Video/image processing (Sharp, fluent-ffmpeg)

## Endpoint Categorization

### Auth Endpoints (Migrate to Supabase Auth)

| Endpoint | Method | Current Behavior | Target | Why |
|----------|--------|------------------|--------|-----|
| `/api/auth/login` | POST | Delegates to Flask backend | **Supabase Auth** | Replace with Supabase signInWithPassword |
| `/api/auth/verify` | GET | Delegates to Flask backend | **Supabase Auth** | Replace with Supabase JWT verification |

### File System Operations (MUST Stay Backend)

| Endpoint | Method | Current Behavior | Target | Why |
|----------|--------|------------------|--------|-----|
| `/api/browse` | GET | Recursive directory listing | **Keep Backend** | GPU server filesystem access |
| `/api/browse/subdirectory` | GET | Subdirectory traversal | **Keep Backend** | GPU server filesystem access |
| `/api/browse/search` | GET | File search in directories | **Keep Backend** | GPU server filesystem access |
| `/api/file` | GET | Read/serve files, video streaming | **Keep Backend** | Range requests, video streaming |
| `/api/file` | DELETE | Delete files/directories | **Keep Backend** | GPU server filesystem access |
| `/api/directory` | POST | Create directories | **Keep Backend** | GPU server filesystem access |
| `/api/system` | GET | System info (OS, paths) | **Keep Backend** | GPU server system info |

### File Upload Operations (MUST Stay Backend)

| Endpoint | Method | Current Behavior | Target | Why |
|----------|--------|------------------|--------|-----|
| `/api/upload` | POST | Multer file upload | **Keep Backend** | Server filesystem, 500MB limit |
| `/api/upload-video-loop-media` | POST | Video upload (100GB limit) | **Keep Backend** | Large files, GPU server storage |
| `/api/upload/fonts` | POST | Font file upload | **Keep Backend** | Font files for video generation |

### Video Loop Media Operations (MUST Stay Backend)

| Endpoint | Method | Current Behavior | Target | Why |
|----------|--------|------------------|--------|-----|
| `/api/video-loop-media/:filename` | GET | Get video as base64 | **Keep Backend** | File conversion, memory intensive |
| `/api/video-loop-media/list` | GET | List videos with metadata | **Keep Backend** | GPU server filesystem |
| `/api/video-loop-media/:filename` | DELETE | Delete video file | **Keep Backend** | GPU server filesystem |
| `/api/video-loop-media/:filename/rename` | PUT | Rename video file | **Keep Backend** | GPU server filesystem |
| `/api/video-loop-media/file/:filename` | GET | Serve video file | **Keep Backend** | Static file serving |
| `/api/video-loop-media/preview/:filename` | GET | Generate 1-min preview | **Keep Backend** | **FFmpeg required** |
| `/api/videos` | GET | List all videos | **Keep Backend** | GPU server filesystem |

### Processing Operations (MUST Stay Backend - GPU Required)

| Endpoint | Method | Current Behavior | Target | Why |
|----------|--------|------------------|--------|-----|
| `/api/thumbnail` | GET | Generate image thumbnails | **Keep Backend** | **Sharp image processing** |
| `/api/generate` | POST | Video generation | **Keep Backend** | **FFmpeg + CUDA on GPU** |

### Font Operations (MUST Stay Backend)

| Endpoint | Method | Current Behavior | Target | Why |
|----------|--------|------------------|--------|-----|
| `/api/fonts` | GET | List uploaded fonts | **Keep Backend** | GPU server filesystem |
| `/uploads/fonts/:filename` | GET | Serve font file | **Keep Backend** | Static file serving |

### Pure Data Transformation (Could be Client-Side)

| Endpoint | Method | Current Behavior | Target | Why |
|----------|--------|------------------|--------|-----|
| `/api/template-to-cli-args` | POST | Template to CLI args conversion | **Move to Client** | Pure data transformation, no I/O |

### Health Check

| Endpoint | Method | Current Behavior | Target | Why |
|----------|--------|------------------|--------|-----|
| `/health` | GET | Returns timestamp | **Keep Backend** | Server health monitoring |

## What Can Move to Supabase

### 1. Authentication (Native)
- **Current:** Express routes delegate to Flask backend
- **Migration:** Replace with Supabase Auth client-side
- **Code Reduction:** ~100 lines (auth routes + service)

### 2. Template-to-CLI-Args (Client-Side)
- **Current:** ~270 lines in server.ts (templateToCliArgs + cliArgsToTemplate functions)
- **Migration:** Move to frontend utility function
- **Reason:** Pure data transformation, no server resources needed

## What MUST Stay as Backend

### Core Requirements
1. **GPU Server Filesystem Access**
   - Video loop media storage
   - Font storage
   - Generated video output
   - Project folder access

2. **FFmpeg Video Processing**
   - `generateSlideshow.ts` - Core video generation
   - Uses fluent-ffmpeg with CUDA acceleration
   - GPU-accelerated encoding (h264_nvenc, hevc_nvenc, av1_nvenc)
   - Ken Burns effects, transitions, captions overlay

3. **Image Processing**
   - Sharp for thumbnail generation
   - Image resizing and format conversion

4. **Large File Handling**
   - 100GB upload limit for video loop media
   - 500MB general file uploads
   - Video streaming with range requests

## Migration Path

### Phase 1: Auth Migration
1. Remove `routes/auth.ts` and `services/authService.ts`
2. Frontend uses Supabase Auth directly
3. Backend validates JWT from Supabase (same pattern as FastAPI guide)

### Phase 2: Client-Side Data Transformation
1. Move `templateToCliArgs` and `cliArgsToTemplate` to frontend
2. Frontend sends complete CLI args directly to `/api/generate`
3. Removes ~270 lines from backend

### Phase 3: Keep Express or Migrate to FastAPI?

**Recommendation: Keep Express/TypeScript**

Reasons:
1. Heavy FFmpeg integration - fluent-ffmpeg is mature and well-tested
2. Complex video processing logic already written in TypeScript
3. File streaming and range requests work well in Express
4. No Supabase client needed (just filesystem + FFmpeg)
5. Migration to FastAPI would require rewriting ~2000+ lines with no benefit

**Alternative: Migrate to FastAPI only if:**
- Consolidating all backends into Python
- Need Python-specific video libraries
- Want unified deployment with Flask backend

## Dependencies Analysis

### Current Dependencies (package.json)
```
- express (web server)
- multer (file uploads)
- sharp (image processing)
- fluent-ffmpeg (video processing)
- dotenv (config)
- cors (cross-origin)
```

### Not Needed After Migration
```
- N/A - All dependencies are required for core functionality
```

## File Structure Summary

```
backend/src/
├── server.ts              # Main Express server (~2250 lines) - KEEP
├── routes/
│   └── auth.ts            # Auth routes (~65 lines) - REMOVE
├── services/
│   └── authService.ts     # Auth delegation (~70 lines) - REMOVE
├── middleware/
│   └── auth.ts            # JWT validation - UPDATE for Supabase
├── generator/
│   ├── makeVideoFunction.ts    # Video gen entry (~350 lines) - KEEP
│   ├── generateSlideshow.ts    # Core FFmpeg logic (~1500+ lines) - KEEP
│   ├── captionUtils.ts         # Caption processing - KEEP
│   ├── languageUtils.ts        # Language support - KEEP
│   ├── fillerWordsUtils.ts     # Text processing - KEEP
│   └── i18n.ts                 # Internationalization - KEEP
├── config/
│   └── env.ts             # Environment config - KEEP
├── types/
│   └── index.ts           # TypeScript types (~190 lines) - KEEP
└── utils/
    └── index.ts           # Utility functions - KEEP
```

## Migration Notes

1. **Auth Middleware Update**
   - Change from Flask token validation to Supabase JWT verification
   - Use Supabase service role key for server-side validation

2. **Frontend Changes**
   - Use Supabase Auth for login/logout
   - Move template-to-cli conversion to client utilities
   - Pass Authorization header with Supabase JWT

3. **No Database Needed**
   - This backend has NO database operations
   - All data is filesystem-based (videos, fonts, project files)
   - No migration needed for data layer

4. **Deployment Unchanged**
   - Still runs on GPU server (nuca-systems)
   - Still managed by PM2 (production) or Makefile (staging)
   - Port assignments unchanged (3001 production, 3011 staging)

## Final Verdict

**This backend is NOT a candidate for significant Supabase migration.**

It's a specialized video generation service that requires:
- GPU hardware (CUDA)
- FFmpeg binary
- Large local filesystem storage
- Image processing capabilities

The only changes are:
1. Auth updates (Supabase JWT instead of Flask delegation)
2. Optional client-side refactor (template-to-cli conversion)

**Estimated effort:** 1-2 hours for auth update, ~92% of code stays unchanged.

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

