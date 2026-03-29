---
publish: true
---

# Paul Call Agenda: Light Tier Scope
[[client-paul]]

**Date:** 2025-12-27
**Goal:** Define Light Tier feature scope to unblock #353

---

## Current State Recap

### Dependency Model
```
#36 Multi-tenant (umbrella)
├── #121 Frontend Consolidation ──► NEAR COMPLETE
│   ├── #228 Thumbnail ✓ ready to merge
│   ├── #229 Template → decision needed (standalone?)
│   ├── #230 Auth → LAST (per ADR)
│   └── #301 Script-Writer ✓ done
│
├── #330 Pipeline Parallelization ──► LAST MILE
│   ├── #328 ImageGen → needs acceptance
│   ├── #329 VideoGen Spike → needs acceptance
│   ├── #349 Ken Burns → in progress
│   ├── #299 Chatterbox TTS ✓ non-blocking
│   └── #335 Whisper GPU ✓ non-blocking
│
└── #353 Light Tier Product Launch ──► BLOCKED
    └── #354 This call (scope decisions)
```

### Blockers for #353
1. **#121 decisions** → This call resolves (which frontends in Light tier?)
2. **#330 completion** → #349, #328, #329 finishing up

### Architecture Decision
- **Two domains:** v1.paul.com (existing), v2.paul.com (Light Tier)
- **One codebase:** v1 frozen on old commit, v2 active development
- **No token exchange needed:** Each domain self-contained

---

## Context for Paul

- **Target user:** 1 channel, 1 video/day (small creator)
- **v2 stack:** Supabase + FastAPI + simplified frontend
- **Goal:** Sellable product with clear pricing

---

## Questions Per Frontend

### 1. Script-Writer (absorbed)
**Q1:** Include in Light tier?
- [ ] YES (all features)
- [ ] PARTIALLY → which features excluded?
- [ ] NO → Full tier only / Standalone / Deprecate?

### 2. Config (absorbed)
**Q1:** Include in Light tier?
- [ ] YES (all features)
- [ ] PARTIALLY → which features excluded?
- [ ] NO → Full tier only / Standalone / Deprecate?

### 3. Thumbnail (absorbed)
**Q1:** Include in Light tier?
- [ ] YES (all features)
- [ ] PARTIALLY → which features excluded?
- [ ] NO → Full tier only / Standalone / Deprecate?

### 4. Template (absorbed)
**Q1:** Include in Light tier?
- [ ] YES (all features)
- [ ] PARTIALLY → which features excluded?
- [ ] NO → Full tier only / Standalone / Deprecate?

---

## Pricing Questions

**Q5:** Light tier pricing model?
- [ ] Per channel
- [ ] Per video
- [ ] Flat monthly
- [ ] Other: ___

**Q6:** Full tier pricing model?
- [ ] Per channel
- [ ] Per video
- [ ] Flat monthly
- [ ] Other: ___

**Q7:** Upgrade path Light → Full?
- [ ] Self-service upgrade
- [ ] Contact sales
- [ ] Automatic based on usage
- [ ] Other: ___

---

## Decisions Made

| Topic | Decision | Notes |
|-------|----------|-------|
| Script-Writer | | |
| Config | | |
| Thumbnail | | |
| Template | | |
| Light pricing | | |
| Full pricing | | |
| Upgrade path | | |

---

## Next Steps After Call

- [ ] Update #353 with scope decisions
- [ ] Update #354 with call notes
- [ ] Create sub-issues for Light Tier features
- [ ] Define v2 MVP checklist

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

