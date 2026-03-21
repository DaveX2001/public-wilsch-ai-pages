---
publish: true
---

# Deployment & Runtime State — Convention Over Configuration
[[docker-deployment]]

Design for eliminating the deployment decision surface through runtime state codification, standardized project recipes, and convention-over-configuration. Absorbs CCI #646 (Deployment & Staging Workflow Not Embodied) and CCI #640 (CI/CD Auto-Deployment).

---

## Problem Statement

A git clone followed by `docker compose up` does not produce a working system. Runtime state — Docker volumes, agent configurations, pipeline prompts, database seeds — lives outside git. When this state is lost, manually recreated, or deployed to the wrong container, every downstream process breaks: testing, verification, and deployment.

This creates a structural gap between the **worktree** (isolated code) and the **running system** (code + runtime state). The developer works in a worktree that represents code, not the system. Testing requires the full system, which only exists on staging — with state that no one version-controls. The gap widens with every manual `docker cp`, every untracked agent definition, every prompt that lives in a volume instead of a file.

Documentation-based fixes (CLAUDE.md deployment identity) do not close this gap. IITR's CLAUDE.md had a 7-section schema including Server Access, Deployment, and Domains — the agent still went to the wrong path twice in the same session. Research confirms: more agent context rules [reduce task success rates while increasing inference cost](https://www.augmentcode.com/blog/your-agents-context-is-a-junk-drawer) (ETH Zurich, 2026), and [passive context outperforms active retrieval](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals) by eliminating decision points (Vercel, 2026). Adding more instructions to CLAUDE.md — including deployment identity — increases noise in a context window where every line competes for the agent's attention.

The fix is structural, not informational: everything needed to produce a working system must be derivable from git.

**Preconditions:**
- Self-hosted Docker Compose backends across multiple servers
- Worktree-based development (code isolation, draft PRs)
- AI agents making deployment decisions (container selection, path routing, Docker commands)
- Multiple deployment targets per project (staging + production, sometimes multiple servers)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Git is the single source of truth. The running system is derived from it — deterministically, without manual steps, without agent decisions about infrastructure. |
| **Success** | A developer creates a worktree for an issue and receives a testable running system (not just isolated code). Push to staging branch produces a deployed, verifiable system. No SSH. No manual Docker commands. No CLAUDE.md deployment instructions. |
| **Done test** | Developer pushes to worktree branch → platform spins up a working system automatically. No manual compose, no seed command, no SSH. The push IS the deployment. Fallback litmus test for the recipe itself: `git clone` + `docker compose up` + `seed` with only `.env` credentials must also produce a working system — this validates the recipe is complete, even when the platform handles execution. |

---

## Approach

### Part 1 — The Three Layers

