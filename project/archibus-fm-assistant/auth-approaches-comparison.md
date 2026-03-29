---
publish: true
---

# Authentication Approaches Comparison

[[client-archibus]]

## Approach A: Iframe (Current - Working)

```mermaid
sequenceDiagram
    participant User
    participant BruceBEM as Bruce BEM
    participant LibreChat
    participant MCP

    User->>BruceBEM: 1. Login to Bruce BEM
    BruceBEM->>BruceBEM: 2. Authenticate user
    BruceBEM->>BruceBEM: 3. Generate userToken + refreshToken
    BruceBEM->>LibreChat: 4. Open iframe with ?userToken=...&refreshToken=...
    Note over LibreChat: Tokens captured from URL
    LibreChat->>MCP: 5. API calls with tokens in headers
    MCP->>BruceBEM: 6. Backend API calls (authenticated)
```

**Why it works:** Bruce BEM controls iframe creation. Tokens passed directly via URL params. No OAuth redirect = no state parameter needed.

---

## Approach B: Standalone OAuth (Blocked)

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant LibreChat
    participant Redis
    participant BruceBEM as Bruce BEM<br/>(Code on Time)

    User->>LibreChat: 1. Visit chat.example.com
    User->>LibreChat: 2. Click "Login with Bruce BEM"
    LibreChat->>Redis: 3. Store flow {state: "abc123", status: PENDING}
    LibreChat->>Browser: 4. Redirect to BruceBEM /login?state=abc123
    Browser->>BruceBEM: 5. Load login page

    Note over BruceBEM: Code on Time framework<br/>STRIPS state parameter

    BruceBEM->>BruceBEM: 6. User authenticates
    BruceBEM->>BruceBEM: 7. Generate tokens
    BruceBEM->>LibreChat: 8. Callback: /callback?userToken=...&state=???

    Note over LibreChat: state missing or wrong<br/>Cannot match to browser session

    LibreChat->>LibreChat: 9. FAIL: Which user is this for?
```

**Why it fails:** Code on Time framework strips "irrelevant" query params during internal redirect to login form. State parameter lost = session matching impossible.

---

## Approach C: Tiny Auth App (Rein's Proposal)

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant LibreChat
    participant Redis
    participant TinyApp as Tiny Auth App<br/>(We Control)
    participant BruceBEM as Bruce BEM API

    User->>LibreChat: 1. Visit chat.example.com
    User->>LibreChat: 2. Click "Login with Bruce BEM"
    LibreChat->>Redis: 3. Store flow {state: "abc123", status: PENDING}
    LibreChat->>Browser: 4. Redirect to TinyApp /login?state=abc123
    Browser->>TinyApp: 5. Load login form

    Note over TinyApp: Simple app we control<br/>State preserved in session/URL

    User->>TinyApp: 6. Enter credentials
    TinyApp->>BruceBEM: 7. Validate credentials (API call)
    BruceBEM-->>TinyApp: 8. Valid + user info
    TinyApp->>TinyApp: 9. Generate userToken + refreshToken
    TinyApp->>LibreChat: 10. Callback: /callback?userToken=...&state=abc123

    Note over LibreChat: state intact!<br/>Session matching works

    LibreChat->>Redis: 11. Update flow {state: "abc123", status: COMPLETED, tokens}
    Browser->>LibreChat: 12. Poll detects completion
    Note over Browser: Login successful
```

**Why it works:** Tiny app is outside Bruce BEM UI. We control it completely. No Code on Time framework = state parameter preserved.

---

## Key Difference

| Aspect | Approach B (OAuth) | Approach C (Tiny App) |
|--------|-------------------|----------------------|
| Login UI | Bruce BEM's login page | Our own login form |
| Framework | Code on Time (strips state) | None (we control) |
| Auth validation | Bruce BEM handles internally | Tiny app calls Bruce BEM API |
| Token generation | Bruce BEM creates tokens | Tiny app creates tokens |
| State preservation | Broken | Works |


---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

