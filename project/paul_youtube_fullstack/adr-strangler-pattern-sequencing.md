---
publish: true
---

# ADR: Strangler Pattern Sequencing - Auth Migration After Frontend Absorption
[[client-paul]]

## Status
**Created** | Date: 2025-12-14

## Context
- Multi-frontend codebase (5 React apps) being consolidated into single app (#121)
- Auth system migration from custom JWT to Supabase Auth planned (#230)
- Backend data layer still on local PostgreSQL (FastAPI + Supabase rebuild in progress)
- Need for continuous integration testing during migration

**Problem discovered:** Auth migration (#230) was completed before frontend absorption (#121), creating a state where:
- Old frontends send custom JWT → Flask expects Supabase JWT → **broken**
- New frontend uses Supabase auth → Flask queries local PostgreSQL → **data layer mismatch**
- Result: Zero working integration test environments

## Decision
We will revert #230 and resequence: complete frontend absorption with old auth first, then migrate auth as the final step.

**Correct sequence:**
1. Keep old auth throughout absorption
2. Absorb each frontend incrementally → test → verify works
3. Single frontend achieved, still using old auth
4. THEN migrate auth (one change point, one test cycle)

## Consequences

### ✅ Positive
- Restores working integration test environment immediately
- Follows Strangler Pattern - system remains functional throughout migration
- Each absorption step is independently testable
- Auth migration becomes single atomic change at the end

### ❌ Negative
- Auth-related code absorbed during frontend consolidation will be deleted later
- Requires git history rewrite (reset + force push)
- Reopening of completed issue (#230)

### ⚪ Neutral
- TTS/pipeline work continues on feature branches
- Backend rebuild (#122) proceeds independently
- Database schema decisions unaffected

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Dual-auth support** | Maintenance nightmare - two code paths, debugging complexity, "temporary" becomes permanent |
| **Rush #121 completion** | Still wouldn't fix data layer mismatch; integration testing remains broken |
| **Test in isolation only** | Unbounded debugging pain when pieces eventually integrate |
| **Keep current state** | Violates Strangler Pattern; no working test environment blocks all progress |

---

**Key Principle:**
During migrations, prefer **additive changes** over **breaking changes**. Additive changes keep the system functional; breaking changes affect all integration points simultaneously.

**Key Resources:**
- [Pipeline Orchestration Design](pipeline-orchestration-design.md) - Strangler pattern phases
- [Paul Supabase Schema Design](paul-supabase-schema-design.md) - Migration sequence strategy

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

