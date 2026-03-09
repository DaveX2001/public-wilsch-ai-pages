---
publish: true
---

# Deployment Pattern Standardization
[[deployment-pattern-standardization-design]]

Design doc for consolidating three proven deployment patterns into the Developer Operations Manual as the single source of truth.

---

## Problem Statement

Deployment knowledge is scattered across project-specific docs, generic ADRs, and server configs. Each new project rediscovers how to deploy by reading klimafolgenschutz's README, ROHDEX's Makefile, or SSHing into the VPS to inspect Caddy configs. Three proven patterns exist in production — but no single operational source tells a developer (or AI agent) which pattern to use and how to execute it.

This is not about inventing new deployment patterns. All three are proven and running. This is a consolidation task: extract what works from scattered sources into the Developer Operations Manual as the single source of truth.

**Preconditions:**
- Three deployment patterns are proven in production: Docker Compose on VPS (ROHDEX), Vercel Hobby multi-dev (klimafolgenschutz), Caddy subdomain routing (wilsch-deployment.com)
- The Developer Operations Manual has explicit gaps at staging setup and deploy procedures (Path B: Spec-Implement)
- Existing ADRs (Makefile targets, Caddy conf.d, Vercel branch environments) document decisions but not operational steps
- #893 (DNS Subdomain Setup) is blocked until these patterns are operationalized

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | The Developer Operations Manual contains actionable deployment sections for Docker Compose, Vercel Hobby, and Caddy Subdomain — filling Path B's staging/deploy gaps. |
| **Success** | Each pattern explains how we did it, why we did it, and that it works. A developer (or AI agent) starting a new project can identify which pattern(s) apply from a routing table and follow the steps without searching other repos. |
| **Done test** | All 3 patterns absorbed into the manual. Old ADRs deprecated with redirect. Hippocampus copies deleted. No scattered duplicates remain. #893 unblocked. |

---

## Approach

### Part 1: Design Doc Format

Each deployment pattern gets a standalone design doc following the same template with three dimensions:

1. **How we did it** — the concrete implementation: file structure, commands, configuration, workflow files
2. **Why we did it** — the decision rationale: why this pattern over alternatives, constraints that shaped the choice
3. **That it works** — proof: which projects run this in production, what evidence confirms it

Content depth varies naturally per pattern. Caddy's "how" is 5 steps; Vercel Hobby's is 15 steps with 3 workflow files and a git author workaround. Same template, different depth — no artificial constraints.

Each design doc links to its reference implementation (not embeds). The klimafolgenschutz repo, ROHDEX repo, and VPS configs stay as living examples. The design doc explains; the reference impl demonstrates.

### Part 2: Three Design Docs

**Docker Compose Backend** — generalized from ROHDEX
- Source: `MariusWilsch/rohdex` (Dockerfile, docker-compose.yml, Makefile)
- Covers: Dockerfile conventions, compose file structure, Makefile as deployment interface (`make deploy` / `make staging`), env separation
- Reference: #1067 design doc (CI/CD staging for email-based system)

