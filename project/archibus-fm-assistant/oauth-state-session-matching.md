---
publish: true
---

<script src="https://cdn.jsdelivr.net/npm/mermaid/dist/mermaid.min.js"></script>
<script>mermaid.initialize({startOnLoad:true});</script>

# ADR: OAuth State Parameter for Session Matching

[[client-archibus]]

## Status
**Proposed** | Date: 2025-12-16

## Context

When moving from iframe-embedded LibreChat to standalone chat interface, a session matching problem emerges:

**Iframe approach (current):**
- Bruce BEM opens LibreChat in iframe with `?userToken=...&refreshToken=...`
- Session context is implicit - iframe IS the session
- One user, one browser, one session

**Standalone approach (target):**
- User visits LibreChat directly → selects "Login with Bruce BEM" → redirects to Bruce BEM login → callback
- Multiple users could authenticate simultaneously
- Session context must be explicit

**The Problem:**
When Bruce BEM sends callback with tokens to LibreChat, how does LibreChat know which browser session those tokens belong to?

## Decision

Use the standard OAuth `state` parameter for session matching. LibreChat already implements this via a Redis-backed polling architecture.

### How LibreChat Solves It

<div class="mermaid">
sequenceDiagram
    participant Browser
    participant LibreChat as LibreChat Server
    participant BruceBEM as Bruce BEM
    participant Redis

    Browser->>LibreChat: 1. Initiate OAuth (state=S...)
    LibreChat->>Redis: 2. Create Flow ("S...", PENDING)
    Browser->>LibreChat: 3. Poll Status ("S...")
    LibreChat-->>Browser: 4. Poll Response (PENDING)
    Note over Browser: Polling continues...
    Browser->>BruceBEM: 5. Redirect to Authorize (state=S...)
    Note over BruceBEM: User Authenticates
    BruceBEM->>LibreChat: 6. Callback (state=S..., tokens={T...})
    LibreChat->>Redis: 7. Update Flow ("S...", COMPLETED, tokens)
    Browser->>LibreChat: 8. Poll Status ("S...")
    LibreChat->>Redis: 9. Get Flow ("S...")
    Redis-->>LibreChat: 10. Flow Data (COMPLETED, {T...})
    LibreChat-->>Browser: 11. Poll Response (COMPLETED, {T...})
    Note over Browser: ✓ Login Complete
</div>

LibreChat does NOT route callbacks directly to browser sessions. Instead:

1. **Deterministic Flow ID** = `userId:serverName` used as `state` parameter
2. **Redis Store** = All sessions access shared flow state
3. **Stateless Callback** = Callback handler updates Redis, doesn't need session
4. **Client Polling** = Browser polls Redis every 3.5s until completion detected

### The Flow

```
1. User clicks "Login with Bruce BEM" in LibreChat
   → LibreChat generates state="userId:serverName"
   → LibreChat stores { state: session } mapping in Redis
   → Browser starts polling Redis every 3.5 seconds

2. User redirected to Bruce BEM with state parameter
   → Bruce BEM authenticates user
   → Bruce BEM creates userToken/refreshToken

3. Bruce BEM redirects to LibreChat callback
   → callback?code=...&state=userId:serverName

4. LibreChat callback handler (STATELESS):
   → Extracts state from URL
   → Looks up flow in Redis
   → Stores tokens, marks flow COMPLETED

5. Browser's next poll detects COMPLETED
   → Retrieves tokens → Login complete
```

## Bruce BEM Contract

Bruce BEM does NOT need to implement full OAuth. Minimal requirements:

1. **Accept** `state` parameter in login redirect URL
2. **Authenticate** user (however Bruce BEM does it)
3. **Create** userToken + refreshToken
4. **Return** to callback URL with `state` unchanged

```
LibreChat sends:
  https://bruce-bem.com/login?
    redirect_uri=https://chat.com/callback
    state=user123:brucebem

Bruce BEM returns:
  https://chat.com/callback?
    userToken=ABC123
    refreshToken=XYZ789
    state=user123:brucebem  ← MUST match exactly
```

**Key insight:** Bruce BEM just echoes `state` back. LibreChat handles all session matching logic via Redis polling.

## Consequences

### Positive
- Standard OAuth pattern - well documented
- LibreChat already implements polling architecture
- Bruce BEM changes minimal - just preserve `state`
- Multi-user concurrent auth works automatically

### Negative
- LibreChat's `state` is predictable (`userId:serverName`) not random CSRF token
- Relies on JWT auth gating flow creation for security

### Neutral
- Polling adds 3.5s latency to login completion detection

## Reference Links

### Must Read

- [LibreChat OAuth handler.ts](https://github.com/danny-avila/LibreChat/blob/main/packages/api/src/mcp/oauth/handler.ts) - State parameter generation (line 216)
- [LibreChat mcp.js callback](https://github.com/danny-avila/LibreChat/blob/main/api/server/routes/mcp.js) - Callback handling (lines 74-98)
- [OAuth 2.0 RFC 6749 §4.1.1](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1.1) - Official state parameter spec

### Should Read

- [LibreChat flow manager.ts](https://github.com/danny-avila/LibreChat/blob/main/packages/api/src/flow/manager.ts) - Redis polling implementation
- [LibreChat useMCPServerManager.ts](https://github.com/danny-avila/LibreChat/blob/main/client/src/hooks/MCP/useMCPServerManager.ts) - Client-side polling hook
- [Auth0 state parameter guide](https://auth0.com/docs/secure/attack-protection/state-parameters) - Beginner-friendly explanation
- [OAuth Security RFC 6819 §5.3.5](https://datatracker.ietf.org/doc/html/rfc6819#section-5.3.5) - Security best practices
- [LibreChat OAuth docs](https://www.librechat.ai/docs/configuration/authentication/OAuth2-OIDC) - Configuration reference


---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

