---
publish: true
---

# v2→v1 Proxy Pattern with Service Account Auth
[[paul-youtube-fullstack]]

## Problem

v2 React frontend needs to call v1 Flask API endpoints during migration. v1 uses JWT authentication. **Credentials cannot live in the browser** - any approach that puts v1 credentials in frontend JS exposes them to anyone who inspects the code.

## Solution

Route v2 frontend calls through v2 FastAPI backend, which authenticates to v1 using a service account.

```
Browser (v2 React)
    ↓ Supabase JWT
v2 FastAPI Backend
    ↓ v1 JWT (service account)
v1 Flask Backend
```

## Why This Pattern

| Alternative | Problem |
|-------------|---------|
| Hardcode v1 credentials in frontend | Exposed in browser dev tools |
| User logs into v1 separately | Dual login UX, separate account systems |
| Make v1 endpoints public | Security risk, GPU resource abuse |
| Token bridge (v2 JWT → v1 JWT) | Different signing keys, doesn't exist |

**Proxy wins because:**
- Credentials stay server-side (never in browser)
- Leverages existing v2 FastAPI backend
- Strangler Pattern aligned (v2 becomes API gateway)
- Works identically in local/staging/production

## Architecture

### v1 Service Account

Created in v1 `accounts` table:
- Username: `v2-service`
- Password: MD5 hash (v1's auth mechanism)
- Roles: `["queue_processor"]`

### v2 Backend Components

**Config** (`backend/app/config.py`):
```python
V1_API_URL: str = "http://localhost:5001"
V1_SERVICE_USER: str = "v2-service"
V1_SERVICE_PASS: SecretStr | None = None
```

**V1AuthService** (`backend/app/services/v1_auth_service.py`):
- Authenticates to v1 `/api/auth/login`
- Caches JWT token (24h expiry)
- Lazy refresh: re-authenticates when token expired or near expiry (1h threshold)

**Generic Proxy Route** (`backend/app/routes/v1_proxy.py`):
- Catch-all: `ANY /api/v1/proxy/{path:path}`
- Forwards to v1: `/api/{path}`
- Adds `Authorization: Bearer <token>` header
- Preserves method, query params, body

## How to Use

### For Frontend (calling v1 through proxy)

Instead of:
```typescript
// DON'T: Direct v1 call (will get 401)
fetch('http://localhost:5001/api/video/123/demo')
```

Do:
```typescript
// DO: Call through v2 proxy
fetch('http://localhost:8000/api/v1/proxy/video/123/demo')
```

The proxy handles authentication automatically.

### For New v1 Endpoints

No backend changes needed. The generic proxy forwards any path:

| v1 Endpoint | v2 Proxy URL |
|-------------|--------------|
| `/api/video/{id}/demo` | `/api/v1/proxy/video/{id}/demo` |
| `/api/scripts/generate` | `/api/v1/proxy/scripts/generate` |
| `/api/channels/list` | `/api/v1/proxy/channels/list` |

### Environment Setup

Add to v2 backend `.env`:
```bash
V1_API_URL=http://localhost:5001
V1_SERVICE_USER=v2-service
V1_SERVICE_PASS=v2-service-secret
```

For production (nuca-systems), same pattern but with production v1 URL.

## Commit Reference

Implementation: https://github.com/MariusWilsch/christmas-paul-package/commit/32fc530

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

