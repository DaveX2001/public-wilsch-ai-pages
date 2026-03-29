---
publish: true
---

# Thumbnail Manager Feature
[[client-paul]]

## Why

Centralizes thumbnail management for YouTube video workflows. Provides upload, replacement, and library management for video thumbnails across multiple sources (video processes, external requests via MakeMyThumb, and YouTube channels via VidRush). Separates thumbnail operations from core video queue processing.

## What

### Admin Workflow
1. **Video Processes** - View all videos, upload/replace thumbnails, edit metadata
2. **Thumbnail Processing** - Create tasks, assign to users, track completion
3. **Thumbnail Requests** - Manage MakeMyThumb external requests, assign to team, approve/reject
4. **Storage** - Manage thumbnail library, upload standalone thumbnails
5. **Channels** - Connect YouTube channels via OAuth, push thumbnails to YouTube

### Manager Workflow
1. **Channels** - View connected YouTube channels and videos
2. **Thumbnail Requests** - View requests, upload thumbnails (cannot approve/reject)
3. **Storage** - Upload and view thumbnails (cannot delete)

### Thumbnail Role Workflow
1. **Thumbnail Processing** - View assigned tasks, upload thumbnails, mark complete
2. **Storage** - View thumbnail library

### Roles
- **Admin**: Full access to all tabs, CRUD operations, approvals
- **Manager**: Access to channels, requests, can upload but cannot delete/approve
- **Thumbnail**: Access to processing tab and storage, complete assigned work

## How

### Key Behaviors
- **JWT Authentication**: Token stored in localStorage (`thumbnail_auth_token`)
- **Role-based tab visibility**: Tabs shown/hidden based on user role
- **Permission flags**: `canEdit`, `canDelete`, `canUpload` control actions
- **External integrations**: MakeMyThumb API, YouTube OAuth
- **File handling**: 2MB size limit, blob downloads

### Data Model

| Entity | Key Fields |
|--------|------------|
| User | username, roles[], token |
| VideoProcess | id, title, thumbnail_title, thumbnail_description, thumbnail_exists, thumbnail_url, is_thumbnail_uploaded, template_name, channel_name, reference |
| ThumbnailEntry | id, title, description, reference_link, thumbnail_url, has_thumbnail, status (pending/completed/hidden/rejected), source, rejectionReason |
| MakeMyThumbRequest | id, title, description, refUrl, downloadUrl, status (PENDING/ASSIGNED/THUMBNAIL_SENT/APPROVED_BY_ADMIN/REJECTED/DELIVERED), customer, assignment |
| StoredThumbnail | id, filename, url, file_size_mb, file_extension, created_at |
| VidRushChannel | id, youtube_channel_id, display_name, token_file_path |
| VidRushVideo | id, title, description, tags[], thumbnail, viewCount, likeCount, commentCount, publishedAt |

### API Endpoints

**Authentication:**
- `POST /api/auth/login` - User login
- `GET /api/auth/verify` - Token validation

**User Management:**
- `GET /api/thumbnail/user-role` - Current user role
- `GET /api/accounts` - All accounts (Admin only)

**Video Processes:**
- `GET /api/thumbnail/video-processes` - List (paginated, searchable)
- `GET /api/thumbnail/video-processes/:id` - Single process
- `PATCH /api/thumbnail/video-processes/:id` - Update metadata
- `POST /api/thumbnail/upload/:id` - Upload thumbnail
- `DELETE /api/thumbnail/delete/:id` - Delete thumbnail

**Thumbnail Processing:**
- `GET /api/thumbnail/thumbnail-processing` - List tasks
- `POST /api/thumbnail/thumbnail-processing` - Create task
- `PATCH /api/thumbnail/thumbnail-processing/:id` - Update
- `DELETE /api/thumbnail/thumbnail-processing/:id` - Delete (Admin)
- `PATCH /api/thumbnail/thumbnail-processing/:id/toggle-hidden` - Hide/unhide
- `POST /api/thumbnail/thumbnail-processing/:id/upload` - Upload
- `GET /api/thumbnail/thumbnail-processing/:id/download` - Download
- `GET /api/makemythumb/assignments` - MakeMyThumb assigned tasks

**Thumbnail Requests (MakeMyThumb):**
- `GET /api/makemythumb/requests` - List all
- `POST /api/makemythumb/assign` - Assign to user
- `PATCH /api/makemythumb/assignments/:id/status` - Update status
- `POST /api/makemythumb/upload/:id` - Upload
- `POST /api/makemythumb/requests/:id/approve` - Approve (Admin)
- `POST /api/makemythumb/requests/:id/reject` - Reject (Admin)
- `DELETE /api/makemythumb/requests/:id` - Delete (Admin)

