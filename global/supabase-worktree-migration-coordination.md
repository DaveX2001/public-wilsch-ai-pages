---
publish: true
---

[[supabase]]

# Supabase Worktree Migration Coordination

Design proposal for coordinating Supabase migrations across parallel git worktrees.

## Problem Statement

Parallel worktrees push migrations to shared remote, causing:
- Out-of-order migrations in history table
- Migration files scattered across worktrees (never consolidated to main)
- Silent RPC failures (migration recorded but functions not created)
- No single source of truth for migration state

**Root Cause:** Supabase uses timestamp-based migration tracking. Parallel migrations assume different base states. When worktrees push independently, migrations get applied in push order, not timestamp order, and each migration assumes a schema state that may not match reality.

## Solution Architecture

### Core Principles

| Principle | Description |
|-----------|-------------|
| **Schema files are source of truth** | Edit `supabase/schemas/*.sql`, not migration files directly |
| **Local Supabase for testing** | Shared local instance from main repo |
| **Main-only push to remote** | Only main branch runs `db diff` and `db push` |
| **Migrations regenerated on main** | Worktree migrations are throwaway test artifacts |

### Skill Architecture

| Skill | Type | Responsibility |
|-------|------|----------------|
| **Supabase workflow skill** | Knowledge | Commands, workflow patterns, introspection, merge ceremony |
| **Worktree skill** | Setup/Action | Creates worktree, installs hooks, starts local Supabase |

### Local Development Setup

**One shared local Supabase from main repo:**

```bash
# Main repo (always running):
supabase start  # Ports 54321/54322/54323

# All worktrees connect to same instance:
# - API: http://localhost:54321
# - Postgres: postgresql://postgres:postgres@localhost:54322/postgres
# - Studio: http://localhost:54323
```

**Benefits:**
- No port management
- One MCP config for local introspection
- Worktrees share local DB (ephemeral, resettable)

### Migration Protection

**Pre-commit hook (installed by worktree skill):**

```bash
#!/bin/bash
branch=$(git branch --show-current)
if [[ "$branch" != "main" ]] && \
   git diff --cached --name-only | grep -q "supabase/migrations/"; then
  echo "ERROR: Don't commit migrations from worktrees"
  exit 1
fi
```

**Why hook, not gitignore:**
- .gitignore is committed → would affect main too
- Hook is branch-aware → blocks worktrees only
- No naming convention required

### Developer Workflow

```
1. Create worktree (skill auto-installs hook, starts local)
2. Edit schema files (supabase/schemas/*.sql)
3. supabase db diff -f local_test → local migration for testing
4. supabase db push → pushes to LOCAL (not remote)
5. Test feature against local Supabase
6. git commit → schema files only (hook blocks migrations)
7. Merge to main → /merge command handles rest
```

### Merge Ceremony (Phase 5b in /merge command)

```bash
# After squash merge to main:
supabase db pull           # Sync from remote (catch external changes)
supabase db diff -f <name> # Generate fresh migration
# Review migration
supabase db push           # Apply to remote
git add supabase/migrations/ && git commit
```

**Key insight:** Migration is generated AFTER merge, against CURRENT remote state. Schema files travel via git, migrations are regenerated fresh.

## Implementation Checklist

| Artifact | Action |
|----------|--------|
| `~/.claude/skills/supabase-workflow/SKILL.md` | CREATE - All Supabase knowledge |
| `~/.claude/skills/worktree/SKILL.md` | UPDATE - Add Supabase start + hook installation |
| `~/.claude/commands/merge.md` | UPDATE - Add Phase 5b for Supabase handling |
| `~/.claude/CLAUDE.md` | UPDATE - Remove protocol, add skill pointer |

## Open Questions

- **Seed data strategy:** Synthetic vs anonymized dump vs skip? Essential for meaningful local testing but adds maintenance.
- **MCP for local:** Can configure MCP to connect to localhost:54321 for local introspection, but adds per-worktree config complexity.

## Related

- Issue: DaveX2001/claude-code-improvements#152
- Issue: DaveX2001/claude-code-improvements#202 (Auto-link Supabase CLI)
- Issue: DaveX2001/claude-code-improvements#203 (Supabase CLI error recovery)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