**Vercel Hobby Multi-Dev Deploy** — generalized from klimafolgenschutz
- Source: `DaveX2001/klimafolgenschutz-website/README.VERCEL_DEPLOY_WITHOUT_PRO.MD`
- Covers: GHA + Vercel CLI deploy token pattern, git author workaround, 3 workflows (staging, production, preview), branch strategy
- Complements existing Vercel ADR (#011) — the ADR says "use Vercel for frontends." This doc says "here's how to enable multi-dev on Hobby plan." Both get absorbed into the same manual section.
- Reference: #849 implementation, DaviZCodes external reference

**Caddy Subdomain Routing** — generalized from VPS
- Source: VPS `91.99.74.207:/etc/caddy/conf.d/` (23 active configs)
- Covers: Wildcard DNS + Caddy reverse proxy + auto-SSL, conf.d file organization, service enable/disable pattern, validation + reload steps
- Unblocks: #893 (DNS Subdomain Setup for UWI client apps)
- Reference: existing `caddy-conf-subdomain-deployment-pattern.md`

Design docs are stepping stones — they exist to get the content right and provide a validation surface for SA review. Once absorbed into the Operations Manual, they are archived.

### Part 3: Manual Absorption

The Developer Operations Manual is the single source of truth after absorption. The design docs explain (Marius reviews). The manual section executes (AI follows steps).

**Placement:** Fill Path B (Spec-Implement) gaps first. The manual already lists "staging environment setup" and "deploy procedures" as gaps in its deploy step. The 3 patterns fill these directly. If content doesn't fit Path B's flow, it gets a new section.

**Routing table:** The manual adds a deployment routing section at the top of the deploy area:

> When deploying, identify which patterns apply:
> - Backend on VPS? → Docker Compose section
> - Frontend on Vercel? → Vercel Hobby section
> - Needs subdomain? → Caddy section
>
> Combining: Deploy services first (Docker/Vercel), then configure Caddy last (it needs a running target to proxy to).

This handles cross-pattern projects (e.g., Invoice Agent uses all three) without each pattern needing to reference the others.

**Key commands embedded inline** in the manual section — the manual is self-contained for AI execution. Links back to reference impls for working examples.

### Part 4: Deprecation & Cleanup

After absorption, source documents are cleaned up:

| Document | Location | Action |
|----------|----------|--------|
| Makefile ADR | `velox-global-adrs` | Status → Deprecated + redirect to manual |
| Caddy pattern doc | `velox-global-adrs` | Status → Deprecated + redirect to manual |
| Vercel ADR (#011) | `velox-global-adrs` | Status → Deprecated + redirect to manual |
| Makefile ADR copy | `hippocampus/global` | Delete |
| Caddy doc copy | `hippocampus/global` | Delete |
| Vercel staging copy | `hippocampus/global` | Delete (if exists) |

The `velox-global-adrs` copies stay as historical decision records with a redirect note: *"Superseded by Developer Operations Manual, Deployment Patterns section."* The hippocampus copies are deleted — the manual in hippocampus is the SSoT.

---

## Source

- **Transcript:** [2026-02-24 Daily Sync](https://drive.google.com/file/d/17Iw0W6LHk9iVTL17Hp73Bfxrf7tNoxEQ/view)
- **Issues:** [#712](https://github.com/DaveX2001/deliverable-tracking/issues/712), [#893](https://github.com/DaveX2001/deliverable-tracking/issues/893), [#849](https://github.com/DaveX2001/deliverable-tracking/issues/849), [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067), [#726](https://github.com/DaveX2001/deliverable-tracking/issues/726)
- **Reference Impls:** [klimafolgenschutz Vercel README](https://github.com/DaveX2001/klimafolgenschutz-website/blob/staging/README.VERCEL_DEPLOY_WITHOUT_PRO.MD), [ROHDEX Docker stack](https://github.com/MariusWilsch/rohdex/blob/main/docker-compose.yml), [ROHDEX Makefile](https://github.com/MariusWilsch/rohdex/blob/main/Makefile)
- **Existing ADRs:** [Makefile ADR](https://github.com/veloxforce/velox-global-adrs/blob/main/docker-compose-makefile-targets-standardization.md), [Caddy pattern](https://github.com/veloxforce/velox-global-adrs/blob/main/caddy-conf-subdomain-deployment-pattern.md), [Vercel ADR](https://github.com/veloxforce/velox-global-adrs/blob/main/use-vercel-for-frontend-deployments-with-branch-environments.md)
- **Operations Manual:** [Developer Operations Manual](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services)
- **Sessions:** [Rubber duck 620ef53e](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/620ef53e-c84d-49f9-8cf3-8312f72aff48.jsonl), [Probe cb31db0d](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/cb31db0d-980c-4200-8583-adee2c9328cb.jsonl)
