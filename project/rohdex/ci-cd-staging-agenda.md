---
publish: true
---

# Network Access Request: RDX-APP-01 → GitHub
[[project-rohdex]]

Technical requirements for enabling git-based deployment from RDX-APP-01. Presented by Marius to Sikander Wenzel (Gmelch IT).

---

## Current Situation

RDX-APP-01 (192.168.44.11) runs the ROHDEX Dokumentenverarbeitung system. The server currently has **no outbound internet access** — the Gmelch IT firewall blocks all outbound connections. Code deployment requires an external developer to push files to the server via VPN + rsync.

## What We Need

Outbound HTTPS access from RDX-APP-01 to GitHub, so the server can pull code updates directly.

### Required Firewall Rules

| Rule | Protocol | Port | Destination | Purpose |
|------|----------|------|-------------|---------|
| 1 | HTTPS | 443 | `github.com` | Git clone/pull over HTTPS |
| 2 | HTTPS | 443 | `*.githubusercontent.com` | Git LFS, raw file downloads |
| 3 | DNS | 53 | existing DNS server | Resolve GitHub domains |

### Optional (if SSH-based git is preferred)

| Rule | Protocol | Port | Destination | Purpose |
|------|----------|------|-------------|---------|
| 4 | SSH | 22 | `github.com` | Git clone/pull over SSH |

### IP Ranges (if domain-based rules aren't supported)

GitHub publishes their IP ranges at: `https://api.github.com/meta`

Key ranges (as of 2026):
- `github.com`: 140.82.112.0/20
- `*.githubusercontent.com`: 185.199.108.0/22

## Why This Helps

- **Simpler deployment:** Server pulls code directly instead of requiring VPN + external push
- **Enables automation:** CI/CD pipeline can trigger deployments on code merge
- **Reduces manual steps:** Currently every deploy requires a developer to VPN in and rsync files
- **Security:** HTTPS-only, read-only access to a single private repository. No inbound ports opened.

## What Doesn't Change

- No inbound firewall rules needed (server doesn't receive connections from GitHub)
- VPN access for SSH administration remains unchanged
- All other outbound restrictions remain in place
- Only the specific domains listed above need to be allowed

## Verification

After the firewall rules are applied, we can verify with:

```bash
# Test HTTPS connectivity
curl -s https://github.com > /dev/null && echo "OK" || echo "BLOCKED"

# Test git clone
git clone https://github.com/MariusWilsch/rohdex.git /tmp/test-clone
```

## Related

- **Design Doc:** [CI/CD & Staging Design](ci-cd-staging-design) — Part 2: Path B
- **Issue:** [#1067](https://github.com/DaveX2001/deliverable-tracking/issues/1067)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

