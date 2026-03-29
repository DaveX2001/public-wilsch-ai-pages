---
publish: true
---

# Rain Token Auth Update Guide

[[client-archibus]]

Instructions for updating an **existing** LibreChat deployment to support Rain token authentication.

## What Changed

LibreChat now captures Rain tokens from Bruce BEM iframe and passes them to the MCP server. This enables per-user authentication instead of shared credentials.

## Update Steps

### Step 1: Pull Latest Code

```bash
cd archibus-deploy
git pull origin main
```

### Step 2: Add Auto-Login Environment Variables

Add these to your `.env` file:

```bash
# Auto-login (bypasses LibreChat login screen)
AUTO_LOGIN_ENABLED=true
AUTO_LOGIN_EMAIL=admin@yourcompany.com
AUTO_LOGIN_PASSWORD=your-secure-password
```

### Step 3: Rebuild and Restart

```bash
docker compose -f docker-compose.dev.yml up -d --build
```

## Verification

1. Access LibreChat via Bruce BEM iframe (not directly)
2. Send a message that uses MCP tools (e.g., "Search for assets")
3. Check logs for successful auth:
   ```
   INFO:[BRUCE_BEM] Using Rain user token: abc123...
   ```

## Important Notes

- **Must access via Bruce BEM iframe** - Rain tokens come from URL params
- **Direct access will fail** - MCP rejects requests without tokens
- **MCP server is hosted** - No changes needed on your end for MCP

## Repository

```
https://github.com/veloxforce/archibus-deploy
```

---

*Last updated: 2026-01-09*
*Related: [Issue #7](https://github.com/DaveX2001/deliverable-tracking/issues/7)*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