A Docker backend has three layers. The deployment confusion pattern (#646, #635) always originates in Layer 3.

| Layer | What | In Git? | Reproducible? |
|-------|------|---------|---------------|
| **Code** | Application source, tests, configs | Yes | Yes — `git clone` |
| **Container images** | Docker build from code | Derived | Yes — `docker compose build` |
| **Runtime state** | Volumes, agent configs, DB seeds, prompts, Caddy routing | Partially | No — manual reconstruction |

Layer 1 and 2 are solved. Worktrees isolate code. Docker builds images deterministically from code. The entire Ship with Confidence workflow (AC verify, smoke test, human witness) operates on Layer 1 and 2.

Layer 3 is where state lives outside git:
- **LibreChat agent definitions** live in MongoDB, not in code (Archibus #1189: deployed to wrong container volume)
- **Pipeline prompts** deployed via `docker cp`, never committed (IITR #1191 Finding 4: golden prompt lost during migration)
- **Database state** seeded manually after container startup
- **Caddy routing** configured via SSH, not version-controlled per project

Every deployment failure in the CCI #646 evidence chain traces back to Layer 3. The fix targets this layer specifically.

### Part 2 — Git = Truth

The direction of truth flips:

| | Today | Convention |
|---|-------|-----------|
| **Truth lives in** | Running system (staging server) | Git repository |
| **Git contains** | Partial copy (code + compose) | Complete recipe (code + compose + seed) |
| **Deploy means** | Manual reconstruction of runtime state | Automated derivation from git |
| **State drift** | Accumulates silently between deploys | Impossible — every deploy starts from recipe |

This is the same principle as infrastructure-as-code (Terraform), applied to Docker runtime state. The system state IS the code.

**What can be codified:**

| Component | Codification method | Already done? |
|-----------|-------------------|---------------|
| DB schemas | Migration scripts | Yes (most projects) |
| DB seed data | SQL/JSON seed scripts in `seed/` | Rarely |
| Pipeline filters / prompts | Git-tracked files, volume-mounted | IITR fixed post-Finding 4 |
| LibreChat agent definitions | MongoDB seed scripts (JSON export) | No — [feature requested upstream](https://github.com/danny-avila/LibreChat/discussions/10019) |
| Caddy routing | `.conf` files in repo, deployed with project | IITR partially |
| Docker networking | Defined in docker-compose.yml | Yes |

**What cannot be codified (irreducible):**
- **Credentials** — `.env` files, gitignored. Injected from secrets management or manual setup.
- **GPU hardware** — Ollama on IBM Power or dedicated GPU servers. Physical constraint.
- **External service state** — IMAP inboxes, third-party API state. Real-world dependency.

The convention requires: everything above the irreducible line MUST be in git. Everything below is accepted as an external dependency with a documented injection point.

### Part 3 — The Recipe Convention

Every project ships with a **recipe**: the complete set of instructions to produce a working system from a git clone. The recipe is not documentation — it is executable code.

**The convention:** Every named volume in `docker-compose.yml` must have a corresponding seed mechanism in git. The volume is the interface between git and the running system — what's inside (MongoDB data, prompt files, pipeline configs) is a project-specific implementation detail.

**Standard project structure:**

```
project-root/
├── docker-compose.yml          # Layer 2: container definitions (declares volumes)
├── docker-compose.staging.yml  # Environment override (staging)
├── docker-compose.prod.yml     # Environment override (production)
├── seed/                       # Layer 3: one entry per named volume
│   ├── seed.sh                 # Entrypoint: seeds all volumes in order
│   └── {volume-name}/          # Seed data for each named volume
├── .env.example                # Credential template (injection points)
└── Makefile                    # Developer interface: make up, make seed, make staging
```

The seed mechanism per volume is project-specific: SQL init scripts, MongoDB JSON exports, file copies, or volume tarballs. The convention doesn't prescribe the format — it prescribes that every volume has one.

**The litmus test:** `cp .env.example .env && <fill credentials> && docker compose up -d && ./seed/seed.sh` produces a working system. If it doesn't, the recipe is incomplete.

**Makefile as convention surface:**

| Target | What it does |
|--------|-------------|
| `make up` | Start all containers (compose up) |
| `make seed` | Run seed/seed.sh (populate runtime state) |
| `make staging` | Full staging deploy (up + seed + health check) |
| `make health` | Verify system health (curl endpoints, check containers) |

The Makefile is the only interface the developer (or CI/CD, or platform) uses. No raw Docker commands. No SSH into containers. The Makefile IS the convention.

**Undefined:** Makefile evolution — if push = working system (platform handles everything), the Makefile becomes platform infrastructure, not a developer interface. How does this change the convention? Does the Makefile become what `package.json` build scripts are to Vercel — something the platform calls, not the developer?

**Undefined:** Concrete recipe contents per project type (ROHDEX simple backend vs. IITR two-layer compose vs. Archibus LibreChat with agent state). Requires per-project audit in next extraction pass.

**Undefined:** Staging → git reverse flow — when runtime state is created manually on staging (e.g., new agent configured via LibreChat UI), how does it get exported back into the `seed/` directory? This is the maintenance cycle for recipes.

### Part 4 — Preview Environments: Closing the Worktree Gap

The worktree gives the developer isolated code. The recipe defines a reproducible system. A **preview environment** connects the two: when a worktree creates a PR, a platform automatically spins up the recipe into a running system with its own URL.

**Target flow:**

1. Developer creates worktree → worktree skill creates draft PR (existing behavior)
2. Platform detects PR → runs `docker compose up` + `seed/seed.sh` from that branch
3. Preview URL assigned (e.g., `issue-1189.wilsch-deployment.com`)
4. Developer has: code locally (worktree) + running system remotely (preview)
5. PR merged or closed → preview torn down automatically

This is the Vercel model applied to Docker backends. The developer never SSHs. The agent never chooses a container. The platform IS the deployment.

**Self-hosted PaaS options evaluated:**

| Platform | Preview PRs | CLI | Server overhead | Fit |
|----------|------------|-----|-----------------|-----|
| **Coolify** | Built-in (GitHub App) | Limited | Server daemon | Strongest preview support |
| **Dokploy** | Supported | Limited | Docker Swarm | Better multi-server |
| **Kamal** (37signals) | Not supported | CLI-first | Zero daemon | No preview environments |

Coolify and Dokploy both provide the preview environment primitive. Kamal does not — it solves deployment automation but not the worktree→system gap.

**The platform does it, not the agent.** The agent writes code and pushes. The platform reacts to the push. No AI involvement in infrastructure decisions.

**Structural enforcement: read-only SSH.** Rather than instructing agents "don't deploy via SSH" (documentation — ignorable), restrict the agent's SSH key to read-only operations on the server. The key can check logs, health, and container status, but physically cannot run `docker compose up`, `git pull`, or modify files. Deployment becomes impossible via SSH — it can only happen through the platform on push. This is the same "no decision point" principle from the Vercel research: don't ask the agent to make the right choice, remove the wrong choice entirely.

**Undefined:** Development topology — does the developer work locally (worktree) with a remote preview on the server? Or does development happen on the server directly? GPU-dependent services (IITR Ollama) cannot run locally — the preview environment on the server must provide them. Hybrid model likely needed.

**Undefined:** Platform selection — requires a spike. Prior experience: Coolify was used in 2025 and abandoned due to unreliable deployments ("stuff just not deploying"). Root cause unclear — may have been a Layer 3 problem (no recipe) rather than a Coolify problem. Nixpacks angle: platforms using Nixpacks (Coolify, Railway) auto-detect language and build without Dockerfiles — convention-over-configuration for the build step, which combined with the recipe convention for seeding could deliver push → build → seed → preview URL with zero configuration. Evaluation criteria: preview environment quality, Docker Compose support, Nixpacks reliability, CLI/API access, resource overhead on WILSCH-AI-SERVER.

**Undefined:** Recipe debugging — when the platform-triggered recipe fails, the developer needs a local escape hatch (`make seed` locally). How does the developer get visibility into platform-side failures? Logs, error reporting, fallback to manual execution.

**Undefined:** Worktree skill integration — the current worktree skill (Step 5) asks about Docker volume copying. With preview environments, Step 5 changes: instead of copying volumes locally, the platform spins up a preview from the recipe. How does the worktree skill encode this? Does it trigger the platform via API? Or does the PR creation (Step 4) already trigger the platform automatically via GitHub App webhook?

### Part 5 — CLAUDE.md Implications: What Dies, What Stays

The Vercel AGENTS.md research and Augment's "junk drawer" analysis converge on one finding: agent context files should contain only what prevents specific failures the agent would otherwise make. Everything else is noise that degrades performance.

Applied to deployment, combined with CCI #659 (CLAUDE.md as Router):

**What dies (platform handles it):**
- Deployment identity (container name, port, server path) — the recipe IS the identity
- Branch→server mapping — the platform routes branches to environments
- `make staging` instructions — CI/CD or platform triggers on push
- Caddy subdomain convention — platform manages routing
- SSH access details — no one SSHs anymore

**What stays (prevents failures the agent would make):**
- Build/test/run commands (`uv sync`, `uv run pytest`)
- Known gotchas that aren't in code (e.g., "temperature 0 for RAG with small models")
- Architecture decisions not obvious from reading code
- Credential locations (which `.env` vars are needed, where to get them)
- Route to recipe: `→ See seed/ for runtime state setup`

**The filter (from Augment):** For each CLAUDE.md line, ask: "Would the agent make a mistake without this?" If no — delete it. If the answer is "yes, but the recipe or platform handles it now" — also delete it.

This aligns with #659's router principle: CLAUDE.md routes to source files where truth lives. The recipe (`seed/`, `docker-compose.yml`, `Makefile`) IS the source of deployment truth. CLAUDE.md doesn't restate it.

**Recipe authorship:** The Developer owns the recipe. The recipe is code — seed scripts, compose files, Makefile targets — written and maintained as part of the implementation, not as a separate infrastructure concern. The JA may define recipe requirements in the design doc (e.g., "this project needs volume seeds for X and Y"), but the Developer implements and maintains them, same as application code.

---

## Source

**CCI Issues:**
- [#646 — Release: Deployment & Staging Workflow Not Embodied](https://github.com/DaveX2001/claude-code-improvements/issues/646) (focus issue, 5 observations)
- [#640 — CI/CD Auto-Deployment for Staging Branch](https://github.com/DaveX2001/claude-code-improvements/issues/640) (absorbed into #646)
- [#635 — DaveX2001 Operator Epic](https://github.com/DaveX2001/claude-code-improvements/issues/635) (parent, 10 deployment observations)
- [#659 — CLAUDE.md as Router](https://github.com/DaveX2001/claude-code-improvements/issues/659) (connected — context engineering)
- [#657 — Trunk-First Decomposition](https://github.com/DaveX2001/claude-code-improvements/issues/657) (#1189 wrong-container evidence)

**Evidence Cases (deliverable-tracking):**
- [#1189 — E2E Agent Pipeline](https://github.com/DaveX2001/deliverable-tracking/issues/1189) (deployed to spike-a instead of staging)
- [#1191 — IITR Mono-repo Migration](https://github.com/DaveX2001/deliverable-tracking/issues/1191) (4 witness findings, golden prompt lost)
- [#1067 — ROHDEX CI/CD](https://github.com/DaveX2001/deliverable-tracking/issues/1067) (staging authority established, manual step remains)
- [#712 — Deployment Pattern Standardization](https://github.com/DaveX2001/deliverable-tracking/issues/712) (closed, absorbed into ops manual)

**Research:**
- [Vercel: AGENTS.md outperforms skills](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals) — passive context beats active retrieval, "no decision point"
- [Augment: Your agent's context is a junk drawer](https://www.augmentcode.com/blog/your-agents-context-is-a-junk-drawer) — convention over configuration, failure-backed rules only
- [LibreChat Agent Export/Import Request](https://github.com/danny-avila/LibreChat/discussions/10019) — structural limitation: agents live in MongoDB

**Post-Mortem:**
- [Worktree vs Staging Workflow Post-Mortem](https://github.com/veloxforce/claude-user-configs/commit/e09ec32) — David's 4-part analysis (root cause, workflow, CI/CD corrective, acceptance surface)

**Existing Design Docs:**
- [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services) (Step 7: Deploy to Staging)
- [Ship with Confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) (testing pyramid)
- [Docker Compose Backend Deployment Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/docker-compose-backend-deployment-design)

**Transcripts:**
- [2026-03-11 Grooming](https://app.fireflies.ai/view/01KKEHEX9ESP51H73NZRMKTRRG) — CI/CD idea origin, ops manual ownership transfer
- [2026-03-20 Grooming](https://drive.google.com/file/d/16oDF5LAbBiCrXi5tXgyma5iZ2PCgL3_a/view) — #1189 wrong-container discovery live

**Session:** 🗒️ Session 6fd2a15f-748a-4d9f-b551-2da0574da41b

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

