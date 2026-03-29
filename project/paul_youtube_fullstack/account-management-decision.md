---
publish: true
---

# Account Management Decision
[[client-paul]]

## Decision

**Build only role assignment UI.** Supabase Dashboard handles everything else.

## Context

During frontend consolidation (#121), we evaluated whether to absorb the Flask AccountManager into the Main frontend. Dashboard inspection revealed it covers almost everything - only role assignment requires custom UI.

## What Supabase Dashboard Handles (Verified)

| Feature | Dashboard Support |
|---------|-------------------|
| Create users | ✅ "Add user" button |
| Invite users | ✅ "Send invitation" |
| View user list | ✅ Full user list |
| Delete users | ✅ "Delete user" button |
| Ban/disable users | ✅ "Ban user" button |
| Password reset | ✅ "Send password recovery" button |
| Magic link login | ✅ "Send magic link" button |
| MFA management | ✅ "Remove MFA factors" button |

## What Requires Custom UI

| Feature | Why |
|---------|-----|
| **Role assignment** | Dashboard cannot edit `app_metadata` |

### Role Assignment Options

**Option 1: SQL (current)**
```sql
UPDATE auth.users
SET raw_app_meta_data = jsonb_set(
  COALESCE(raw_app_meta_data, '{}'),
  '{roles}',
  '["scripter"]'
)
WHERE email = 'user@example.com';
```

**Option 2: Minimal UI (if SQL becomes painful)**
- Simple dropdown in Main frontend
- Calls FastAPI → Supabase Admin API
- Only feature needed: select user, assign role(s)

## What We're NOT Building

- User creation UI (Dashboard does it)
- Password reset UI (Dashboard does it)
- User list/delete UI (Dashboard does it)
- Credentials viewer (bad practice anyway)

## Principle Applied

**YAGNI** - Don't build what Dashboard provides. Role assignment is the only gap.

## What This Means

- **#227 (Config absorption):** Full AccountManager NOT needed - only role assignment
- **#226 (Script-Writer):** Blocked by missing backend, unrelated to accounts
- **Flask AccountManager:** Legacy - Dashboard replaces it

## Related Issues

- #121 - Frontend Consolidation (parent)
- #226 - Script-Writer absorption (blocked - missing backend)
- #227 - Config absorption (reduced scope - role assignment only)
- #122 - Backend Rebuild (FastAPI + Supabase)

## Source

Conversation: `13fa5968-4aeb-4970-abe7-14fc33c5bb08.jsonl`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

