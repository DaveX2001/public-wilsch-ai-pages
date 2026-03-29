---
publish: true
---

# Config Console Feature
[[client-paul]]

## Why

Central administrative console for runtime configuration and access control in the Paul Queue video generation system.

**Problem it solves:**
- Provides secure admin interface for managing user accounts and system configuration without code changes
- Enables runtime modification of AI service providers, default models, and TTS settings
- Separates admin operations from regular user workflows through API-key authentication
- Allows dynamic configuration of AI services (OpenAI, Google Gemini, Anthropic Claude, RunAware) without redeployment

## What

### Admin Workflow

**Access:**
1. Navigate to config console (port 3004 locally)
2. Enter API key from `config/allowed_keys.json`
3. API key validated against `/api/accounts/roles` endpoint
4. Upon success, access two main tabs: Configuration and Account Management

**Configuration Tab:**
1. View and configure AI service provider API keys (OpenAI, Gemini, Anthropic, RunAware)
2. Select default AI model for transcript processing
   - Models grouped by provider
   - Only models with configured API keys are selectable
3. Configure TTS generator (F5-TTS or Chatterbox)
4. Google Drive integration (currently disabled)

**Account Management Tab:**
1. View all existing accounts with roles and status
2. Create new accounts with:
   - Username
   - Password (manual or auto-generated)
   - Multiple role assignments
   - Active/inactive status
3. Reset account passwords (manual or auto-generate)
4. Delete accounts (except admin account)
5. View account credentials after creation

### User Workflow

Users do not directly interact with this console. This is an admin-only interface for:
- Creating accounts for users who will access other parts of the system
- Configuring which AI services the system can use
- Setting system-wide defaults (default AI model, TTS generator)

### Roles

**API Key Authentication:**
- Console access requires API key from `config/allowed_keys.json`
- API keys validated via backend, not role-based
- Once authenticated, full admin access to all features

**Account Roles (for created accounts):**
- Roles assigned when creating accounts
- Multiple roles per account supported
- Backend provides role definitions and descriptions via `/api/accounts/roles`

**Protected Operations:**
- Admin account cannot be deleted (hardcoded username check)

## How

### Key Behaviors

**Configuration Management:**
- API keys stored server-side in environment variables and `.env` file
- Default model selection requires provider API key to be configured first
- TTS generator selection updates server configuration immediately

**Account Management:**
- Password generation creates secure random passwords
- Plain passwords only shown once immediately after creation/reset
- Account credentials displayed in temporary modal after creation
- Admin account protected from deletion by username check

**Navigation:**
- Cross-app navigation links fetched from backend (`/api/nav-links`)
- Links include: Queue, Script, Templates, Thumbnail

**Local Storage:**
- API key persisted in localStorage under `config_console_api_key`
- Sign out removes stored API key

### Data Model

Frontend does not directly access database tables. All operations through backend API.

**Inferred backend tables:**
- `accounts` - User accounts with username, password_hash, roles, is_active, created_at
- Configuration storage (file-based: `.env`, `config/allowed_keys.json`)

### API Endpoints

**Public:**
- `GET /api/nav-links` - Fetch navigation links

**API-Key Authenticated - Account Management:**
- `GET /api/accounts/roles` - Fetch available roles (also validates API key)
- `GET /api/accounts` - List all accounts
- `POST /api/accounts` - Create new account
- `GET /api/accounts/:id/credentials` - View account credentials
- `PUT /api/accounts/:id` - Update account (password)
- `POST /api/accounts/:id/reset-password` - Auto-generate and reset password
- `DELETE /api/accounts/:id` - Delete account
- `POST /api/accounts/generate-password` - Generate secure password

**API-Key Authenticated - Configuration:**
- `GET /api/config/apikeys` - Get status of configured API keys
- `POST /api/config/apikeys` - Save API key for provider
- `DELETE /api/config/apikeys/:provider` - Delete API key
- `GET /api/config/defaultmodel` - Get current default model
- `POST /api/config/defaultmodel` - Set default model
- `GET /api/config/models` - Get available models with availability
- `GET /api/config/current` - Get current configuration (TTS settings)
- `PUT /api/config/current/tts` - Update TTS configuration

### Frontend Components

**Entry Points:**
- `src/main.jsx` - React root mount
- `src/App.jsx` - Main application shell with routing and auth state

**Authentication:**
- `src/components/ApiKeyGate.jsx` - API key entry and validation gate

**Account Management:**
- `src/components/AccountManager.jsx` - Full account management interface

**Configuration Management:**
- `src/features/config/components/ConfigPage.jsx` - Main configuration page
- `src/features/config/components/DefaultModelSelector.jsx` - AI model selector
- `src/features/config/components/ProviderCard.jsx` - API key configuration card
- `src/features/config/components/GoogleDriverSetup.jsx` - Google Drive (disabled)

**Business Logic:**
- `src/features/config/hooks/useConfigManagement.js` - Configuration state
- `src/shared/data/useConfigData.js` - Data fetching and caching
- `src/lib/apiClient.js` - Axios client with API key headers
- `src/core/services/config.js` - Configuration API service
- `src/core/registry/models.js` - AI providers and models registry

## Done

### Success Criteria
- Admin can authenticate with API key
- Admin can create/manage user accounts
- Admin can configure AI provider API keys
- Admin can select default AI model
- Admin can configure TTS generator
- Account credentials shown once after creation

### Implementation Status
- **State:** Standalone React app (Vite), port 3004
- **Auth:** API key-based via ApiKeyGate component
- **Backend:** Requires Flask backend (port 5001)
- **Tech:** React 18, Vite 5, Axios, Radix UI, Tailwind CSS

### Related
- Issue #227: Absorb Config into Main (2/4)
- Issue #121: Frontend Consolidation parent issue
- ADR: Strangler Pattern Sequencing

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

