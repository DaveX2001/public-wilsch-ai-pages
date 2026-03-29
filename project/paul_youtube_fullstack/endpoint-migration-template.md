---
publish: true
---

# Endpoint Migration Template
[[client-paul]]

## Why This Exists

Issue #122 (Backend Rebuild) was closed as "not planned" - scope was too broad. This template enables incremental migration planning by categorizing Flask endpoints into:
- **Supabase (native)** - Code deletion, let Supabase handle it
- **FastAPI (debuggable)** - Keep as visible, maintainable Python code

## The Heuristic

> **Native Supabase = Supabase, Workaround = FastAPI**

### Native Supabase (use Supabase)
- Pure CRUD on single table with simple filtering
- Simple RLS: `user_id = auth.uid()` or role-based
- File upload/download via Supabase Storage SDK
- Pagination, search via PostgREST query params
- Joins via PostgREST embedded resources

### Workaround (use FastAPI)
- Multi-table validation requiring `security_definer` functions
- Complex orchestration across tables
- Database triggers + Edge Functions for coordination
- Compute operations (PDF parsing, text extraction)
- Business logic that would be hard to debug in SQL

**The principle:** If something feels like a workaround in Supabase (complex SQL functions, Edge Function error handling), put it in FastAPI where you can see and debug the code.

## Template Format

```markdown
# [Module] Migration Plan
**Heuristic:** Native Supabase = Supabase, Workaround = FastAPI

## Summary
- **Supabase (native):** X endpoints
- **FastAPI (debuggable):** X endpoints
- **Code deletion:** X%

## Endpoint Categorization

| Endpoint | Target | Why | Native/Workaround |
|----------|--------|-----|-------------------|
| `GET /path` | Supabase | Simple read with RLS | Native |
| `POST /complex` | FastAPI | Multi-table orchestration | Workaround |

## Supabase Configuration Needed
- RLS policies
- Storage buckets
- Views (only if truly native)

## FastAPI Services Needed
- ServiceName - Purpose
- Why it's better than Supabase workaround

## Migration Order
1. Phase 1: [low risk items]
2. Phase 2: [medium risk items]
3. Phase 3: [cleanup]
```

## Subagent Prompt

Use this prompt with `Task(subagent_type="general-purpose", model="sonnet")`:

```
**Analyze [Module] for Supabase migration with "Native vs Workaround" heuristic**

**STEP 1: Read architecture guidelines**
# Backend architecture (FastAPI + Supabase patterns)
gh api repos/veloxforce/velox-global-adrs/contents/README-FAST-API-BACKEND.md -H "Accept: application/vnd.github.raw"

# Frontend architecture (React + Supabase SDK patterns)
gh api repos/veloxforce/velox-global-adrs/contents/README-REACT-FRONTEND.md -H "Accept: application/vnd.github.raw"

**STEP 2: Read endpoint code**
- [path to endpoint file]

**STEP 3: Apply the heuristic**

**Native Supabase (use Supabase):**
- Pure CRUD on single table with simple filtering
- Simple RLS: user_id = auth.uid() or role-based
- File upload/download via Supabase Storage SDK
- Pagination, search via PostgREST query params

**Workaround (use FastAPI):**
- Multi-table validation requiring security_definer functions
- Complex orchestration across tables
- Database triggers + Edge Functions for coordination
- Compute operations (PDF parsing, text extraction)

**STEP 4: Create categorization document**

Save to /tmp/[module]-migration.md with:
- Summary (counts, code deletion %)
- Endpoint table with Target/Why/Native-or-Workaround
- Supabase config needed
- FastAPI services needed
- Migration order

Be concise. If something COULD be Supabase but feels like a workaround, recommend FastAPI.
```

## Related

- [Strangler Pattern Sequencing](adr-strangler-pattern-sequencing.md) - Migration order strategy
- [Pipeline Orchestration Design](pipeline-orchestration-design.md) - FastAPI layer design
- [Supabase Schema Design](paul-supabase-schema-design.md) - Database migration

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

