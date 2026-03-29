---
publish: true
---

# Script-Writer Feature
[[client-paul]]

## Why

Decouples script writing workflow from video queue processing. Allows admins to create and assign script writing jobs to team members, who can work independently and submit completed work. Separates content creation from video production pipeline.

## What

### Admin Workflow
1. **Create Script Job** - Name + description for a writing task
2. **Assign to Writers** - Select one or more team members
3. **Track Progress** - Monitor pending/in-progress/completed status
4. **Manage Assignments** - Remove users, delete jobs

### Scripter Workflow
1. **View Assignments** - See all assigned work (filter by status)
2. **Start Working** - Mark assignment as in-progress
3. **Upload Files** - Attach PDF, DOC, DOCX, TXT (10MB limit)
4. **Complete Work** - Submit with optional notes and file
5. **Auto-hide** - Completed work hidden by default (can filter to view)

### Roles
- **Admin/Scripter role**: Can create jobs, assign, track
- **Any authenticated user**: Can view and complete own assignments

## How

### Key Behaviors
- **Multi-assignment**: Same script can be assigned to multiple users
- **Independent status**: Each user has own assignment status
- **Cascade delete**: Deleting script removes all assignments
- **Status tracking**: pending → in_progress → completed
- **Unique constraint**: Same script can't be assigned twice to same user

### Data Model
- `scripts` table: id, name, description, created_by
- `script_assignments` table: script_id, user_id, status, notes, file_path, timestamps

### API Endpoints

**Admin (requires admin role):**
- `GET/POST /api/scripts` - List/create scripts
- `GET/PUT/DELETE /api/scripts/<id>` - Read/update/delete script
- `GET /api/scripts/<id>/assignments` - View assignments
- `POST /api/scripts/<id>/assign` - Assign to users
- `DELETE /api/scripts/<id>/unassign/<user_id>` - Remove assignment
- `GET /api/scripts/available-users` - List assignable users

**Scripter (requires scripter/admin role):**
- `GET /api/my-assignments` - List own assignments (`?status=` filter)
- `POST /api/my-assignments/<id>/start` - Mark in_progress
- `POST /api/my-assignments/<id>/complete` - Complete with optional file
- `PUT /api/my-assignments/<id>/notes` - Update notes
- `POST /api/my-assignments/<id>/upload` - Upload file
- `GET /api/my-assignments/<id>/download` - Download file

### Frontend Components
- `ScriptManagement.jsx` - Admin view (create, assign, track)
- `MyAssignments.jsx` - Scripter view (view, start, complete, upload)

## Done

### Success Criteria
- Admin can create script job and assign to users
- Writers see only their assignments
- Status progresses: pending → in_progress → completed
- Completed assignments hidden from scripter view
- Progress visible to admin with counts

### Implementation Status
- **Frontend**: Complete (`paul_queue/script-writer-frontend/`)
- **Backend**: Complete (`paul_queue/app/script_job_endpoints.py`) - Flask blueprint
- **Database**: Schema ready (migrations exist)
- **Auth**: Custom Flask JWT with `@token_required` decorator (role-based)

### Related
- Issue #226: Absorb Script-Writer into Main frontend
- Issue #121: Parent issue for frontend consolidation

## Feature Architecture

**Three tabs in script-writer-frontend, all to be absorbed together:**

| Tab | Component | Backend Endpoints | Table |
|-----|-----------|-------------------|-------|
| Script Jobs | ScriptManagement.jsx | `/api/scripts/*` | `scripts`, `script_assignments` |
| My Assignments | MyAssignments.jsx | `/api/my-assignments/*` | `script_assignments` |
| Script Files | App.jsx (inline) | `/api/script-writer/*` | `script_entry` |

**Script Jobs** = Admin creates jobs → assigns to scripters
**My Assignments** = Scripters view/complete assigned work (upload via `script_assignments.file_path`)
**Script Files** = Standalone file repository with text extraction

### Backend Files
- `script_job_endpoints.py` - Script Jobs + My Assignments (`/api/scripts/*`, `/api/my-assignments/*`)
- `script_writer_endpoints.py` - Script Files (`/api/script-writer/*`)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

