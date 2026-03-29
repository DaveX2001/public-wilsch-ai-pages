---
publish: true
---

# Vercel Hobby Multi-Dev Deploy
[[vercel-hobby-multi-dev-deploy]]

How to deploy frontends to Vercel with multiple developers on the Hobby plan, using GitHub Actions and a centralized deploy token to bypass per-seat restrictions.

---

## Problem Statement

Vercel's Hobby plan restricts deployments to the account owner. When a developer whose git email doesn't match the Vercel account pushes code, Vercel rejects the deploy with "Git author X must have access to the team." The Pro plan removes this restriction but costs $20/seat/month — $60/month for a 3-person team, scaling linearly with every new contributor.

This pattern eliminates that cost by routing all deployments through GitHub Actions with a single deploy token owned by the Vercel account holder. Developers push code normally; GHA deploys on their behalf.

**Preconditions:**
- Frontend project hosted on GitHub, deployed to Vercel
- Multiple developers push code (account owner + at least one other contributor)
- Hobby plan preferred over Pro (cost optimization)
- GitHub Actions minutes available (2000 min/month on private repos)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A developer (or AI agent) starting a new frontend project can set up multi-dev Vercel deployment on Hobby plan by following this doc — no searching other repos, no tribal knowledge. |
| **Success** | The pattern explains how we did it (concrete steps), why we did it (decision rationale), and that it works (proof from production). All three dimensions are present and specific. |
| **Done test** | Can someone replicate this pattern for a new project using only this design doc + the linked reference implementation? If yes → done. If they need to ask "but how do I...?" → gap exists. |

---

## Approach

### Part 1: GHA as Deployment Proxy

The core idea: decouple who pushes code from who deploys it. Developers push to GitHub normally. GitHub Actions deploys to Vercel using a centralized deploy token owned by the Vercel account holder. Vercel sees every deployment as coming from the account owner — the Hobby plan restriction never triggers.

This requires disabling Vercel's built-in git integration (`"git": {"deploymentEnabled": false}` in `vercel.json`) so that GHA is the sole deployment path. Without this, Vercel attempts its own deploy on push and rejects non-owner commits.

**Prerequisites:**
- [Staging-first repository setup](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/staging-first-repository-setup) — branch strategy is a prerequisite, not part of this pattern
- Vercel project created (don't import repo — git integration stays disabled)
- Deploy token generated: Vercel Settings → Tokens → Create Token
- Three GitHub Secrets configured: `VERCEL_TOKEN`, `VERCEL_ORG_ID`, `VERCEL_PROJECT_ID`

### Part 2: Git Author Workaround

Vercel Hobby plan checks the git commit author email against team membership — even when deploying via CLI in CI. If the commit author isn't the account owner, Vercel rejects the deploy.

The workaround: each GHA workflow amends the commit author to the Vercel project owner's email before deploying. This only affects the temporary GHA runner — the actual commit in the repository is unchanged.

```yaml
- name: Set Vercel deploy identity
  run: |
    git config user.email "<vercel-project-owner-email>"
    git config user.name "Deploy Bot"
    git commit --amend --reset-author --no-edit --allow-empty
```

**Always include this step in every workflow.** The Vercel account owner and the developers pushing code are typically different people. Without it, Vercel rejects deploys from non-owner emails.

### Part 3: Three-Workflow Architecture

Three workflows map to three deployment contexts:

| Workflow | Trigger | Vercel Target | Purpose |
|----------|---------|---------------|---------|
| `staging.yaml` | Push to `staging` | Preview (stable URL) | UAT environment |
| `production.yaml` | Push to `main` | Production | Live users |
| `preview.yaml` | PR against `staging` | Preview (ephemeral) | Feature review |

Each workflow follows the same sequence: checkout → amend author → install Vercel CLI → `vercel pull` (fetch env vars) → `vercel build` → `vercel deploy --prebuilt`. Production adds `--prod` flag for build and deploy.

The preview workflow additionally captures the deployment URL and posts it as a PR comment, requiring explicit `pull-requests: write` permission.

**Env var management:** Deploy credentials (token, org ID, project ID) live in GitHub Secrets. Application env vars (API keys, database URLs) are managed in Vercel's dashboard and fetched into the GHA runner via `vercel pull` before build.

### Part 4: Known Constraints

| Constraint | Impact | Mitigation |
|------------|--------|------------|
| Hobby bandwidth: 100GB/month | Sufficient for client-facing sites, not high-traffic SaaS | Monitor via Vercel dashboard |
| Serialized builds | Only one build runs at a time | Rarely an issue for small teams |
| Preview Deployment Protection | Vercel returns HTTP 401 for preview URLs on Hobby | Verify deploys via GHA run status + PR comments, not HTTP 200 |
| GHA minutes: 2000/month (private) | Sufficient for this pattern | Monitor if build frequency increases |
| No org dashboard | Can't see all projects in one view | Use `vercel ls` CLI instead |

**Always use Hobby.** No threshold for switching to Pro — use this workaround until Vercel forces an upgrade.

### Relationship to Other Patterns

- **Caddy Subdomain:** When a Vercel-hosted frontend needs a custom subdomain (e.g., `rechnung.wilsch-deployment.com`), Caddy proxies to Vercel. The frontend convention is to call `/api/*` for backend requests — Caddy handles the routing. See Caddy Subdomain design doc for implementation.
- **Vercel ADR #011:** The ADR established "use Vercel for frontends." This doc explains the specific Hobby multi-dev workaround. ADR #011's deployment parts are absorbed here; its `/api` routing content moves to the Caddy design doc.

---

## Source

- **Reference Implementation:** [README.VERCEL_DEPLOY_WITHOUT_PRO.MD](https://github.com/DaveX2001/klimafolgenschutz-website/blob/staging/README.VERCEL_DEPLOY_WITHOUT_PRO.MD) (klimafolgenschutz-website)
- **Workflows:** [staging.yaml](https://github.com/DaveX2001/klimafolgenschutz-website/blob/staging/.github/workflows/staging.yaml), [production.yaml](https://github.com/DaveX2001/klimafolgenschutz-website/blob/staging/.github/workflows/production.yaml), [preview.yaml](https://github.com/DaveX2001/klimafolgenschutz-website/blob/staging/.github/workflows/preview.yaml)
- **Issues:** [#849](https://github.com/DaveX2001/deliverable-tracking/issues/849) (original implementation), [#1013](https://github.com/DaveX2001/deliverable-tracking/issues/1013) (merged into #712), [#712](https://github.com/DaveX2001/deliverable-tracking/issues/712) (Operations Manual epic)
- **Existing ADR:** [#011 Vercel Branch Environments](https://github.com/veloxforce/velox-global-adrs/blob/main/use-vercel-for-frontend-deployments-with-branch-environments.md) (to be deprecated → absorbed here + Caddy doc)
- **External Reference:** [DaviZCodes/vercel-hobby-collaboration](https://github.com/DaviZCodes/vercel-hobby-collaboration-github-actions-workflow)
- **Meta Design Doc:** [Deployment Pattern Standardization](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/deployment-pattern-standardization-design)
- **Session:** [Probe bcd7a662](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-00-WILSCH-AI-INTERNAL--deliverable/bcd7a662-a2ff-469c-b20b-4e01c1ac4c5e.jsonl)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