**Storage:**
- `GET /api/thumbnail/storage` - List (paginated)
- `POST /api/thumbnail/storage/upload` - Upload
- `GET /api/thumbnail/storage/download/:id` - Download
- `DELETE /api/thumbnail/storage/:id` - Delete (Admin)

**VidRush (YouTube):**
- `GET /api/thumbnail/vidrush/channels` - List channels
- `POST /api/thumbnail/vidrush/channels` - Create (Admin)
- `DELETE /api/thumbnail/vidrush/channels/:id` - Delete (Admin)
- `GET /api/thumbnail/vidrush/channels/:id/oauth` - OAuth URL
- `GET /api/thumbnail/vidrush/channels/:id/videos` - List videos
- `PUT /api/thumbnail/vidrush/videos/:id/metadata` - Update metadata
- `PUT /api/thumbnail/vidrush/videos/:id/thumbnail` - Update thumbnail

### Frontend Components

**Source:** `paul_queue/thumbnail-frontend/` (port 3002, 4,284 LOC)

| File | Purpose |
|------|---------|
| App.jsx | Main container, tab navigation, role checks |
| AuthLayout.jsx | Auth wrapper, JWT validation |
| Login.jsx | Login form |
| Storage.jsx | Thumbnail library tab |
| ThumbnailProcessing.jsx | Task queue tab |
| ThumbnailRequests.jsx | MakeMyThumb requests tab |
| VidRush.jsx | YouTube channels tab |
| ProcessEntry.jsx | Video process card (inline editing) |
| ThumbnailEntry.jsx | Processing task card |
| components/AssignmentDialog.jsx | Radix Dialog: Assign request |
| components/RequestTabs.jsx | Radix Tabs: All/Assignments/Approvals |
| components/ThumbnailPreviewModal.jsx | Custom modal: Preview |
| components/ConfirmDialog.jsx | Radix AlertDialog: Confirmations |
| components/TooltipWrapper.jsx | Radix Tooltip wrapper |

## Done

### Success Criteria
- Single React app running on port 5173 (Main)
- Thumbnail features accessible as routes in Main
- Plain CSS migrated to Tailwind
- Radix UI components aligned with Main's shadcn
- Side-by-side tested against original
- Port 3002 can be killed

### Implementation Status
- **Frontend**: Standalone at `paul_queue/thumbnail-frontend/`
- **Backend**: Complete (endpoints in Flask)
- **Database**: Schema exists
- **Auth**: Custom Flask JWT

### Related
- Issue #228: Absorb Thumbnail into Main (3/4)
- Issue #121: Parent issue for frontend consolidation
- Absorption Workflow: `~/.claude/hippocampus/global/absorption-workflow.md`

## Feature Architecture

**Five tabs in thumbnail-frontend, absorbed via slices:**

| Slice | Tab | Component | Key Endpoints | Complexity |
|-------|-----|-----------|---------------|------------|
| 1 | Storage | Storage.jsx | `/api/thumbnail/storage/*` | Low |
| 2 | Video Processes | App.jsx (inline) | `/api/thumbnail/video-processes/*` | Medium |
| 3 | Thumbnail Processing | ThumbnailProcessing.jsx | `/api/thumbnail/thumbnail-processing/*` | Medium |
| 4 | Thumbnail Requests | ThumbnailRequests.jsx | `/api/makemythumb/*` | High (external) |
| 5 | Channels | VidRush.jsx | `/api/thumbnail/vidrush/*` | High (YouTube OAuth) |

### CSS Files (14 total, migrate to Tailwind)
- index.css, Login.css, App.css, HeaderStyles.css
- Storage.css, ThumbnailProcessing.css, ThumbnailRequests.css, VidRush.css
- ApiKeySetup.css (legacy, can delete)
- components/: AssignmentDialog.css, RequestTabs.css, ThumbnailPreviewModal.css, ConfirmDialog.css, TooltipWrapper.css

### Radix to shadcn Mapping
| Radix Package | shadcn Equivalent |
|---------------|-------------------|
| @radix-ui/react-dialog | `<Dialog>` |
| @radix-ui/react-tabs | `<Tabs>` |
| @radix-ui/react-alert-dialog | `<AlertDialog>` |
| @radix-ui/react-tooltip | `<Tooltip>` |

### Absorption Notes
- Auth: Unify localStorage keys (`thumbnail_auth_token` → Main's token)
- API: Replace axios instance with Main's api service
- Icons: Already uses lucide-react (same as Main)
- Modals: Migrate custom ThumbnailPreviewModal to shadcn Dialog
- Legacy: Delete ApiKeySetup.jsx/css (unused)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

