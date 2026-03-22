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
| **State drift** | Accumulates silently between deploys | Impossible — every deploy starts from recipe |
| **Audit trail** | SSH ops invisible to next session | Every push = commit = issue-commit-linker |

Two principles, equally important:
- **Reproducibility:** the system is derivable from git — deterministically, without manual steps
- **Traceability:** every state change goes through git, so the issue-commit-linker captures it. The next session reads the commit history and knows what happened. No SSH = no amnesia.

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
| **Archibus** | LibreChat, MongoDB, MeiliSearch, pgvector | FastMCP (fm-assistant, bulk-import) |
| **Simple backend** | Postgres (or nothing) | FastAPI service |

Simple projects don't need an infra file. The convention degrades gracefully — if no `docker-compose.infra.yml` exists, the app compose runs standalone.

**The compose file is the handoff point** between human decisions and automated execution. Everything upstream (JA design, Developer implementation) is design. Everything downstream (deploy script, GHA) is deterministic. The compose file carries all design decisions — the script just reads them.

**Volume cloning replaces seed scripts.** On first preview deploy, app volumes are cloned from staging. No seed directory, no seed.sh, no Makefile. The staging system IS the seed. For bootstrap (first deploy with no staging), app services create their own state on first run — OpenWebUI creates its DB, Typesense starts empty, ingestion runs via GHA.

**Undefined:** Volume state promotion — when a preview's volumes contain state the developer wants on staging (e.g., new agent config tested in preview), how does it transfer on PR merge? Options: export-to-git before merge, volume swap, or manual recreation. Requires a real case to evaluate.

### Part 4 — Push = Preview

The developer works locally in a worktree. Every push triggers a preview environment on the server — automatically, via GHA.

**Developer workflow:**
1. `/worktree` skill creates branch + draft PR (existing behavior)
2. Developer codes locally, runs unit tests (`uv run pytest`)
3. Push → GHA triggers `deploy-preview.sh`
4. Preview URL appears (e.g., `issue-1234.wilsch-deployment.com`)
5. Test via URL, share with team
6. More pushes → GHA redeploys (same volumes, new code)
7. PR merged → GHA runs `cleanup-preview.sh` + `deploy-staging.sh`

The worktree skill's existing push behavior IS the deployment trigger. No modifications to the skill. No GTR hooks. No local Docker.

**Deploy script (`scripts/deploy-preview.sh`, ~60 lines):**

```bash
BRANCH=$1
# 1. Parse volumes from app compose
VOLUMES=$(docker compose config --volumes)
# 2. Clone staging volumes (first deploy only)
for vol in $VOLUMES; do
  docker volume create "${BRANCH}_${vol}"
  docker run --rm -v "staging_${vol}:/from:ro" \
    -v "${BRANCH}_${vol}:/to" alpine cp -a /from/. /to/
done
# 3. Start with branch-scoped names
COMPOSE_PROJECT_NAME=$BRANCH docker compose up -d --wait
# 4. Caddy route
echo "${BRANCH}.wilsch-deployment.com { reverse_proxy localhost:${PORT} }" \
  > caddy/previews/${BRANCH}.conf
git add caddy/previews/ && git commit -m "caddy: preview ${BRANCH}"
caddy reload
```

**Cleanup script (`scripts/cleanup-preview.sh`, ~20 lines):**
Compose down, remove volumes, remove Caddy config, commit removal.

**GHA workflow (3 triggers):**

| Trigger | Action | Script |
|---------|--------|--------|
| Push to feature branch | Deploy preview | `deploy-preview.sh` |
| PR closed/merged | Tear down preview | `cleanup-preview.sh` |
| Push to staging branch | Update staging | `deploy-staging.sh` |

**Back pressure (script-level, not AI-level):**

| Level | Check | What it catches |
|-------|-------|----------------|
| 1 | `docker compose config` | Invalid compose file |
| 2 | `docker compose up --wait` | Health check failures |
| 3 | `curl -f $PREVIEW_URL` | Routing/reachability broken |
| 4 | `docker compose logs \| grep error` | Runtime errors in logs |

Each level returns an exit code. GHA reads it: 0 = success, non-zero = failure. Binary, deterministic, no AI interpretation.

**Deploy-linker:** GHA step that posts deployment status to the issue: `📦 Preview deployed: URL` on success, `❌ Preview failed` on failure. Extracts issue number from branch name. Automatic.

**Caddy configs live in git** — not `/etc/caddy/`. The deploy script generates a config file, commits it, syncs to server. Every route change is a git commit → issue-commit-linker captures it.

**Read-only SSH enforcement:** Three server users:
- `agent` — restricted, investigation only (logs, inspect, read files)
- `deploy` — full access, GHA only (key in GitHub Secrets)
- `marius`/`david` — full access, human emergency use

The AI's SSH config only sees the `agent` user. Deployment via SSH is structurally impossible — it can only happen through git push → GHA. Same "no decision point" principle: remove the wrong choice entirely.

**Data operations (ingestion):** Read-only SSH forces all writes through git. Data files go in the repo, GHA runs ingestion scripts on push. The commit that added data IS the audit trail. For UI-level state changes (agent configured via OpenWebUI), a volume manifest cron detects size changes and commits notifications to git.

**Undefined:** Data ingestion pipeline design — the trigger mechanism for "data changed in git → GHA runs ingestion on server" needs a concrete implementation pattern. Related to the dbt/Flyway model (declarative files + reconciliation loop).

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
- Route to compose: `→ See docker-compose.infra.yml for shared services`

**The filter (from Augment):** For each CLAUDE.md line, ask: "Would the agent make a mistake without this?" If no — delete it. If the answer is "yes, but the compose convention or GHA handles it now" — also delete it.

This aligns with #659's router principle: CLAUDE.md routes to source files where truth lives. The compose files (`docker-compose.yml`, `docker-compose.infra.yml`) ARE the source of deployment truth.

**Compose authorship and the JA standard section:** Every JA design doc for a Docker project includes a deployment topology section:
- **Infra/app split** — which services are shared vs isolated
- **Test rubric** — what "healthy" means (health endpoints, smoke test)
- **Project-specific scripts** — does this project need ingestion? Config import?

The JA designs the split. The Developer writes the compose files and project-specific scripts. The Dev Lead checks that the convention was followed. The compose files are code — maintained as part of the implementation, same as application code.

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

**Sessions:**
- 🗒️ Session 6fd2a15f (Pass 1 — design doc extraction)
- 🗒️ Session b3ede71b (Pass 2 — real project grounding + conversation evidence)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

