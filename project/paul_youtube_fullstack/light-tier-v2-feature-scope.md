# Tier One Feature Scope
[[client-paul]]

---
publish: true
date: 2025-12-28
updated: 2026-01-03T08:35
source: Dec 27 Paul-Marius call transcript + Jan 3 Paul confirmations
issue: "#353"
---

## Overview

**Tier One** = Simplified, sellable version of Paul for small users (unlimited channels, 1 video/day).

**Architecture:**
- v2 domain with new frontend (Strangler Pattern)
- Supabase Auth + FastAPI backend
- v1 (existing "big boys") and v2 (Tier One) coexist
- Migrate page-by-page, not big bang rewrite

**Key Decision:** New frontend via Strangler Pattern (not feature flags in same codebase).

**Tier Naming:**
- **Tier One** = Simplified tier (this doc)
- **Tier Two+** = Undefined - scope TBD after Tier One launch

---

## Navigation Items

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| Script Writer | ❌ NOT IN | Big boys only |
| Config | ⏸️ DEFER | Admin only, revisit later |
| Thumbnail Manager | ❌ NOT IN | Internal graphic designers |
| Create Video Template | ❌ NOT IN | Predefined templates only |
| Create Voice | ❌ NOT IN | Predefined voices only, no cloning |
| Channels (from Niches) | ✅ IN | Rename + build |
| Competition | ✅ IN | Tracking + analysis |
| Queue | ✅ IN | Simplified view |
| Script Checker | ✅ IN | Free feature |
| Master Prompt Builder | ✅ IN | Core feature |

---

## Channels Feature (formerly Niches)

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| Create channel | ✅ IN | Unlimited |
| Edit channel | ✅ IN | Basic CRUD |
| Delete channel | ❌ NOT IN | No hard delete |
| Soft delete channel | ✅ IN | Rename + hide from display |
| YouTube OAuth + Upload | ✅ IN | Confirmed Jan 3 |
| Prompts config | ✅ IN | Optional |
| Workflow config | ✅ IN | Core |
| Preset voice selection | ✅ IN | Select only, no create |
| Speed setting | ✅ IN | Optional |
| Image style | ✅ IN | Predefined + custom |
| Skip image processing | ✅ IN | Toggle |
| Template assignment | ✅ IN | Predefined only |
| Update Statistics | ❌ NOT IN | Advanced |
| Manage Channels button | ✅ IN | Auth visibility |

---

## Competition Feature

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| Add competitor channels | ✅ IN | Core |
| Remove competitor channels | ✅ IN | Core |
| View competitor videos | ✅ IN | Analysis |
| Scrape competitor videos | ✅ IN | YouTube API |
| Filter/sort videos | ✅ IN | Core |

---

## Create Video Flow

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| Title input | ✅ IN | Core |
| Thumbnail Title input | ✅ IN | Core |
| Upload Script (file picker) | ✅ IN | Manual upload |
| "Use Completed Assigned Script" | ❌ NOT IN | Links to Script Writer |
| Video URL input | ✅ IN | Core |
| Title Generation toggle | ✅ IN | Core |
| Description textarea (Title Gen) | ✅ IN | User describes video content |
| Script Preset dropdown (Title Gen) | ✅ IN | Select only, admin creates presets |
| LLM Model dropdown (Title Gen) | ✅ IN | GPT-4o Mini, GPT-4, Claude, Gemini |
| Configure Prompts button | ✅ IN | Optional - uses channel defaults if skipped |
| LLM processing toggle | ✅ IN | Skip/enable AI script enhancement (Step 2) |
| Language badge | ❌ NOT IN | English only at launch |
| Autopilot toggle | ✅ IN | Automation |
| Auto Upload YouTube | ✅ IN | Convenience |
| Enable Video Preview | ✅ IN | Question on GPU load |
| Require Thumbnail button | ✅ IN | Pricing TBD ($1-2) |
| Add CTA | ❌ NOT IN | Phase 2 |

---

## Queue View

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| View queue (own videos only) | ✅ IN | Core |
| View Final Video | ✅ IN | Core |
| Download Video | ✅ IN | Core |
| Thumbnail status indicator | ✅ IN | Shows when ready |
| Edit button (for failures) | ✅ IN | Retry failed jobs |
| Download Images | ❌ NOT IN | Simplified |
| Download Transcript | ❌ NOT IN | Simplified |
| Download Audio | ❌ NOT IN | Simplified |
| Edit Title | ❌ NOT IN | UUID migration |
| Delete | ❌ NOT IN | No delete |
| Redo | ❌ NOT IN | Advanced |
| Clone/Multi | ❌ NOT IN | Big boys |
| Generate Description | ❌ NOT IN | Advanced |
| Upload to YouTube (manual) | ❌ NOT IN | Use auto-upload |

