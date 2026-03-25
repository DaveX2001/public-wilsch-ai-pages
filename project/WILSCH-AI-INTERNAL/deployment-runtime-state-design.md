---
publish: true
---

# Deployment & Runtime State — Convention Over Configuration
[[docker-deployment]]

Design for eliminating deployment decisions through a two-file compose convention (infra/app), automated preview environments via GHA, and structural enforcement (read-only SSH). Git push is the only deployment interface — no SSH, no manual Docker commands. Absorbs CCI #646 (Deployment & Staging Workflow Not Embodied), #640 (CI/CD Auto-Deployment), and #659 (CLAUDE.md as Router).

---

## Problem Statement

A git clone followed by `docker compose up` does not produce a working system. Runtime state — Docker volumes, agent configurations, pipeline prompts, database seeds, cross-stack network wiring, container startup order — lives outside git. When this state is lost, manually recreated, or deployed to the wrong container, every downstream process breaks.

This creates two gaps. **Reproducibility:** the worktree represents code, not the system. Testing requires the full system, which only exists on staging. **Traceability:** operations performed via SSH leave zero git trail. The issue-commit-linker only fires on commits — SSH work is invisible to the next session. Git is the inter-session memory; SSH is amnesia.

Evidence from 5 operator sessions (CCI #635): edits made directly on the server via SSH, containers run live, git state managed after the fact. Two sessions operating on the same server simultaneously — one ran `docker compose down`, destroying containers the other session depended on. The root cause: organic multi-stack architecture with manual cross-stack wiring (`docker network connect`) that no compose file declares.

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
| **Goal** | Git is the single source of truth for both code AND runtime state. The running system is derived from it — deterministically, without manual steps. Every state change leaves a git trail. |
| **Success** | A developer creates a worktree, pushes, and receives a preview URL with a running system — volumes cloned from staging, health checked, reported to the issue. No SSH. No manual Docker commands. Every deployment is traceable via issue-commit-linker and deploy-linker. |
| **Done test** | Developer pushes to worktree branch → GHA deploys preview → health check passes → deploy-linker posts URL to issue. No SSH, no manual compose, no seed command. The push IS the deployment. |

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

Audit of real compose files reveals Layer 3 is more than volumes:

| What's missing | Archibus evidence | IITR evidence |
|---------------|-------------------|---------------|
| **Volume contents** | `staging_mongodb_data` (agent configs), `staging_meili_data`, `staging_pgdata2` | `openwebui_data`, `typesense-data`, `qdrant_storage`, `ollama_data` |
| **Cross-stack networks** | `librechat-shared` — manual `docker network connect` between 3 compose files | `iitr-shared-network` — connects navigation, masterfragen, infrastructure |
| **Startup order** | — | Pipeline filter scores 0/29 if Typesense isn't ready at startup |
| **Port assignments** | 3081 (staging) vs 3020 (spike) — hardcoded, can't run two stacks | 3006 (OpenWebUI), 8109 (Typesense), 11434 (Ollama) |
| **Container names** | `LibreChat-staging` hardcoded | Mixed: some hardcoded, some `${COMPOSE_PROJECT_NAME}` |
| **GPU allocation** | — | TEI + Ollama share one GPU — can't duplicate |

The root cause is organic multi-stack architecture. Archibus has 3 separate compose files manually wired via external networks. When one session ran `docker compose down`, it destroyed containers another session depended on — because the cross-stack wiring (`docker network connect`) was invisible state.

Every deployment failure in the CCI #646 evidence chain traces back to Layer 3. The fix targets the full runtime topology, not just volumes.

### Part 2 — Git = Truth + Memory

The direction of truth flips — and git becomes the inter-session memory:

| | Today | Convention |
|---|-------|-----------|
| **Truth lives in** | Running system (staging server) | Git repository |
| **Git contains** | Partial copy (code + compose) | Complete system definition (code + compose + Caddy + state manifests) |
| **Deploy means** | Manual reconstruction of runtime state | Automated derivation from git |
| **State drift** | Accumulates silently between deploys | Impossible — every deploy starts from volume clone + compose |
| **Audit trail** | SSH ops invisible to next session | Every push = commit = issue-commit-linker |

Two principles, equally important:
- **Reproducibility:** the system is derivable from git — deterministically, without manual steps
- **Traceability:** every state change goes through git, so the issue-commit-linker captures it. The next session reads the commit history and knows what happened. No SSH = no amnesia.

**How runtime state travels (volume cloning replaces codification):**

| State type | Mechanism | In git? |
|-----------|-----------|---------|
| DB data (MongoDB, Postgres, Qdrant) | Volume clone from staging | No — volumes are the truth |
| Pipeline filters / prompts | Bind mount from git | Yes — file changes are commits |
| Agent definitions (OpenWebUI, LibreChat) | Volume clone (stored in DB) | No — detected at witness ceremony ([CCI #623](https://github.com/DaveX2001/claude-code-improvements/issues/623)) |
| Caddy routing | `.conf` files in `caddy/` dir | Yes — committed by deploy script |
| Docker networking | Infra/app compose convention | Yes — compose files in git |
| Search indexes (Typesense, Meili) | Volume clone + ingestion via GHA | Partial — ingestion scripts in git |

**What cannot be codified (irreducible):**
- **Credentials** — `.env` files, gitignored. Injected from secrets management or manual setup.
- **GPU hardware** — Ollama on IBM Power or dedicated GPU servers. Physical constraint.
- **External service state** — IMAP inboxes, third-party API state. Real-world dependency.

The convention requires: everything above the irreducible line MUST be in git. Everything below is accepted as an external dependency with a documented injection point.

### Part 3 — The Infra/App Convention

The organic multi-stack architecture is the root cause (Part 1). The fix: split every project into two compose files that declare the boundary between shared infrastructure and isolated application services.

**`docker-compose.infra.yml`** — shared services. Runs once on the server, never duplicated per branch. Contains GPU services, databases that are shared, model weights. Creates the `{project}-infra` network.

**`docker-compose.yml`** — application services. Isolated per branch via `COMPOSE_PROJECT_NAME`. Contains the code being developed and tested. Connects to the infra network as external.

```
Server
├── infra (always running, one instance)
│   ├── Ollama (GPU)
│   ├── TEI (GPU)
│   └── network: iitr-infra
│
├── staging (app compose, main branch)
│   ├── OpenWebUI → connects to iitr-infra
│   ├── Pipelines → connects to iitr-infra
│   └── volumes: staging_openwebui_data, staging_typesense-data
│
└── preview-issue-1234 (app compose, worktree branch)
    ├── OpenWebUI → connects to iitr-infra (SAME Ollama)
    ├── Pipelines → connects to iitr-infra (SAME TEI)
    └── volumes: issue-1234_openwebui_data (cloned from staging)
```

**Why this works across projects:**

| Project | Infra (shared) | App (isolated per branch) |
|---------|---------------|--------------------------|
| **IITR** | Ollama, TEI, Langfuse | OpenWebUI, Pipelines, Typesense |
| **Archibus bulk-import** | — (none) | LibreChat, MongoDB, FastMCP |
| **Simple backend** | Postgres (or nothing) | FastAPI service |

Archibus bulk-import is fully self-contained — no shared infrastructure with fm-assistant. The only shared element (BEM auth logic) is code-level, not runtime. Blast radius isolation: touching bulk-import never breaks fm-assistant. This means no `docker-compose.infra.yml` — the convention degrades gracefully.

Simple projects don't need an infra file. The convention degrades gracefully — if no `docker-compose.infra.yml` exists, the app compose runs standalone.

**The compose file is the handoff point** between human decisions and automated execution. Everything upstream (JA design, Developer implementation) is design. Everything downstream (deploy script, GHA) is deterministic. The compose file carries all design decisions — the script just reads them.

**COMPOSE_PROJECT_NAME is the naming convention.** Docker Compose auto-prefixes containers, networks, and volumes with `COMPOSE_PROJECT_NAME`. The convention: never override this behavior.

| Anti-pattern | Why banned | Convention |
|-------------|-----------|-----------|
| `container_name:` in compose | Overrides auto-naming, causes collisions | Let Docker name containers: `{project}-{service}-{N}` |
| `external: true` on networks | Creates invisible cross-stack wiring | Let compose manage networks: `{project}_default` |
| `network_mode: host` | Bypasses network isolation | Use compose-managed networks |
| Hardcoded volume names | Not scoped to project | Use relative names: `{project}_{volume}` |

The compose file + one env var (`COMPOSE_PROJECT_NAME`) = complete project identity. No manual naming, no external networks, no `docker network connect`.

**Volume cloning replaces seed scripts.** On first preview deploy, app volumes are cloned from staging. No seed directory, no seed.sh, no Makefile. The staging system IS the seed. For bootstrap (first deploy with no staging), app services create their own state on first run — OpenWebUI creates its DB, Typesense starts empty, ingestion runs via GHA.

**Validation scope differs by compose type.** The app compose must be isolation-safe — it runs N copies simultaneously. The infra compose must be stability-safe — it runs once, referenced by name. The Level 0 convention validator (Part 4) applies different rulesets:

| Compose | Validated for | Why |
|---------|--------------|-----|
| **App** (`docker-compose.yml`) | Isolation — no `container_name:`, no `external: true`, env-var ports, `deploy.entry` label present | Must coexist as multiple branch-scoped copies |
| **Infra** (`docker-compose.infra.yml`) | Stability — healthchecks on every service, credentials in `.env` (not inline), creates the network app composes expect | Singleton — stable names and networks are correct here |

`container_name:` is banned in app compose (breaks `COMPOSE_PROJECT_NAME` isolation) but expected in infra compose (GPU services referenced by stable name). The same directive is a violation in one context and correct in the other.

**Volume state promotion is one-directional by design.** Staging→preview via volume clone (proven: #1238 AC1 reproduced all MongoDB data exactly — 29 collections, 1 agent, 7 categories, 1 user identical to staging). Preview→staging does NOT transfer automatically. Industry research confirms: no platform (Vercel, Railway, Render, Fly.io, Coolify, Neon, PlanetScale, Supabase) supports bidirectional volume state promotion. Data never travels back.

When a developer creates state in a preview (e.g., configures an agent via LibreChat UI), that state lives in the preview's volumes. On PR merge, code travels via git — volume state does not. Detection and reconciliation happen at witness ceremony time:

| Concern | Mechanism | Owner |
|---------|-----------|-------|
| **Detection** | Witness Check 7 — diff volume metadata between preview and staging at review time | Dev Lead ([CCI #623](https://github.com/DaveX2001/claude-code-improvements/issues/623)) |
| **Reconciliation** | Agent-browser guided manual recreation on staging (e.g., recreate agent config in LibreChat UI) | Developer, guided by Dev Lead |
| **Audit** | ACs encode expected volume changes ("agent available in selector" = DB config change) | JA design doc |

Pipeline prompts and skill files are bind-mounted from git — no promotion needed. Search index ingestion is project-specific (see Part 4).

### Part 4 — Push = Preview

The developer works locally in a worktree. Every push triggers a preview environment on the server — automatically, via GHA.

**Developer workflow:**
1. `/worktree` skill creates branch + draft PR (existing behavior)
2. Developer codes locally
3. Push → GHA triggers `deploy-preview.sh`
4. Preview URL appears (e.g., `issue-1234.wilsch-deployment.com`)
5. Test via URL, share with team
6. More pushes → GHA redeploys (same volumes, new code)
7. PR merged → GHA runs `cleanup-preview.sh` + `deploy-staging.sh`

The worktree skill's existing push behavior IS the deployment trigger. No GTR hooks. No local Docker. The skill's Step 5 (local Docker volume handling) is removed — volume cloning is now server-side. In its place, the skill detects compose files and monitors the deploy script's back-pressure output, reporting deployment status in-session.

**Deploy script (`scripts/deploy-preview.sh`):**

```
INPUT: branch name

1. READ volume names from docker-compose.yml
2. FOR EACH volume: CLONE staging volume → branch-scoped copy
3. START app compose with COMPOSE_PROJECT_NAME = branch
4. WAIT for health checks to pass
5. GENERATE Caddy config for {branch}.wilsch-deployment.com
6. COMMIT Caddy config to git
7. RELOAD Caddy
```

**Cleanup script (`scripts/cleanup-preview.sh`):**

```
INPUT: branch name

1. STOP app compose for this branch
2. REMOVE branch-scoped volumes
3. REMOVE Caddy config
4. COMMIT removal to git
5. RELOAD Caddy
```

**GHA workflow (3 triggers):**

| Trigger | Action | Script |
|---------|--------|--------|
| Push to feature branch | Deploy preview (app only) | `deploy-preview.sh` |
| PR closed/merged | Tear down preview | `cleanup-preview.sh` |
| Push to staging branch | Update staging (two-phase: infra then app) | `deploy-preview.sh staging` |

**Infra deployment cadence.** Infra and app deploy at different cadences. Feature branch pushes deploy app compose only — the preview connects to already-running infra via the shared network. Staging merges deploy both: infra compose first (pull new images, restart changed services), then app compose. Infra changes on feature branches are visible in the PR but don't deploy until merge — the preview runs against current infra.

This means adding a new infra service (e.g., Typesense) is sequential: merge the infra change first (establishes the service on staging), then branch off with app code that uses it. Breaking infra changes are rare and inherently risky — the PR review is the gate, not preview testing.

**Entry point detection via compose label.** The deploy script must know which service gets the Caddy route. Industry convention (Traefik, caddy-docker-proxy, Coolify) uses Docker labels. The deployment convention uses a single label:

```yaml
services:
  librechat:
    labels:
      deploy.entry: "3080"          # container port — script reads this
    ports:
      - "${LIBRECHAT_PORT:-0}:3080" # default 0 = auto-assign for previews
```

The script reads `deploy.entry` from compose config, finds the labeled service, queries its auto-assigned host port via `docker compose port`, and generates the Caddy config. No hardcoded service names or ports in the script — it works for any project.

**Port auto-assignment convention.** Compose files default published ports to 0 (`${PORT_VAR:-0}:container_port`). Docker auto-assigns an available host port, preventing collisions between preview and staging. Local development uses `.env` to override with a stable port (e.g., `LIBRECHAT_PORT=3020`). The server has no port override — 0 kicks in, Docker picks a free port. The Level 0 validator checks: exactly one service has `deploy.entry`. Zero = fail (no entry point). More than one = fail (ambiguous).

**Back pressure (script-level, not AI-level):**

| Level | Check | What it catches |
|-------|-------|----------------|
| 0 | Compose lint (convention validator) | `container_name:`, `external: true`, missing `healthcheck:`, missing `deploy.entry` |
| 0s | Server pre-flight | Remote is HTTPS (not SSH), deploy user doesn't own checkout, git can't fetch origin, `.env` missing, Docker daemon inaccessible |
| 1 | `docker compose config` | Invalid compose file |
| 2 | `docker compose up --wait` | Health check failures |
| 3 | `curl -f $PREVIEW_URL` | Routing/reachability broken |
| 4 | `docker compose logs \| grep error` | Runtime errors in logs |

Each level returns an exit code. GHA reads it: 0 = success, non-zero = failure. Binary, deterministic, no AI interpretation. Level 0 is static — runs in CI on every PR without needing the server. Level 0s runs on the server before any deployment — it catches infrastructure prerequisites that would otherwise produce confusing errors ("Repository not found" when deploy keys are disabled, permission denied when ownership is wrong). Level 0s turns the 4 stacked infrastructure blockers discovered during Archibus adoption (#1251) into automated checks with clear error messages and remediation instructions.

**Deploy-owned checkout model.** The deploy user clones all project repos to `/home/deploy/projects/{repo-name}` via SSH. Directory permissions: `deploy:dev-team 2775` (setgid ensures new files inherit `dev-team` group). This eliminates three infrastructure blockers that stacked during Archibus adoption:

| Blocker | Root cause | How deploy-owned eliminates it |
|---------|-----------|-------------------------------|
| `git safe.directory` | Repo owned by marius, deploy can't pull | Deploy owns the checkout — ownership matches |
| SSH vs HTTPS remote | Marius cloned via HTTPS, deploy has no HTTPS credentials | Deploy clones via SSH — remote matches auth method |
| File group ownership | Files owned by `marius:marius`, deploy can't write | Deploy creates files as `deploy:dev-team` — setgid propagates |

Human operators (marius, david) access deploy-owned directories via `dev-team` group membership — same read/write/Docker capabilities as today, different ownership direction. Emergency "break glass" access works via group permissions without sudo.

**Update mode (subsequent pushes).** The first push to a branch clones staging volumes and creates a new preview. Subsequent pushes to the same branch skip volume cloning — they pull new code and restart services. Volumes persist across updates: agent configurations, test data, and database state survive code deployments. The deploy script detects whether the preview already exists (`docker compose ps`) and routes to the appropriate path: first deploy (clone + start) or update (pull + restart).

**Staging deploy path.** The deploy script detects the `staging` branch and takes a different path: no volume cloning (staging IS the volume source), no Caddy config generation (staging has a permanent route). Staging deploy is: `git pull → docker compose up -d → health check`. For two-phase staging (see infra deployment cadence above), the GHA workflow runs `docker compose -f docker-compose.infra.yml up -d` first, then the app compose.

**Workflow dedup.** A single `deploy.yml` handles all three triggers (preview, cleanup, staging). The spike-era `deploy-preview.yml` (single-purpose) must be deleted — both workflows match `issue-*` branches, causing duplicate GHA runs and confusing issue comments (one success, one failure on the same push).

**Deploy-linker:** GHA step that posts deployment status to the issue: `📦 Preview deployed: URL` on success, `❌ Preview failed` on failure. Extracts issue number from branch name. Automatic.

**Caddy configs are ephemeral on the server.** The deploy script generates a config file in `/etc/caddy/conf.d/`, the cleanup script removes it. Preview configs are temporary by definition — they exist while the preview runs and disappear on PR merge. The deploy-linker posts the preview URL to the issue (audit trail). Committing preview configs to git was evaluated and rejected: each preview's config would only be visible on its own branch, creating no central overview of active routes. Periodic filesystem sweep (`ls /etc/caddy/conf.d/preview-*`) catches orphaned configs from abandoned branches or failed cleanups.

**Caddy coexistence with legacy configs.** The server has 20+ manually-created `.conf` files in `/etc/caddy/conf.d/` serving production and staging routes. The deploy script coexists with these — it only manages configs it creates, identified by naming convention:

| Config type | Naming pattern | Managed by |
|------------|---------------|------------|
| Preview environments | `preview-{repo}-issue-{N}.conf` | Deploy/cleanup scripts |
| Staging/production | Project-specific names (e.g., `bulk-import.conf`) | Manual (legacy) or future migration |

Preview naming cannot collide with production naming — one is branch-scoped (`issue-N`), the other is project-scoped. Cleanup script matches `preview-*-issue-{N}*` to remove only script-managed configs. Legacy configs are untouched indefinitely. Migration happens naturally as projects adopt the convention.

**Proof point (#1238):** Manual deploy/cleanup cycle validated on WILSCH-AI-SERVER against Archibus bulk-import (AC1-AC4 passed). Volume cloning produces a faithful staging replica. Port isolation via Docker port 0 prevents binding conflicts. Preview and staging coexist without interference — staging data, services, and routing remain unchanged throughout the full lifecycle. Cleanup leaves zero residual resources. Evidence: [#1238 verification](https://github.com/DaveX2001/deliverable-tracking/issues/1238).

**Read-only SSH enforcement — 6-layer structural design:**

Three server users, each structurally limited:

| User | Docker | Filesystem | Git | SSH access |
|------|--------|------------|-----|------------|
| `agent` | Read-only via socket proxy | Read code, no `.env`, no write | Read-only (safe.directory) | AI sessions |
| `deploy` | Full (docker group) | Full write | Full | GHA runner only |
| `marius`/`david` | Full (docker group) | Full write | Full | Personal keys, emergency |

**Agent user restrictions (structural, not convention):**

- **Docker:** [LinuxServer docker-socket-proxy](https://github.com/linuxserver/docker-socket-proxy) runs on `127.0.0.1:2375`. Agent's `DOCKER_HOST=tcp://localhost:2375`. Proxy configured: `POST=0` (master read-only switch), `CONTAINERS=1`, `IMAGES=1`, `NETWORKS=1`, `VOLUMES=1`. Agent can `docker ps`, `docker logs`, `docker inspect` — cannot `docker stop`, `docker rm`, `docker exec` (403 Forbidden).
- **Filesystem:** Agent not in `dev-team` group. Project dirs are `drwxrwsr-x root:dev-team` — agent can read code (world-readable) but cannot write. `.env` files tightened to `640 :dev-team` — agent cannot read credentials.
- **Git:** Git's `safe.directory` check blocks non-owner operations. Repo owned by `marius:dev-team`, agent is neither — git refuses all operations including `git pull`. Read-only file access still permits `git log` and `git show` via direct object reads.

**Undefined:** `safe.directory` may be redundant — filesystem permissions (agent not in `dev-team`) already prevent writes, and SSH restrictions prevent push. Removing `safe.directory` would enable native `git log`/`git show` without direct object reads. Needs a spike on WILSCH-AI-SERVER to verify that filesystem perms + SSH restrictions alone produce the desired read-only git behavior.
- **SSH config:** AI's `~/.ssh/config` points `WILSCH-AI-SERVER` to `User agent`. Onboarding bootstrap surfaces this host — the AI sees only the restricted user.

The AI's SSH config only sees the `agent` user. Deployment via SSH is structurally impossible — it can only happen through git push → GHA. Same "no decision point" principle: remove the wrong choice entirely.

**Data operations (ingestion):** Read-only SSH forces all writes through git. Data files go in the repo, GHA runs ingestion scripts on push. The commit that added data IS the audit trail.

**Testing model — two-stage:**

| Stage | Where | What it answers |
|-------|-------|----------------|
| **Preview** | Isolated clone of staging | "Does my code work?" (AC verification) |
| **Staging** | Shared, latest state | "Does it work in context?" (automatic via merge queue) |

Before final testing on the preview, the developer rebases code AND re-clones volumes from staging — minimizing divergence. PRs merge sequentially; GHA deploys staging + health check after each merge. If the health check fails, the last merge is the culprit. No race condition — each merge is the only variable.

**Data ingestion is project-specific.** The core convention handles deployment (compose up, volume clone, Caddy routing). Data ingestion — populating search indexes, running ETL pipelines, importing seed data — varies per project. Archibus bulk-import requires none. IITR (Typesense, Qdrant) will design its own ingestion pipeline when it adopts the convention. The convention provides the hook point (GHA workflow + compose), individual projects extend it with project-specific scripts as needed.

### Part 5 — CLAUDE.md Implications: What Dies, What Stays

The Vercel AGENTS.md research and Augment's "junk drawer" analysis converge on one finding: agent context files should contain only what prevents specific failures the agent would otherwise make. Everything else is noise that degrades performance.

Applied to deployment, combined with CCI #659 (CLAUDE.md as Router):

**What dies (GHA + compose convention handles it):**
- Deployment identity (container name, port, server path) — the compose file IS the identity
- Branch→server mapping — GHA routes branches to environments
- `make staging` instructions — GHA triggers on push
- Caddy subdomain convention — deploy script manages routing, configs in git
- SSH access details — read-only for investigation only

**What stays (prevents failures the agent would make):**
- Build/test/run commands (`uv sync`, `uv run pytest`)
- Known gotchas that aren't in code (e.g., "temperature 0 for RAG with small models")
- Architecture decisions not obvious from reading code
- Credential locations (which `.env` vars are needed, where to get them)
- Route to compose: `→ See docker-compose.infra.yml for shared services`

**The filter (from Augment):** For each CLAUDE.md line, ask: "Would the agent make a mistake without this?" If no — delete it. If the answer is "yes, but the compose convention or GHA handles it now" — also delete it.

This aligns with #659's router principle: CLAUDE.md routes to source files where truth lives. The compose files (`docker-compose.yml`, `docker-compose.infra.yml`) ARE the source of deployment truth.

**Compose authorship and the JA standard section:** Every JA design doc for a Docker project includes a deployment topology section:
- **Infra/app split** — which services are shared vs isolated
- **Test rubric** — what "healthy" means (health endpoints, smoke test)
- **Project-specific scripts** — does this project need ingestion? Config import?

The JA designs the split. The Developer writes the compose files and project-specific scripts. The Dev Lead checks that the convention was followed. The compose files are code — maintained as part of the implementation, same as application code.

### Part 6 — Branch Protection: Org-Level Rulesets

Parts 1–5 enforce the convention on the server. This part enforces it at the git layer — preventing code from reaching the server through unauthorized paths.

No branch protection exists on any repository today. GitHub Free does not support branch protection on private repos. The fix: upgrade the existing `WILSCH-AI-SERVICES` organization to Team plan ($4/user/month) and create a single org-level ruleset.

**Org-level ruleset (set once, covers all repos):**

| Setting | Value |
|---------|-------|
| Target | All repositories (current + future) |
| Branches | `staging`, `main` |
| Require PR before merge | Yes — forces worktree→PR→merge flow |
| Block force push | Yes — prevents history destruction |
| Require status checks | Deferred — added when GHA CI pipelines exist |
| Require reviews | Optional — solo developer context |

The ruleset auto-applies to every repo transferred to the org, including repos created in the future. No per-repo configuration, no automation scripts, no manual setup.

**Migration path:**
1. Upgrade `WILSCH-AI-SERVICES` to Team plan
2. Transfer client repos from `MariusWilsch` personal account
3. Create org-level ruleset (one API call or Settings UI)
4. Every repo inherits protection immediately

This closes the last gap in the enforcement chain. Without branch protection, the AI can `git push` directly to staging from the local machine — bypassing the worktree→PR→GHA flow that Parts 1–5 depend on. With the ruleset, direct push returns 403. The only path to staging is: worktree branch → PR → merge → GHA deploys.

**AI git identity: GitHub App.** The Undefined is resolved — GitHub App replaces both deploy keys and the machine user proposal. One App installed on the WILSCH-AI-SERVICES org provides: (a) org-wide repo access without per-repo deploy key setup, (b) distinct identity for audit trail — commits via the App are attributable to automation, not a human, (c) 1-hour auto-expiring tokens (no permanent credentials on the server), (d) no additional Team seat cost ($4/mo saved vs machine user). The App's installation token is generated via JWT helper script on the server, used for `git pull` during GHA deploys. The deploy-linker already runs as a GitHub App (the issue-commit-linker) — deploy access follows the same pattern. Server-side mapping: the `deploy` SSH user uses the App's credentials for git operations; the `agent` SSH user remains read-only (no git write access regardless of identity).

### Part 7 — Scaling the Convention

Parts 1–6 validated the mechanism against one project (Archibus bulk-import). Scaling to all projects requires resolving distribution, validation, and adoption. The Archibus exemplar proved the mechanism for the simple case (self-contained stack, no shared infra). Pass 5 absorbed implementation learnings to prepare for multi-project adoption.

- **Distribution: Custom GitHub Action.** Convention tooling lives in `WILSCH-AI-SERVICES/deploy-action` as a custom GitHub Action. Projects write their own workflows (own triggers, project-specific steps like data ingestion) and reference the action: `uses: WILSCH-AI-SERVICES/deploy-action@v1`. The action receives inputs (`action: deploy-preview|cleanup|deploy-staging`, `compose-file`, `branch`). Version pinning via tags — projects upgrade at their own pace. This was chosen over reusable workflows because projects need different triggers and project-specific steps that a shared workflow can't accommodate.
- **Convention linter:** `docker compose config` validates syntax, not convention compliance. A CI linter on compose changes would structurally enforce: healthcheck blocks present, env-var ports, no external networks, self-contained stacks. Same principle as read-only SSH — remove the wrong choice.
- **Adoption path:** How a project opts into push=preview. Convention-compliant compose → add GHA reference → first deploy establishes staging. The checklist needs definition.
- **CI gate:** Whether PRs modifying `docker-compose.yml` should be validated against the convention before merge. Related to the convention linter — the linter is the tool, the CI gate is where it runs.

**Undefined:** Monorepo deploy strategy — single root compose (deploy everything on every push) vs per-subproject compose (path-filtered GHA jobs). Docker Compose selectively restarts only changed services, minimizing the risk of "deploy everything." However, volume cloning cost on first preview deploy scales with the number of volumes across all subprojects. Needs a spike with actual IITR compose to measure deploy time and disk usage before deciding. The convention default is one compose file per deploy unit — a monorepo either consolidates into one compose or treats each subproject as an independent deploy unit with path-filtered triggers.

### Spike Format

Undefined markers in this design doc become spike issues when ready for validation. The format (established across #1220, #1222, #1229, #1250):

**What / Why.** Frame the spike's purpose and the risk it mitigates. A spike exists because an assumption blocks decomposition — the What names the assumption, the Why explains what breaks if it's wrong.

**Spike Primitives.** Numbered table of independently testable claims. Each primitive isolates one variable — if the spike fails, the primitive table tells you which layer failed. Order primitives so each builds on the previous (P1 must pass before P2 is meaningful).

**Proof Point.** The binary comparison or question the spike answers. Usually "does X produce the same result as Y?" or "does X work at all?" The proof point is what makes the spike pass or fail — without it, the spike is open-ended research, not validation.

**Proven-vs-Assumed Map.** Output template with Status and Evidence columns, filled in during execution. Before the spike, claims are "Assumed" (empty evidence). After, each is "Proven" or "Failed" with a specific artifact. This is the spike's deliverable — the map travels back to the design doc or decomposition.

**After Spike.** Routing logic for passes and fails. Passes unlock the next decomposition trunk. Fails identify which primitive broke and what the fallback is. Both outcomes are valuable — a failed spike prevents wasted implementation effort.

```
## What?
{Assumption that blocks decomposition}

## Why?
{What breaks if the assumption is wrong}

## Spike Primitives

| # | Primitive | What's tested |
|---|-----------|--------------|
| 1 | {layer 1} | {isolated variable} |
| 2 | {layer 2} | {builds on P1} |

## Proof Point
{Binary question — pass/fail}

## Proven-vs-Assumed Map

| Claim | Status | Evidence |
|-------|--------|----------|
| {claim 1} | Assumed | |
| {claim 2} | Assumed | |

## After Spike
**Passes:** {next trunk / decomposition}
**Fails:** {which primitive, fallback path}
```

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

**Pass 2 Evidence (operator conversations):**
- [David: Archibus #1189 — wrong container deploy](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/b9f80acd-3f1a-470b-a2d1-1b35dfc725a3.jsonl)
- [David: Archibus multi-session — docker compose down disaster](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/289660df-41cd-4e98-9381-f49c248d5ea0.jsonl)
- [David: IITR #1191 — mono-repo migration + test harness](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/d301ee6f-d848-46a6-a57b-5f5db116cac9.jsonl)
- [David: IITR — wrong staging path investigation](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-03-IITR--deliverable/a3ca882c-690b-446b-a594-0309aa836e75.jsonl)

**Pass 2 Research:**
- [Back pressure for agents](https://banay.me/dont-waste-your-backpressure/) — automated feedback enables delegation down the org chart
- [Organization Chart — Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services) — VP/Delivery path: JA designs → Developer builds → Dev Lead checks
- [CodeRabbit git-worktree-runner](https://github.com/coderabbitai/git-worktree-runner) — evaluated, not needed (push IS the trigger)

**Pass 3 Evidence (Archibus exemplar):**
- [#646 comment — side-by-side compose comparison](https://github.com/DaveX2001/claude-code-improvements/issues/646#issuecomment-4109938814) — bulk-import (clean) vs FM-Assistant (organic)
- [#1237 — Compose Refactor](https://github.com/DaveX2001/deliverable-tracking/issues/1237) (merged, Archibus first exemplar)

**Pass 4 Evidence (proof point + verification):**
- [#1238 — Deploy script manual preview](https://github.com/DaveX2001/deliverable-tracking/issues/1238) (AC1-AC4 passed, Trunk 1 complete)
- [#1237 — Compose refactor](https://github.com/DaveX2001/deliverable-tracking/issues/1237) (closed, first convention exemplar)
- [#623 — Witness Skill v1](https://github.com/DaveX2001/claude-code-improvements/issues/623) (Check 7 volume state diff, merge as witness outcome)

**Pass 5 Evidence (Archibus retrospective):**
- [#1251 — GHA deploy workflow](https://github.com/DaveX2001/deliverable-tracking/issues/1251) (closed, all 7 ACs, first project wired to push=preview)
- [#1250 — GHA primitives spike](https://github.com/DaveX2001/deliverable-tracking/issues/1250) (closed, all 4 primitives proven)
- [#646 comment — 4 stacked infrastructure blockers](https://github.com/DaveX2001/claude-code-improvements/issues/646) (safe.directory, deploy keys, SSH/HTTPS, setgid)
- [#646 comment — deploy workflow collision observation](https://github.com/DaveX2001/claude-code-improvements/issues/646) (2026-03-25, two workflows on same push)

**Pass 5 Research:**
- [Traefik Docker routing](https://doc.traefik.io/traefik/reference/routing-configuration/other-providers/docker/) — label-based service discovery
- [caddy-docker-proxy](https://github.com/lucaslorentz/caddy-docker-proxy) — Caddy native Docker label integration
- [Coolify Docker Compose docs](https://coolify.io/docs/knowledge-base/docker/compose) — UI domain assignment + Traefik labels
- [Kamal proxy configuration](https://kamal-deploy.org/docs/configuration/proxy/) — deploy.yml proxy block

**Pass 3 Research:**
- [LinuxServer docker-socket-proxy](https://github.com/linuxserver/docker-socket-proxy) — read-only Docker API proxy, actively maintained (74 releases)
- [Tecnativa docker-socket-proxy](https://github.com/Tecnativa/docker-socket-proxy) — reference baseline, stale
- [OPA Docker AuthZ plugin](https://github.com/open-policy-agent/opa-docker-authz) — evaluated, overkill for this use case
- [GitHub org-level rulesets](https://docs.github.com/en/organizations/managing-organization-settings/creating-rulesets-for-repositories-in-your-organization) — Team plan, all-repo coverage

**Sessions:**
- 🗒️ Session 6fd2a15f (Pass 1 — design doc extraction)
- 🗒️ Session b3ede71b (Pass 2 — real project grounding + conversation evidence)
- 🗒️ Session aa9eb012 (Pass 3 — Archibus exemplar validation + CI/CD testing model)
- 🗒️ Session e8fef0b2 (Pass 3 — Archibus server cleanup + SSH enforcement design + branch protection)
- 🗒️ Session 20e0c452 (Pass 4 — resolve Undefined markers + proof point absorption)
- 🗒️ Session ce6cc685 (Pass 5 — Archibus retrospective, IITR generalization, 12 resolutions)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

