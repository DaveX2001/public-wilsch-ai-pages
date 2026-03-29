# Phase 1: Testing Ready - Design Doc
[[client-paul]]

---
publish: true
date: 2026-01-13
issue: "#353"
---

## Goal

Get the system to a state where Paul's team can **test and create videos**. This unblocks validation before launch polish.

---

## Infrastructure Decisions

### CPU Server (Video Assembly)

| Option | Specs | Cost | Recommendation |
|--------|-------|------|----------------|
| **Hetzner CPX62** | 16 shared vCPU, 32GB RAM | €46/mo | Start here for testing |
| **Hetzner CCX53** | 32 dedicated vCPU, 128GB RAM | €192/mo | Production upgrade |

**Why these specs:** Video assembly uses OpenCV Ken Burns effect with ProcessPoolExecutor. Each worker needs ~1.4GB RAM. 32GB supports 15+ workers.

**Note:** Server cost is temporary. Once video gen moves to serverless CPU workers (like GPU path), the dedicated server becomes just an orchestrator.

### GPU Platform (TTS + Image Gen)

| Decision | Detail |
|----------|--------|
| **Strategy** | Drain existing RunPod credits, then consolidate to Modal |
| **Why Modal** | Better platform overall (despite one quirk) |
| **Funding** | Paul funds consolidated account |

---

## Phase 1 Blockers

| # | Title | Owner |
|---|-------|-------|
| #475 | Production Infrastructure (32 CPU + GPU) | Paul |
| #460 | Docker Redeploy on new server | Marius |
| #491 | Template-channel linking bug | Marius |
| #508 | Per-Template Assets via Supabase Storage | Developer |

---

## Sequence

```
1. Paul provisions server (Hetzner) + GPU credits (Modal)
2. Marius redeploys Docker (#460)
3. Marius fixes linking bug (#491)
4. Developer starts on template assets (#508)
5. Paul's team can test
```

---

## What Paul Provides

1. **Server** - Provision Hetzner (€46-192/mo)
2. **GPU Credits** - Fund Modal account
3. **Testing** - Team validates video creation flow

---

## Success Criteria

- Video can be created end-to-end on new infrastructure
- Template-channel linking persists
- Paul's team reports "usable for testing"

---

## Related

- **Parent Issue:** #353 (Tier 1 Product Launch)
- **Phase 2:** Post-testing fixes
- **Avatar:** Separate workstream (paid scope, end of January)

---

## Full Launch Structure

### Phase 1: Testing Ready
**Goal:** Paul's team can test and create videos

- Infrastructure provisioned (server + GPU credits)
- Docker deployed on new server
- Critical bugs fixed
- Review existing completed work

**Outcome:** Working system for internal testing

### Phase 2: Launch Readiness
**Goal:** Polish based on testing feedback

- Fix issues discovered during testing
- UX improvements (wizard steps, admin pages)
- Schema cleanup
- Modal migration completion

**Outcome:** Production-ready for first paying customer

### Paid Workstream
**Goal:** Market differentiation + scale readiness

- **Avatar integration** - YouTube monetization compliance
- **Queue backpressure** - Handle concurrent jobs without cascade failures
- **GPU optimization** - Faster TTS and image generation
- **Competition feature** - Track and analyze competitor channels

**Outcome:** Scalable platform that "destroys the market"

### Timeline

- **Phase 1** → ~1 day
- **Phase 2** → Testing feedback dependent
- **Paid** → End of January (Avatar deadline)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