---

## Voice System

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| View voices | ✅ IN | Select from list |
| Select voice | ✅ IN | ~16 predefined |
| Create voice | ❌ NOT IN | No custom |
| Clone voice | ❌ NOT IN | Security risk ("Trump stuff") |

---

## Template System

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| View templates | ✅ IN | Select from list |
| Select template | ✅ IN | Predefined only |
| Create template | ❌ NOT IN | Advanced |
| Edit template | ❌ NOT IN | Advanced |

---

## ImageStyle System

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| View styles (list) | ✅ IN | Predefined + custom |
| Create style | ✅ IN | Both text + image modes |
| Edit style | ✅ IN | Custom styles only (predefined protected) |
| Delete style | ✅ IN | Custom styles only (predefined protected) |
| Preview generation | ✅ IN | Test image before saving |
| Video model selection | ❌ NOT IN | Image model only |
| Predefined styles | ✅ IN | Admin-seeded presets |

---

## Infrastructure

| Sub-feature | Tier One | Notes |
|-------------|---------------|-------|
| v2 subdomain | ✅ REQUIRED | New domain |
| Supabase Auth | ✅ REQUIRED | User registration |
| Stripe integration | ✅ REQUIRED | Payments |
| Tier routing | ✅ REQUIRED | Tier One vs others |
| Dark mode + Light mode | ✅ IN | UI polish |

---

## Question Marks (Need Decision)

| Item | Question | Notes |
|------|----------|-------|
| ~~YouTube OAuth~~ | ~~System load concern - in or out?~~ | ✅ RESOLVED: IN (Jan 3) |
| Thumbnail pricing | Bundled or $1-2 extra? | Affects conversion |
| ~~Channel limit~~ | ~~1 channel or unlimited?~~ | ✅ RESOLVED: Unlimited (Jan 3) |
| ~~Delete channel~~ | ~~Allowed or not?~~ | ✅ RESOLVED: No hard delete, soft delete only (Jan 3) |
| Config page | What parts needed? | Deferred - revisit later |
| ~~Predefined style protection~~ | ~~Can users edit/delete predefined styles?~~ | ✅ RESOLVED: Protected - CRUD for custom only (Jan 3) |

---

## Summary Counts

| Category | IN | NOT IN | TBD |
|----------|-----|-----|-----|
| Navigation Items | 5 | 5 | 1 |
| Channels | 11 | 2 | 0 |
| Competition | 5 | 0 | 0 |
| Create Video Flow | 14 | 3 | 0 |
| Queue View | 5 | 9 | 0 |
| Voice System | 2 | 2 | 0 |
| Template System | 2 | 2 | 0 |
| ImageStyle System | 6 | 1 | 0 |
| Infrastructure | 5 | 0 | 0 |
| **TOTAL** | **55** | **24** | **1** |

---

## Implementation Approach

**JIT Vertical Slicing** (validated against Strangler Pattern)

### The Pattern

```
Start: Create Video UI (v2, Supabase)
       │
       ▼
   Empty dropdown? ──► Build Niche/Channel entity
       │
       ▼
   FK error (no voices)? ──► Seed Voice data
       │
       ▼
   Keep going until end-to-end works
       │
       ▼
   Move to next feature
```

### Why This Works

1. **Strangler Pattern recommends vertical slices by business domain**, not horizontal technical layers
2. "Extracting by technical layers (database → services → UI) is problematic" - industry consensus
3. Start with user-facing functionality, build dependencies as discovered
4. Scope doc = MAP (what entities exist), Errors = ORDER (when to build each)

### Known Dependencies

When building Create Video UI, these entities will be needed:

| Entity | UI Name | When Needed |
|--------|---------|-------------|
| Voice | Voice dropdown | Niche creation / Step 3 |
| ImageStyle | Image Style dropdown | Niche creation / Step 4 |
| VideoGenTemplate | Video Template dropdown | Niche creation |
| PromptConfig | Prompt configuration | Script generation |
| Template | Niche/Channel | Create Video Step 1 |

### Starting Point

**Create Video Flow** → discover → build → continue

---

## Source References

- **Transcript:** Dec 27, 2025 Paul-Marius call (Fireflies: 01KDG0H66T52T8BW7736QJRK83)
- **Migration Framework:** [supabase-migration-framework](./supabase-migration-framework.md)
- **Strangler Pattern ADR:** [adr-strangler-pattern-sequencing](./adr-strangler-pattern-sequencing.md)
- **Issue:** DaveX2001/deliverable-tracking#353

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

