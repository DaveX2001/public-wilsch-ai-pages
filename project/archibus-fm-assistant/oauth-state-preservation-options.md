---
publish: true
---

# OAuth State Preservation: Bruce BEM Integration

[[client-archibus]]

**Parent:** [[standalone-auth-architecture]]

## Problem

Bruce BEM (Code on Time framework) strips OAuth `state` query parameter during internal redirect to login form. State is required for LibreChat to match callback to originating session.

## Constraint

LibreChat generates: `state = userId:serverName` (deterministic per user+server)

Bruce BEM must return this value unchanged in callback URL.

## Solutions

### Category A: Bruce BEM Only

| Approach | Implementation | Complexity |
|----------|---------------|------------|
| **A1. UserLogin Override** | Intercept state pre-login, store in DB/cache, restore post-login | Medium |
| **A2. Global.asax BeginRequest** | Catch request before Bruce BEM processes, preserve state | Medium |
| **A3. Hidden Form Field** | Modify login form to carry state through POST | Low |

**Code on Time docs:** [UserLogin override documented](https://codeontime.com/print/learn/security/handling-login-and-logout). BeginRequest requires standard ASP.NET Global.asax (outside framework).

### Category B: LibreChat + Bruce BEM

| Approach | Implementation | Complexity |
|----------|---------------|------------|
| **B1. Redirect URI Encoding** | Encode flowId in callback URL path | Medium |

```
LibreChat sends:
  redirect_uri = /api/mcp/bruce-bem/oauth/callback/{flowId}
  state = {flowId}  (stripped by Bruce BEM)

Bruce BEM:
  1. Extract flowId from redirect_uri path
  2. Include as state in callback:
     POST /api/mcp/bruce-bem/oauth/callback/{flowId}?state={flowId}

LibreChat receives:
  state param as expected (no handler changes)
```

**Requires:** LibreChat code change to encode flowId in redirect_uri.

## Decision Tree

```
Can Rein intercept state in Code on Time?
├─ Yes → Category A (A1, A2, or A3)
└─ No  → Category B (redirect_uri encoding)
```

## Open Questions

- Does Bruce BEM preserve `redirect_uri` intact?
- Does Bruce BEM preserve `nonce` or other OAuth params?
- Is there a Bruce BEM config to preserve unknown query params?

## References

- [Code on Time: Handling Login and Logout](https://codeontime.com/print/learn/security/handling-login-and-logout) - UserLogin override documented
- [Code on Time: Custom Login Form](https://codeontime.com/learn/security/custom-login-form)
- [LibreChat OAuth handler](services/librechat/packages/api/src/mcp/oauth/handler.ts)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

