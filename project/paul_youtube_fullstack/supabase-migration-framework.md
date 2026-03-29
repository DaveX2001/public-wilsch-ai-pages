---
publish: true
---

# Supabase Migration Framework
[[client-paul]]

Vertical slicing approach for migrating Flask → Supabase/FastAPI.

## Core Principles

### Vertical Slicing (Not Horizontal)

**Migrate by feature, not by layer.**

| Approach | Example | Risk |
|----------|---------|------|
| **Vertical (correct)** | Migrate "List Scripts" end-to-end | Low - feature works or doesn't |
| **Horizontal (wrong)** | Migrate all controllers, then all services | High - broken intermediate states |

Each vertical slice is a complete request path: Frontend → Backend → Database.

### Strangler Fig Pattern

Run old + new systems in parallel via routing:

```
/api/v1/* → Flask (legacy)
/api/v2/* → FastAPI/Supabase (new)
```

Migrate features one at a time. Old system eventually has zero traffic → delete.

**Lesson learned:** Auth migration (#230) done too early broke integration testing. Auth should be LAST, not first.

## Chunking Approach

```
Spike (per frontend) → Sub-issues → Implement (simplest first)
```

### Spike Output

For each frontend being migrated, spike produces:

1. **Feature list with hardness ranking**

   | Factor | Easy | Hard |
   |--------|------|------|
   | Dependencies | Feature works alone | Depends on other features |
   | Auth | Public or simple | Complex role checks |
   | External APIs | None | YouTube, OpenAI, RunPod |
   | Filesystem | None | Reads/writes files |
   | State | Stateless read | Stateful workflows |

2. **Migration approach per feature**
   - Database: Dual-write pattern (old + Supabase)
   - Backend: v1/v2 coexistence
   - Frontend: Which component changes
   - Validation: How to confirm it works

3. **Feature categorization**
   - **Supabase-native:** Frontend + DB only, delete backend
   - **FastAPI-required:** Needs backend (external APIs)
   - **Mixed:** Decide per case - keep as one or split

4. **Sub-issues** for each feature migration

### Implementation Sequence (Per Feature)

```
Database → Backend → Frontend → Validate → Next Feature
```

**For Supabase-native features:**
1. Database: Create Supabase table + RLS policies
2. Frontend: Rewrite `fetch('/api/...')` → `supabase.from('...')`
3. Backend: DELETE (no FastAPI equivalent needed)

**For FastAPI-required features:**
1. Database: Supabase table if needed
2. Backend: Build FastAPI `/api/v2` endpoint
3. Frontend: Rewrite to call v2
4. Validate: E2E test
5. Deprecate: Remove Flask `/api/v1` when zero traffic

**For mixed features:**
- Decide during spike: keep as one feature or split
- Example: "Upload Thumbnail" = Storage (Supabase) + YouTube (FastAPI)

## Dual-Write Pattern

During migration, write to both systems:

```python
# Phase 1: Dual write
def create_script(data):
    old_db.insert(data)  # Keep old system working
    supabase.table('scripts').insert(data)  # Populate new system

# Phase 2: Dual read (validate)
def get_script(id):
    old_result = old_db.query(id)
    new_result = supabase.table('scripts').select().eq('id', id).execute()
    assert old_result == new_result  # Validate consistency
    return old_result  # Still use old system

# Phase 3: Switch reads
def get_script(id):
    return supabase.table('scripts').select().eq('id', id).execute()
```

## JIT Issue Creation

**Don't create all issues upfront.** They change and become stale.

- Framework lives in this doc
- Create spike issue only when starting a frontend migration
- Create feature sub-issues from spike output
- Next spike only when previous frontend complete

## Related Docs

- [Strangler Pattern ADR](adr-strangler-pattern-sequencing.md) - Lesson learned
- [Main Flask Migration](main-flask-migration.md) - Endpoint inventory
- [Template Backend Migration](template-backend-migration.md) - Express analysis

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

