---
publish: true
---

# CI/CD & Staging Environment Design — Dokumentenverarbeitung
[[project-rohdex]]

Design for the CI/CD pipeline and staging environment of the email-based Dokumentenverarbeitung system. Production runs on RDX-APP-01, staging on Wilsch AI server. Both deploy via git pull — single deployment model.

---

## Problem Statement

The Dokumentenverarbeitung system runs on RDX-APP-01 as a single Docker container polling IONOS email (export-ki@rohdex.com). No staging environment exists — code changes reach production through ad-hoc rsync over VPN without a deploy script, CI pipeline, or verification step.

Previously, staging was blocked by an email isolation constraint: the system moves emails from INBOX to Processed after handling them, so two instances on the same inbox would race for emails. This constraint is now resolved — staging uses a separate Gmail account (rohdexautomation@gmail.com) on a separate server (Wilsch AI), eliminating the race condition entirely.

This design doc covers three areas: (1) how to isolate staging email from production, (2) how to deploy code given server constraints, and (3) how branches map to environments.

**Preconditions:**
- RDX-APP-01 is sole production (migration #1046 complete, old server stopped)
- RDX-APP-01 has outbound GitHub access (confirmed 2026-03-09: `curl github.com` → 200, `git clone` public → works, SSH port 22 → reachable)
- Deploy key required on RDX-APP-01 for private repo access (ed25519, read-only)
- VPN-only access to server (OpenVPN → 192.168.44.11)
- Wilsch AI server available for staging (clean after #1046 cutover — no ROHDEX state)
- Gmail account (rohdexautomation@gmail.com) available for staging email
- Codebase: MariusWilsch/rohdex (default branch: staging)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Code changes are verified on a staging environment before reaching production email processing |
| **Success** | A developer can deploy to staging, verify document generation works, then promote to production — without affecting production email processing |
| **Done test** | Can a developer follow this document end-to-end to: (1) deploy to staging, (2) test without affecting production, (3) promote to production? If yes → design is complete |

---

## Approach

### Part 1: Staging Email Isolation

The system polls IMAP and **moves** emails from INBOX to a Processed or Skipped folder after handling them. Two instances polling the same inbox creates a first-poller-wins race condition. This is resolved by running staging and production on separate servers with separate email accounts.

| Environment | Server | Email Provider | Account |
|-------------|--------|---------------|---------|
| Production  | RDX-APP-01 | IONOS | `export-ki@rohdex.com` |
| Staging     | Wilsch AI server | Gmail | `rohdexautomation@gmail.com` |

The codebase already supports provider switching via `WHICH_IMAP` / `WHICH_SMTP` env vars — setting `WHICH_IMAP=GMAIL` on the staging server points it at Gmail. No code changes required.

**Prerequisite:** Gmail app password for `rohdexautomation@gmail.com` — obtain from Marius before staging setup.

**Why Gmail instead of a second IONOS mailbox:** Wilsch AI cannot create IONOS mailboxes (app-only credentials). Gmail eliminates the external dependency on Rohdex/Gmelch IT entirely. The Gmail account and app password already exist from prior development.

### Part 2: Deployment Mechanism

Both servers deploy via **git pull** — the same mechanism for staging and production. The Makefile is the single deployment interface — all deployments go through `make` targets, never raw docker commands. This follows the [Standardized Makefile ADR](https://github.com/veloxforce/velox-global-adrs/blob/main/docker-compose-makefile-targets-standardization.md).

| Target | Server | Access | Compose File |
|--------|--------|--------|-------------|
| `make deploy` | RDX-APP-01 (prod) | VPN required | `docker-compose.prod.yml` |
| `make staging` | Wilsch AI server | Direct SSH | `docker-compose.staging.yml` |

#### Deployment Commands

Both environments use the same git-pull mechanism. The developer runs one command locally.

```
make deploy    →  VPN pre-check → SSH to RDX-APP-01 → git pull → docker compose build + up
make staging   →  SSH to Wilsch AI → git pull → docker compose build + up
```

**Why git pull on both:** RDX-APP-01 has confirmed outbound GitHub access (tested 2026-03-09). Git pull ensures the server always matches a committed state — the `git_sha` from `/health` proves exactly which commit is running. This answers "how do we know the code is correct?" — the git SHA is the verification.

**Deploy key (RDX-APP-01):** Private repo access requires a deploy key (ed25519, read-only). Generated at `~/.ssh/github_deploy` with SSH config pointing `github.com` to the key. Wilsch AI server already has GitHub access via existing credentials.

**Standard targets per ADR:**

```makefile
.DEFAULT_GOAL := help  # Never deploys on bare `make`

# Production (RDX-APP-01 via VPN)
deploy          # SSH + git pull + docker compose -f docker-compose.prod.yml up -d --build
logs            # SSH + docker compose logs -f
status          # SSH + docker compose ps

# Staging (Wilsch AI server, direct SSH)
staging         # SSH + git pull + docker compose -f docker-compose.staging.yml up -d --build
staging-logs    # SSH + docker compose logs -f
staging-status  # SSH + docker compose ps

# Utility
help            # Display available commands (DEFAULT)
```

`.env` files are server-local — not transferred during deployment (not tracked in git). Each server maintains its own `.env` based on `.env.example`.

**Tradeoffs:** Simple, no external dependencies, works today. Deploy is tied to the commit via `/health` endpoint (`git_sha` + `build_time`). No audit trail beyond git history.

#### Rollback

If a deploy breaks production:
1. SSH to server, `git checkout` the previous working commit
2. `docker compose -f docker-compose.prod.yml up -d --build`
3. Verify via `/health` endpoint (`git_sha` matches expected commit)

Future improvement: tag Docker images before deploying (`docker tag rohdex-backend:latest rohdex-backend:$(git rev-parse --short HEAD)`) to enable instant rollback without rebuild.

### Part 3: Branch-to-Environment Mapping

| Branch | Server | Email | Deploy Command |
|--------|--------|-------|----------------|
| `main` | RDX-APP-01 (prod) | IONOS (`export-ki@rohdex.com`) | `make deploy` |
| `staging` | Wilsch AI (staging) | Gmail (`rohdexautomation@gmail.com`) | `make staging` |
| `issue-*` | Local dev only | N/A | N/A |

**`staging` is the default branch** (set during #1046 migration). All feature branches are created from and merged back to `staging`.

#### Merge and Deploy Flow

```
feature branch (issue-XXX)
  → PR to staging (David merges autonomously)
  → merge → make staging (deploy to staging)
  → verify on staging (send test email, check generated documents)
  → PR to main (Marius reviews)
  → merge → make deploy (deploy to production via VPN)
```

**Deploy authority:** David has authority to merge feature branches to staging and deploy to staging independently. Marius only reviews the staging → main promotion.

**Deploy is always manual.** After merge, the developer runs `make deploy` or `make staging` via Claude Code terminal. The Makefile handles the full deployment sequence — the developer does not run SSH, git, or docker compose commands individually.

#### Env Var Differences

Staging and production share the same `.env` structure. Only these values differ:

| Env Var | Production (RDX-APP-01) | Staging (Wilsch AI) |
|---------|------------------------|---------------------|
| `WHICH_IMAP` | `IONOS` | `GMAIL` |
| `WHICH_SMTP` | `IONOS` | `GMAIL` |
| `*_IMAP_EMAIL` | `export-ki@rohdex.com` | `rohdexautomation@gmail.com` |
| `*_IMAP_PASSWORD` | IONOS credentials | Gmail app password |
| Compose file | `docker-compose.prod.yml` | `docker-compose.staging.yml` |

Each server maintains its own `.env` (not transferred during deployment). The `.env.example` in the repo serves as the template. All other env vars are identical between environments.

---

## Deploy Key Setup (RDX-APP-01)

The private repo requires a deploy key for git pull access. Setup steps:

1. **Key generated** (2026-03-09): `~/.ssh/github_deploy` on RDX-APP-01
2. **SSH config written:** `~/.ssh/config` points `github.com` to the deploy key
3. **Pending:** Public key must be added to GitHub repo (Settings → Deploy keys, read-only)
   - Requires admin access to `MariusWilsch/rohdex`
   - Public key: `ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIW8GJ3zRB6N4XnNkwzz+JDsM34qEOMX0dmH6A22EMUp RDX-APP-01-deploy`
4. **Verify:** After adding, test with `ssh -T git@github.com` from RDX-APP-01

---

## Source

- **Issue:** [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067)
- **Epic:** [#909 Dokumentenverarbeitung SLA & Wartung](https://github.com/DaveX2001/deliverable-tracking/issues/909)
- **Migration:** [#1046](https://github.com/DaveX2001/deliverable-tracking/issues/1046) (closed — deployment patterns established here)
- **Design Doc:** [hosting-anforderungen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen) (system architecture reference)
- **Codebase:** [MariusWilsch/rohdex](https://github.com/MariusWilsch/rohdex) (default branch: staging)
- **Makefile ADR:** [Standardize Docker Compose Makefile Targets](https://github.com/veloxforce/velox-global-adrs/blob/main/docker-compose-makefile-targets-standardization.md)
- **GitHub access test:** Marius SSH session 2026-03-09 (curl → 200, git clone → works, SSH port 22 → reachable)
- **Rubber-duck session:** [9d1941ec](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/9d1941ec-6175-4d1e-9df0-2a0b1301d055.jsonl)
- **v1 session:** [15641742](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/15641742-b196-41d6-85d2-13f45111ff65.jsonl)
- **SA review session:** [3509ecb1](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/3509ecb1-9381-4268-a4f3-0d0657eb8b30.jsonl)
- **v2 extraction pass:** [a96ef05d](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/a96ef05d-75d8-48f5-9e4b-233a9fab939a.jsonl)
- **v3 AC session:** [6aea89cf](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/6aea89cf-a05b-4794-88cb-fd310118856f.jsonl)
