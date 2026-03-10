---
publish: true
---

# Docker Compose Backend Deployment
[[docker-compose-backend-deployment-design]]

Deployment pattern for Docker Compose backends: multi-stage Dockerfile, staging/production compose split, Makefile as the single deployment interface, and SSH-based git pull deploys.

---

## Problem Statement

Each new Docker Compose backend project reinvents its deployment setup by copying files from another repo and adapting them. The Makefile ADR defines target naming conventions but not operational steps — how to structure the Dockerfile, how to split compose files for staging vs production, how to wire SSH deploys, or how to verify a deployment succeeded.

This pattern extracts what's proven across multiple production deployments into a single reference. It covers the deployment infrastructure (files, commands, conventions), not the application code running inside the container.

**Preconditions:**
- Multiple Docker Compose backends run in production (different servers, different topologies)
- The Makefile ADR exists and defines standard target names
- Staging and production environments use separate compose files
- Deployments happen via SSH from a developer's machine (no CI/CD runner)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A developer starting a new Docker Compose backend can set up the full deployment infrastructure — Dockerfile, compose files, Makefile, env separation — by following this pattern. |
| **Success** | The pattern explains how we deploy (concrete files and commands), why we do it this way (decision rationale), and that it works (production references). No need to read other project repos. |
| **Done test** | Can a developer follow this document to deploy a new backend to both staging and production, and verify the deploy succeeded? If yes → pattern is complete. |

---

## Approach

### Part 1: Dockerfile Conventions

The standard Dockerfile uses a multi-stage build to separate dependency installation from the runtime image. This keeps the production image minimal — no build tools, no dev dependencies, no package manager.

**Stage 1 (Builder):**
- Base: `python:3.11-slim`
- Install `uv` from the official container image
- Copy `pyproject.toml` + `uv.lock` first (layer caching for deps)
- Run `uv sync --locked --no-dev --no-editable`
- Copy application code last

**Stage 2 (Runtime):**
- Base: `python:3.11-slim` (same version, clean image)
- Install `curl` for health checks (the only extra package)
- Copy `.venv` from builder (not the whole builder filesystem)
- Copy application code from builder
- Set `PATH="/app/.venv/bin:$PATH"`
- `HEALTHCHECK` directive with `curl -f http://localhost:{PORT}/health`
- `CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "{PORT}"]`

**Why multi-stage:** The builder stage includes git, uv, and build tools. The runtime stage includes only the virtual environment and curl. This reduces image size and attack surface.

**Why uv:** 10-100x faster than pip for dependency resolution. `--locked` ensures reproducible builds from `uv.lock`. `--no-editable` prevents development-mode installs in production.

