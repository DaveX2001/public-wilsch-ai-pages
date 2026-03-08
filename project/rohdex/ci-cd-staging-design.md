---
publish: true
---

# CI/CD & Staging Environment Design — Dokumentenverarbeitung
[[project-rohdex]]

Design for the CI/CD pipeline and staging environment of the email-based Dokumentenverarbeitung system. Production runs on RDX-APP-01, staging on Wilsch AI server.

---

## Problem Statement

The Dokumentenverarbeitung system runs on RDX-APP-01 as a single Docker container polling IONOS email (export-ki@rohdex.com). No staging environment exists — code changes reach production through ad-hoc rsync over VPN without a deploy script, CI pipeline, or verification step.

Previously, staging was blocked by an email isolation constraint: the system moves emails from INBOX to Processed after handling them, so two instances on the same inbox would race for emails. This constraint is now resolved — staging uses a separate Gmail account (rohdexautomation@gmail.com) on a separate server (Wilsch AI), eliminating the race condition entirely.

This design doc covers three areas: (1) how to isolate staging email from production, (2) how to deploy code given server constraints, and (3) how branches map to environments.

**Preconditions:**
- RDX-APP-01 is sole production (migration #1046 complete, old server stopped)
- No outbound GitHub access from RDX-APP-01 (Gmelch IT firewall)
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

Staging and production run on separate servers. The Makefile is the single deployment interface — all deployments go through `make` targets, never raw rsync or docker commands. This follows the [Standardized Makefile ADR](https://github.com/veloxforce/velox-global-adrs/blob/main/docker-compose-makefile-targets-standardization.md).

| Target | Server | Access | Compose File |
|--------|--------|--------|-------------|
| `make deploy` | RDX-APP-01 (prod) | VPN required | `docker-compose.prod.yml` |
| `make staging` | Wilsch AI server | Direct SSH | `docker-compose.staging.yml` |

#### Path A: rsync-push via Makefile (confirmed)

Each make target wraps the full deployment sequence: rsync code to the target server, SSH in, build the Docker image, and restart the container. The developer runs one command locally.

```
make deploy    →  VPN pre-check → rsync to RDX-APP-01 → SSH → docker compose build + up
make staging   →  rsync to Wilsch AI → SSH → docker compose build + up
```

**Standard targets per ADR:**

```makefile
.DEFAULT_GOAL := help  # Never deploys on bare `make`

# Production (RDX-APP-01 via VPN)
deploy          # rsync + SSH + docker compose -f docker-compose.prod.yml up -d --build
logs            # SSH + docker compose logs -f
status          # SSH + docker compose ps

# Staging (Wilsch AI server, direct SSH)
staging         # rsync + SSH + docker compose -f docker-compose.staging.yml up -d --build
staging-logs    # SSH + docker compose logs -f
staging-status  # SSH + docker compose ps

# Utility
help            # Display available commands (DEFAULT)
```

`.env` files are server-local — excluded from rsync (alongside `.git` and `__pycache__`). Each server maintains its own `.env` based on `.env.example`.

**Tradeoffs:** Simple, no external dependencies, works today. Deploy is tied to the commit via `/health` endpoint (`git_sha`). No audit trail beyond git history.

#### Path B: git-pull (future improvement)

If Gmelch IT opens outbound HTTPS access to `github.com` from RDX-APP-01, production deployment simplifies to `ssh RDX-APP-01 && git pull && make deploy` — eliminating the rsync step. GitHub Actions could further automate this on merge.

**Requirements for Gmelch IT:**
- Outbound HTTPS (port 443) to `github.com` and `*.githubusercontent.com`
- DNS resolution for these domains

**Undefined:** Gmelch IT network change request — who initiates, through which channel, and how to frame the ask for Sikander. See [Meeting Agenda: Gmelch IT Network Access](ci-cd-staging-agenda).

#### Rollback

If a deploy breaks production:
1. `git checkout` the previous working commit
2. rsync (or git pull) + `make deploy`
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
  → PR to staging (Marius reviews)
  → merge → manual deploy to staging
  → verify on staging (send test email, check generated documents)
  → PR to main
  → merge → manual deploy to production
```

**Review gate:** All PRs require Marius (SA) review before merge. This is the existing pattern from #1046 (PR #7 to staging).

**Deploy is always manual.** After merge, the developer runs `make deploy` or `make staging` via Claude Code terminal. The Makefile handles rsync, SSH, and docker compose — the developer does not run these commands individually.

#### Env Var Differences

Staging and production share the same `.env` structure. Only these values differ:

| Env Var | Production (RDX-APP-01) | Staging (Wilsch AI) |
|---------|------------------------|---------------------|
| `WHICH_IMAP` | `IONOS` | `GMAIL` |
| `WHICH_SMTP` | `IONOS` | `GMAIL` |
| `*_IMAP_EMAIL` | `export-ki@rohdex.com` | `rohdexautomation@gmail.com` |
| `*_IMAP_PASSWORD` | IONOS credentials | Gmail app password |
| Compose file | `docker-compose.prod.yml` | `docker-compose.staging.yml` |

Each server maintains its own `.env` (not rsynced). The `.env.example` in the repo serves as the template. All other env vars are identical between environments.

---

## Source

- **Issue:** [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067)
- **Epic:** [#909 Dokumentenverarbeitung SLA & Wartung](https://github.com/DaveX2001/deliverable-tracking/issues/909)
- **Migration:** [#1046](https://github.com/DaveX2001/deliverable-tracking/issues/1046) (closed — deployment patterns established here)
- **Design Doc:** [hosting-anforderungen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen) (system architecture reference)
- **Codebase:** [MariusWilsch/rohdex](https://github.com/MariusWilsch/rohdex) (default branch: staging)
- **Makefile ADR:** [Standardize Docker Compose Makefile Targets](https://github.com/veloxforce/velox-global-adrs/blob/main/docker-compose-makefile-targets-standardization.md)
- **Rubber-duck session:** [9d1941ec](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/9d1941ec-6175-4d1e-9df0-2a0b1301d055.jsonl)
- **v1 session:** [15641742](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/15641742-b196-41d6-85d2-13f45111ff65.jsonl)
- **SA review session:** [3509ecb1](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/3509ecb1-9381-4268-a4f3-0d0657eb8b30.jsonl)
- **v2 extraction pass:** [a96ef05d](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/a96ef05d-75d8-48f5-9e4b-233a9fab939a.jsonl)
