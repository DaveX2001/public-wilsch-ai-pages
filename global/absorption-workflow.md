---
publish: true
---

# Frontend Absorption Workflow
[[development-standards]]

## Why

Micro-frontends accumulate during rapid development. Consolidation reduces maintenance burden, eliminates duplicate code, and creates unified UX. This workflow documents the method for absorbing a standalone frontend app into a main application.

## The Method

### Phase 1: Document First

Before touching code, capture the feature in hippocampus:
- Feature overview (what it does, why it exists)
- User workflows (admin vs user perspectives)
- API endpoints (backend dependencies)
- Data model (tables, relationships)
- Implementation status

**Why document first:** Creates the "before picture" for validation. Future AI sessions can reference this to understand expected behavior.

### Phase 2: Absorb Components

Rebuild using Main's patterns, not copy-paste:
- Use Main's component library (e.g., shadcn/ui)
- Use Main's styling system (e.g., Tailwind)
- Use Main's state management patterns
- Create feature folder: `features/{feature-name}/components/`

**Key insight:** Absorption is a rebuild with functionality parity, not a migration of old code.

### Phase 3: Integrate Routing

- Add route to Main's router
- Update navigation (change external links to internal `<Link>`)
- Ensure auth/role gating works with Main's auth system

### Phase 4: Validate with Browser Automation

Side-by-side testing using Chrome MCP:
1. Run original on its port (e.g., 3003)
2. Run absorbed in Main on its port (e.g., 5173)
3. Automate test scenarios:
   - Login flow
   - CRUD operations
   - Status changes
   - File uploads
4. Take screenshots for visual comparison
5. Check console for errors

**Pattern:** `snapshot → fill_form → click → verify` cycle

### Phase 5: Iterate on Gaps

Expect to discover missing pieces during validation:
- Missing backend endpoints
- Missing environment config
- Missing UI elements (role badges, etc.)
- Styling differences (intentional vs bugs)

**Cycle:** Validate → Discover gap → Fix → Re-validate

This is normal. Budget time for 2-3 discovery cycles.

## Strangler Pattern Context

From ADR: Sequence absorption as additive changes, save breaking changes for last.

```
Absorption sequence:
App1 → test → ✓
App2 → test → ✓
App3 → test → ✓
────────────────
Single frontend achieved (old auth intact)
────────────────
Auth migration (FINAL STEP - breaking change)
```

**Why this order:** Each absorption keeps the system functional. Auth migration affects all apps simultaneously - do it once, at the end, when all apps are consolidated.

## Checklist

- [ ] Feature documented in hippocampus
- [ ] Components rebuilt with Main's patterns
- [ ] Route added to Main's router
- [ ] Navigation updated (external → internal links)
- [ ] Browser automation validation passed
- [ ] Gap fixes committed
- [ ] Original port can be killed
- [ ] PR merged

## Example: Script-Writer Absorption (#226)

First proof-of-concept using this workflow:

| Phase | Outcome |
|-------|---------|
| 1. Document | `hippocampus/project/paul_youtube_fullstack/script-writer.md` |
| 2. Absorb | `frontend/src/features/script-writer/` (ScriptWriterPage, ScriptManagement, MyAssignments, ScriptFiles) |
| 3. Integrate | Route `/script-writer` added to router.jsx |
| 4. Validate | Browser automation verified CRUD, role gating, file upload |
| 5. Iterate | 3 cycles (missing endpoints, role badges, code review fixes) |

**Cleanup:** Moved 7 legacy docs to `/legacy/` with `[ARCHIVED]` markers, deleted 78MB standalone frontend.

**Result:** Port 3003 can be killed. PR #6 merged.

## Related

- Issue #121: Frontend consolidation parent issue
- ADR: Strangler pattern sequencing
- Script-Writer feature doc: `hippocampus/project/paul_youtube_fullstack/script-writer.md`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

