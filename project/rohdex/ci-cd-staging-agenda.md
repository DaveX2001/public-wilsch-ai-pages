---
publish: true
---

# Meeting Agenda: Gmelch IT Network Access — RDX-APP-01
[[project-rohdex]]

## Meeting Goal

Clarify the network change request needed for Gmelch IT to enable git-pull deployment from RDX-APP-01 (Path B improvement).

1. **Request framing** — what Marius communicates to Sikander
2. **Feasibility signal** — whether this is straightforward or politically complex

## Pre-Read

- [CI/CD & Staging Design Doc](ci-cd-staging-design) — Part 2: Path B explains why GitHub access would improve the deployment workflow

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Minimal network changes for git-pull from RDX-APP-01
⏱️ 10 min

RDX-APP-01 currently has no outbound access to GitHub (Gmelch IT firewall). Production deployment works today via rsync-push over VPN (Path A). Enabling git-pull would simplify deployment and open the door for CI/CD automation.

- Required: outbound HTTPS (port 443) to `github.com` and `*.githubusercontent.com`
- Required: DNS resolution for these domains from RDX-APP-01
- Optional: outbound SSH (port 22) to `github.com` if SSH clone is preferred
- Gmelch IT's current policy blocks "security-critical" outbound traffic and certain countries — GitHub (US/CDN) may or may not fall under their rules

**To resolve:** The exact framing and channel for requesting this network change from Sikander Wenzel at Gmelch IT.

## Meeting Format

- **Type:** Quick coordination (async or 5-min sync with Marius)
- **Expectation:** Marius reviews the technical requirements and decides how to approach Sikander
- **Outcome:** Request either submitted or deferred with reasoning

## Related

- **Issue:** [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067)
- **Design Doc:** [CI/CD & Staging Design](ci-cd-staging-design)
