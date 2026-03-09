---
publish: true
---

# Caddy Subdomain Routing
[[caddy-subdomain-routing-design]]

Design doc for the wildcard DNS + Caddy reverse proxy pattern used across all VPS-hosted services.

---

## Problem Statement

Adding a new subdomain to the VPS requires inspecting existing Caddy configs via SSH or finding a scattered ADR that describes the decision but not the operational steps. The pattern is proven — 22 configs run across 10+ services — but no single document tells a developer (or AI agent) how to add a subdomain, why the architecture works this way, and what evidence confirms it.

This is not about building a new deployment pattern. The wildcard DNS + Caddy auto-SSL combination has been running in production since mid-2025. This is an extraction task: document the proven pattern as an actionable operational reference.

**Preconditions:**
- Wildcard DNS `*.wilsch-deployment.com` resolves to VPS IP (`91.99.74.207`) — active, no per-subdomain DNS records needed
- Caddy runs as a systemd service with `import conf.d/*.conf` in the main Caddyfile
- 22 active config files serve 10+ services (archibus, call2tanss, invoice, fireflies, trustgraph, etc.)
- Part of the [Deployment Pattern Standardization](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-pattern-standardization-design) epic (#712)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | An actionable reference for adding, managing, and removing Caddy subdomain configurations on the VPS — covering the full range of proxy targets (localhost, path-based, external/Vercel). |
| **Success** | A developer or AI agent can add a new subdomain by selecting the matching variant example, filling in subdomain name + proxy target, and following the steps. No SSH inspection of existing configs needed. |
| **Done test** | Can #893 (DNS Subdomain Setup for call + rechnung subdomains) be implemented by following this doc alone? If yes → design is complete. |

---

## Approach

### Part 1: Core Architecture

One pattern, three components working together:

1. **Wildcard DNS** — `*.wilsch-deployment.com` resolves to the VPS IP (`91.99.74.207`). Set up once. Every new subdomain automatically resolves without touching DNS.
2. **Caddy TLS termination** — Caddy provisions and renews Let's Encrypt certificates automatically for every subdomain it serves. No certbot, no renewal crons, no manual certificate management.
3. **Caddy reverse proxy** — Each subdomain gets a conf file that proxies requests to a target. The target varies (localhost port, path-based route, external URL) but the mechanism is always `reverse_proxy`.

The architectural insight: wildcard DNS eliminates per-service DNS management. Caddy auto-SSL eliminates certificate management. Together, adding a new subdomain is a conf-file-only operation with zero external dependencies.

| | Wildcard DNS + Caddy | Per-subdomain DNS + nginx + certbot |
|---|---|---|
| New subdomain | 1 conf file, reload | DNS record + nginx conf + certbot cert |
| SSL | Automatic (Let's Encrypt) | Manual certbot setup + renewal cron |
| External deps per service | Zero | DNS provider access needed |

### Part 2: Step-by-Step Process

Adding a new subdomain follows the same 4 steps every time:

**Step 1: Create the conf file**

```bash
sudo nano /etc/caddy/conf.d/{SERVICE_NAME}.conf
```

**Step 2: Write the configuration**

```caddyfile
{SERVICE_NAME}.wilsch-deployment.com {
    reverse_proxy localhost:{PORT}
}
```

See Part 3 for variant examples covering different proxy targets.

**Step 3: Validate**

```bash
sudo caddy validate --config /etc/caddy/Caddyfile
```

**Step 4: Reload (zero downtime)**

```bash
sudo systemctl reload caddy
```

Verify with:
```bash
curl -I https://{SERVICE_NAME}.wilsch-deployment.com
```

SSL certificates are provisioned automatically on first request — no additional steps.

### Part 3: Variant Examples

The core pattern is always the same — only the proxy target varies. Three variants cover all current use cases:

**Variant A: Simple proxy (backend on VPS)**

Most common. Subdomain proxies directly to a Docker container on localhost.

```caddyfile
# /etc/caddy/conf.d/call2tanss-api.conf
call2tanss-api.wilsch-deployment.com {
    reverse_proxy localhost:5000
}
```

Used by: call2tanss-api (prod :5000, dev :5001, staging :5002), fireflies, trustgraph, archibus, and most other services.

**Variant B: Path-based routing (API behind path prefix)**

Subdomain serves multiple routes, stripping a path prefix before proxying.

```caddyfile
# /etc/caddy/conf.d/invoice.conf
invoice.wilsch-deployment.com {
    handle_path /api/* {
        reverse_proxy localhost:8081
    }
}
```

Used by: Invoice Agent (API behind `/api/*`, frontend on Vercel).

**Variant C: Caddy-to-Vercel proxy (frontend on Vercel)**

> **Status: Designed, not yet proven.** Will be validated when #893 is implemented.

Subdomain proxies to a Vercel deployment. Requires `header_up Host` override so Vercel routes to the correct project.

```caddyfile
# /etc/caddy/conf.d/rechnung.conf
rechnung.wilsch-deployment.com {
    reverse_proxy https://{VERCEL_PROJECT_URL} {
        header_up Host {VERCEL_PROJECT_URL}
    }
}
```

Designed for: #893 — `rechnung.wilsch-deployment.com` proxying to the Invoice Agent Vercel frontend.

### Part 4: Scaling Patterns

The rule: **one subdomain = one conf file.** Multiple conf files per app happen for different reasons:

**Environment multiplication** — each environment gets its own subdomain and conf file, pointing to a different port.

| Conf file | Subdomain | Target |
|-----------|-----------|--------|
| `call2tanss-api.conf` | `call2tanss-api.wilsch-deployment.com` | `:5000` (prod) |
| `call2tanss-api-dev.conf` | `call2tanss-api-dev.wilsch-deployment.com` | `:5001` (dev) |
| `call2tanss-api-staging.conf` | `call2tanss-api-staging.wilsch-deployment.com` | `:5002` (staging) |

Three environments, three Docker containers on different ports, three conf files. Intentional isolation.

**Domain aliases** — the same backend served under multiple domains during migration.

| Conf file | Subdomain | Target |
|-----------|-----------|--------|
| `invoice.conf` | `invoice.wilsch-deployment.com` | `:8081` |
| `invoice-veloxforce.conf` | `invoice.veloxforce.dev` | `:8081` |

Same service, same port, two domains. This is a migration artifact — `veloxforce.dev` was the old domain, `wilsch-deployment.com` is the consolidation target. Eventually the old one gets retired.

### Part 5: Service Management

**Disable a service** (preserve configuration):

```bash
sudo mv /etc/caddy/conf.d/{SERVICE}.conf /etc/caddy/conf.d/{SERVICE}.disabled-$(date +%Y-%m-%d).conf
sudo systemctl reload caddy
```

Caddy imports `*.conf` — renaming away from `.conf` effectively removes the route. The date suffix tracks when it was disabled.

**Re-enable a service:**

```bash
sudo mv /etc/caddy/conf.d/{SERVICE}.disabled-YYYY-MM-DD.conf /etc/caddy/conf.d/{SERVICE}.conf
sudo systemctl reload caddy
```

**List active configurations:**

```bash
ls /etc/caddy/conf.d/*.conf
```

---

## Source

- **Issues:** [#712](https://github.com/DaveX2001/deliverable-tracking/issues/712) (epic), [#893](https://github.com/DaveX2001/deliverable-tracking/issues/893) (blocked — DNS Subdomain Setup)
- **Existing doc (to retire):** [caddy-conf-subdomain-deployment-pattern](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/caddy-conf-subdomain-deployment-pattern)
- **Meta design doc:** [Deployment Pattern Standardization](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-pattern-standardization-design)
- **VPS configs:** `91.99.74.207:/etc/caddy/conf.d/` (22 active configs)
- **Reference repos:** [Call2Tanss](https://github.com/MariusWilsch/Call2Tanss), [Aircall_Tanss_UI](https://github.com/MariusWilsch/Aircall_Tanss_UI), [uwi__invoice-agent](https://github.com/MariusWilsch/uwi__invoice-agent)
- **Session:** [Probe b9343924](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/b9343924-6409-480a-a68c-5fd752158fb4.jsonl)