**Reference:** [ROHDEX Dockerfile](https://github.com/MariusWilsch/rohdex/blob/staging/Dockerfile)

### Part 2: Compose File Structure

Every project maintains two separate compose files — one per environment. Not one file with profiles or environment variables toggling behavior. Separate files make it impossible to accidentally deploy staging config to production.

**Files:**
- `docker-compose.prod.yml` — production environment
- `docker-compose.staging.yml` — staging environment

**Standard service structure:**

```yaml
services:
  backend:
    image: {project}-backend:latest
    container_name: {project}-{env}        # e.g., rohdex-prod, rohdex-staging
    ports:
      - "{external_port}:{internal_port}"
    env_file:
      - .env
    environment:
      - ENVIRONMENT={staging|production}
      # Project-specific env overrides here
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:{internal_port}/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 10s
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

**What differs between files:** Container name, external port, environment value, and any project-specific config (e.g., which external service provider to use per environment). The service structure, healthcheck, and logging config stay identical.

**Port convention on shared servers:** When multiple projects run on the same server, assign ports by project with increments per environment:
- Base port for production (e.g., 5000)
- Base + 1 for staging (e.g., 5001)
- Base + 2 for dev (e.g., 5002)

Check `docker ps` on the target server before assigning to avoid conflicts.

**Why separate files:** A single compose file with environment switching (via `.env` or profiles) creates a class of bugs where the wrong environment config reaches production. Separate files eliminate this risk — the Makefile target selects the file.

**Reference:** [ROHDEX docker-compose.prod.yml](https://github.com/MariusWilsch/rohdex/blob/staging/docker-compose.prod.yml), [ROHDEX docker-compose.staging.yml](https://github.com/MariusWilsch/rohdex/blob/staging/docker-compose.staging.yml)

### Part 3: Makefile as Deployment Interface

The Makefile is the single deployment interface. All deployments go through `make` targets — never raw `docker compose`, `ssh`, or `git` commands individually. This follows the [Makefile ADR](https://github.com/veloxforce/velox-global-adrs/blob/main/docker-compose-makefile-targets-standardization.md).

**Standard targets (every project):**

```makefile
.DEFAULT_GOAL := help  # REQUIRED: bare `make` shows help, never deploys

# Production
deploy          # Build and deploy production
logs            # Follow production logs
status          # Show production container status
start / stop    # Start / stop production containers
restart         # Restart production containers

# Staging
staging         # Build and deploy staging
staging-logs    # Follow staging logs
staging-status  # Show staging container status
staging-start / staging-stop
staging-restart

# Utility
help            # Display available commands (DEFAULT)
build           # Build Docker image with version info
```

**Two deployment topologies:**

The target spec is fixed. The implementation behind each target depends on where the server is relative to the developer.

**Topology A — SSH Remote Deploy (developer's laptop → server):**
The developer runs `make deploy` or `make staging` locally. The Makefile SSHes into the target server, pulls the latest code, builds the image on the server, and starts the containers. This is the standard for servers the developer doesn't work on directly.

```makefile
STAGING_HOST   := {ssh-alias}
PROD_HOST      := {ssh-alias}
REMOTE_DIR     := ~/projects/{project}

staging:
	ssh $(STAGING_HOST) "cd $(REMOTE_DIR) && git pull origin staging && \
		make build && docker compose -f docker-compose.staging.yml down && \
		docker compose -f docker-compose.staging.yml up -d"
	@sleep 5
	@ssh $(STAGING_HOST) "curl -sf http://localhost:{PORT}/health" && \
		echo "✅ Staging deploy complete!" || echo "❌ Health check failed"
```

**Prerequisite:** SSH access to the target server (via SSH config alias). Some servers may require VPN — this is a project-specific access detail, not a deployment pattern concern.

**Topology B — Local Server Deploy (commands run on the server itself):**
The developer (or CI) runs commands directly on the server. The Makefile wraps `docker compose` commands without SSH. This is the standard for servers where the developer has a terminal session.

```makefile
deploy:
	docker compose -f docker-compose.prod.yml up -d --build

staging:
	docker compose -f docker-compose.staging.yml up -d --build
```

**Why Makefile over raw commands:** Prevents accidental deployments (`make` = help), encapsulates multi-step sequences (pull → build → deploy → health check), and provides a discoverable interface (`make help` lists all targets). The Makefile is the only file a developer needs to read to understand deployment operations.

**Reference:** [ROHDEX Makefile](https://github.com/MariusWilsch/rohdex/blob/staging/Makefile) (Topology B), [Call2Tanss Makefile](https://github.com/MariusWilsch/call2tanss/blob/main/Makefile) (Topology B)

### Part 4: Environment Separation

Environment variables are managed through two mechanisms: `.env` files for secrets and credentials, compose file `environment:` blocks for environment-specific behavior.

**`.env.example` (tracked in git):**
Documents every environment variable the application needs, with placeholder values and comments explaining what each one does. This is the template for setting up a new server.

**`.env` (server-local, never in git):**
Each server maintains its own `.env` file based on `.env.example`. These files contain real credentials and are never transferred during deployment. When deploying via SSH git pull, the `.env` stays on the server — only code changes are pulled.

**Compose `environment:` block:**
Environment-specific config that isn't secret goes in the compose file directly. The `ENVIRONMENT` variable (`staging` or `production`) is always set here. Any config that switches behavior between environments (e.g., which external service provider to use) also goes here — visible in the compose file, not hidden in `.env`.

**Separation rule:** Secrets → `.env` file. Behavior switches → compose `environment:` block. This makes environment differences visible by diffing the two compose files, without exposing credentials.

**Reference:** [ROHDEX .env.example](https://github.com/MariusWilsch/rohdex/blob/staging/.env.example)

### Part 5: Health Endpoint & Deploy Verification

Every backend exposes a `/health` endpoint that serves two purposes: container health checks (Docker restarts unhealthy containers) and deploy verification (the Makefile confirms the right code is running after deploy).

**Minimum `/health` response:**
```json
{"status": "healthy", "timestamp": "2026-03-09T12:00:00Z"}
```

**Recommended: version baking.** The `make build` target captures git metadata at build time and bakes it into the image. The `/health` endpoint returns this metadata, providing proof of exactly which commit is running.

```json
{
  "status": "healthy",
  "version": "v1.2.0",
  "git_sha": "5f71337",
  "build_time": "2026-03-09T12:00:00Z"
}
```

**How version baking works:**
1. `make build` runs before `docker build`
2. It captures `git describe --tags`, `git rev-parse --short HEAD`, and current timestamp
3. Writes these to a language-specific file (e.g., `app/version.py` for Python)
4. The health endpoint imports and returns these values
5. After deploy, the Makefile curls `/health` to confirm the new `git_sha`

**Why this matters:** "Is the right code running?" is the most common post-deploy question. The git SHA in `/health` answers it definitively. Without it, you're guessing based on timestamps or container restart times.

**Cross-pattern note:** If this service needs a public subdomain, see the Caddy Subdomain deployment pattern for reverse proxy setup. Docker Compose handles the container on `localhost:{PORT}` — Caddy handles routing external traffic to it.

**Reference:** [ROHDEX /health endpoint](https://github.com/MariusWilsch/rohdex/blob/staging/app/main.py), [ROHDEX make build](https://github.com/MariusWilsch/rohdex/blob/staging/Makefile)

---

## Source

- **Issue:** [#712](https://github.com/DaveX2001/deliverable-tracking/issues/712) (Deployment Pattern Standardization epic)
- **Meta Design Doc:** [deployment-pattern-standardization-design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-pattern-standardization-design)
- **Makefile ADR:** [docker-compose-makefile-targets-standardization](https://github.com/veloxforce/velox-global-adrs/blob/main/docker-compose-makefile-targets-standardization.md)
- **Reference Impl (Topology A):** *(no current reference — ROHDEX migrated to Topology B in [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067))*
- **Reference Impl (Topology B):** [MariusWilsch/rohdex](https://github.com/MariusWilsch/rohdex) — Dockerfile, docker-compose.prod.yml, docker-compose.staging.yml, Makefile | [MariusWilsch/call2tanss](https://github.com/MariusWilsch/call2tanss) — Makefile, docker-compose.yml, docker-compose.staging.yml
- **ROHDEX CI/CD Design:** [ci-cd-staging-design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/ci-cd-staging-design)
- **Session:** [0d7eff70](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/0d7eff70-d608-42cf-a96b-b76081eeef5a.jsonl)
