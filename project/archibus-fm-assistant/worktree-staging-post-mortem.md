---
publish: true
---

# Worktree vs Staging Workflow — Post-Mortem Analysis
[[worktree-staging-post-mortem]]

Post-mortem of the ARCHIBUS developer workflow (March 4–20, 2026). Covers the staging deployment confusion, IITR comparison, and corrective actions — building on IITR and Archibus post-mortem findings.

---

## Problem Statement

The developer workflow breaks when the gap between code (git branch) and runtime (Docker container) is manual. "Merge to staging" means two different things: pushing code to the staging branch, and deploying that code into the correct Docker container on the server. Without CI/CD auto-deploy, these are separate steps — and the second step is where errors occur.

The ARCHIBUS #1189 pipeline was implemented correctly, merged to the staging branch, and deployed — but into the wrong container (spike-a :3020 instead of archibus-chat-staging :3081). The developer instructed the AI to "push to staging," and the AI chose the spike container because no authoritative source documented which container IS staging. The IITR project avoided this failure because the developer worked directly on the server where containers run — no deployment gap existed.

This is NOT a worktree problem. Worktrees worked correctly for code isolation. This is NOT a code quality problem. Both pipelines produced correct output. The failure is structural: a manual deployment step with no authoritative target definition.

**Preconditions:**
- No CI/CD auto-deploy for Docker Compose backends (merge ≠ deploy)
- No CLAUDE.md deployment identity documenting which container is staging
- Spike containers (#1167) persisted alongside staging containers, creating ambiguous targets
- AI has no mechanism to determine the "correct" deployment target — it guesses from context

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Developer merges to staging branch → code is automatically deployed to the correct Docker container. No manual SSH or Docker steps. No AI decision-making about deployment targets. |
| **Success** | The staging deployment confusion observed in ARCHIBUS #1189 (wrong container) and documented in CCI #646 (5 deployment observations) cannot recur structurally — CI/CD script is the single source of truth for which container receives which code. |
| **Done test** | Developer merges PR to staging, visits the staging URL, and sees the new code running — without any manual intervention between merge and verification. |

---

## Approach

### Part 1 — Root Cause: "Staging" Means Two Things

The word "staging" refers to both a git branch (where code lives) and a Docker deployment (where code runs). Without auto-deploy, merging to the staging branch does NOT deploy to the staging container — a manual SSH + Docker step sits between them.

The ARCHIBUS failure chain:
1. Developer implemented #1189 in a worktree (code isolation — worked correctly)
2. Merged to staging branch (git operation — worked correctly)
3. AI deployed to spike-a container (:3020) instead of archibus-chat-staging (:3081)
4. Developer saw "spike-a" in the URL but trusted the AI's path selection
5. Marius discovered the error during grooming — code was in an isolated spike container

**Why AI chose wrong:** No CLAUDE.md deployment identity existed. The spike container from #1167 was still running. The AI inferred "archibus" + "staging" = the archibus-bulk-import compose stack, which was the spike — not the real staging LibreChat.

**Why IITR avoided this:** Developer SSH'd directly into the server, edited files where containers run, restarted Docker locally. No deployment gap. Code and runtime were co-located.

### Part 2 — Developer Workflow Definition

Staging is the developer's playground. Testing happens there — always in the right container, with the right pipeline, at the right URL. Worktrees remain the standard for code isolation; validation always happens on staging.

**Authority model:**
- Developer (David) pushes to staging freely — no approval needed
- SA (Marius) reviews staging → main only
- Universal rule across all projects (established in ROHDEX #1067, confirmed for ARCHIBUS)

**Flow:** Worktree → merge to staging → auto-deploy → verify on staging URL

### Part 3 — Corrective: CI/CD Auto-Deploy

The fix is structural: CI/CD makes merge = deploy. The developer merges to the staging branch; a GitHub Actions workflow SSHs to the server, pulls the code, and rebuilds the correct Docker container. No manual step. No AI decision-making about containers.

**Scope:** All Docker Compose backends (ARCHIBUS, ROHDEX, IITR), ARCHIBUS first. The GHA template already exists in the Docker Compose Design Doc (Part 6, Session 163) but has not been deployed to any project.

**Tracking:** CCI #646 (Release: Deployment & Staging Workflow Not Embodied) is the release epic under CCI #635. Postmortem #1210 cross-references #646 — decomposition into per-project implementation issues happens separately.

**Supporting fix:** Every project repo must have CLAUDE.md deployment identity (container name, port, server path, rebuild command). This is documented as a standard in CCI #646 observations and the Operations Manual (Session 163). Even with CI/CD, CLAUDE.md identity prevents the "which container is staging?" confusion in AI sessions.

### Part 4 — Acceptance Surface Impact

The Archibus post-mortem identified "acceptance surface discovered, not defined" as a central failure — the test rubric expanded through 11 witness ceremony findings (F1–F11). Most of those findings were infrastructure gaps: wrong subdomain, missing deployment identity, repos in wrong directories, test data missing from containers.

CI/CD auto-deploy + CLAUDE.md deployment identity eliminates this class of infrastructure findings structurally. Witness ceremonies can then focus on functional verification (does the agent produce correct output?) rather than infrastructure discovery (is the code in the right container?).

The existing AC workflow (rubber-duck → AC definition → verification) already handles functional acceptance criteria. No additional process change needed for functional testing.

---

## Source

- **Transcript:** [Grooming 2026-03-20](https://app.fireflies.ai/view/01KM5NVDEW1PEGZGBDV6YNGWEB) — staging deployment confusion discovered live
- **Post-Mortems:**
  - [IITR Post-Mortem](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-post-mortem) — spike primitive, trunk-first decomposition
  - [Archibus Post-Mortem](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/archibus-post-mortem) — acceptance surface, witness ceremony expansion
- **CCI Observations:**
  - [#657](https://github.com/DaveX2001/claude-code-improvements/issues/657) — Trunk-First Decomposition position epic (latest comments: deployment confusion)
  - [#646](https://github.com/DaveX2001/claude-code-improvements/issues/646) — Release: Deployment & Staging Workflow Not Embodied (5 observations)
- **Issue Chain:** [#1189](https://github.com/DaveX2001/deliverable-tracking/issues/1189) (E2E pipeline), [#1167](https://github.com/DaveX2001/deliverable-tracking/issues/1167) (Spike A), [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067) (ROHDEX CI/CD — staging authority precedent)
- **Post-Mortem Issue:** [#1210](https://github.com/DaveX2001/deliverable-tracking/issues/1210)
- **Session:** 16e5fa72-4e55-4ef5-95cb-852eaaf1205a

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

