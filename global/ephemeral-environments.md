---
publish: true
---

# Ephemeral Environments
[[testing-methodology]]

Future upgrade path for scaling beyond sequential staging.

## Core Insight: Human Attention is the Bottleneck

Human attention is the scarcest resource, not compute. Every layer of automated feedback (backpressure) preserves human attention for things only humans can judge.

**The Backpressure Stack:**

| Layer | Catches | Automated? | Human Attention |
|-------|---------|------------|-----------------|
| **Sanity checks** | Build fails, missing deps | ✅ Yes | ❌ Not needed |
| **AC verification** | Spec violations | ✅ Yes | ❌ Not needed |
| **Rebase (integration)** | Merge conflicts | ✅ Yes | ❌ Not needed |
| **Ephemeral test** | Runtime failures | ✅ Yes | ❌ Not needed |
| **Human witness** | Experience issues | ❌ No | ✅ ONLY HERE |

**The principle:** *Every automatable check that catches issues before human witness = human attention preserved.*

## Why Ephemeral Environments?

**The bottleneck problem:**
- Developer receives 3-5 issues
- Implementation + AC verification can run in parallel (worktrees)
- But staging is sequential → bottleneck
- Local testing is unreliable (missing configs, "works on my machine")

**Why not local-first?**
- Same investment as server ephemeral
- Only solves reliability, not velocity or isolation
- Developer machines differ in CPU, memory, network

## Why Rebase-First?

**Test the true state, not a lie:**

| Approach | What you test | Reality gap |
|----------|---------------|-------------|
| **Pure isolation** | Feature alone | ❌ State never exists in production |
| **Integration preview** | Feature + staging | ✅ This IS what production will see |

**Rebase = integration backpressure:**
- Merge conflict is automated feedback
- AI gets immediate signal: "this won't integrate cleanly"
- Resolves before wasting time testing something that can't merge

**Martin Fowler's recommendation:** Always rebase into preview first, otherwise you're testing a hypothetical state.

## Conflict Resolution as Collaboration

When rebase fails:
1. Script returns error to AI
2. AI + developer resolve together (pair programming)
3. Resolution may reveal deeper design issues
4. Re-run script after resolution

**The conflict IS the signal** - you can't spin up ephemeral until integration is clean.

## Implementation Pattern

```bash
./ephemeral-up.sh {issue}
    ↓
git fetch origin staging
git rebase origin/staging
    ↓
IF conflict → Return error, AI + developer resolve
IF success → docker compose -p paul-{issue} up -d
```

**Routing:** Caddy wildcard → `paul-{issue}.wilsch-deployment.com`

**Port allocation:** 8000 + issue_number

## When to Upgrade from Sequential Staging

- Deployment queue blocks work >1x/week
- Features break each other in staging
- Team approaches 7-10 developers

**Reference:** [DaveX2001/deliverable-tracking#527](https://github.com/DaveX2001/deliverable-tracking/issues/527) - Paul implementation

## Related

- [[staging-uat-workflow]] - Current workflow (sequential staging)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

