---
publish: true
---

[[claude-code-architecture]]

# ADR: Adopt Git Worktrees with Cross-Repo Issue Linking for Parallel AI Development

## Status
**Proposed** | Date: 2025-12-01

## Context
- Multiple Claude sessions working on the same codebase can overwrite each other's changes
- Manual issue state management (to-do → in-progress → done) creates overhead and is error-prone
- Need self-managing systems that reduce manual coordination
- Parallel AI development requires file-level isolation, not just branch-level separation
- Issue tracking lives in a separate meta-repository from code repositories

## Decision
We will adopt git worktrees for parallel AI development with three architectural principles:

1. **Worktrees for Code Isolation**: Each coding task gets its own worktree directory, enabling multiple Claude sessions to work simultaneously without file conflicts

2. **Meta-Layer Issue Tracking**: Deliverable-tracking repository serves as a cross-project meta-layer for issues, separate from code repositories where work happens

3. **Self-Managing Issue Lifecycle**: GitHub closing keywords in PRs (`Closes owner/repo#X`) automatically transition issues to done on merge, eliminating manual state management

## Consequences

### ✅ Positive
- Parallel Claude sessions cannot destroy each other's work (directory-level isolation)
- Issue state transitions are self-managing via PR merge (no manual closing)
- Commits create instant audit trail via cross-repo references
- Clear separation: code repos have worktrees, tracking repo has issues only

### ❌ Negative
- Requires worktree tooling installation and configuration
- Each worktree needs independent setup (dependencies, environment files)
- Only beneficial for coding tasks (non-coding tasks gain nothing)

### ⚪ Neutral
- Worktree skill discovered at implementation-clarity phase, executed at execute phase
- Filter: coding tasks trigger worktree workflow, non-coding tasks skip it
- Worktrees are local-only constructs (must recreate on each machine)

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Branch switching only** | File state not isolated; one session's uncommitted changes block another |
| **Separate repo clones** | Heavy duplication; git database not shared; sync complexity |
| **Manual issue management** | Overhead scales with task count; human error in state transitions |
| **Single-session workflow** | Eliminates parallelization benefit; bottleneck on one Claude instance |

---

**Key Resources:**
- Git worktree documentation: `git worktree --help`
- GitHub closing keywords: [Linking PRs to issues](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue)
- Related: `skill-lifecycle-execution-timing.md` (when worktree skill executes)
- Related: `git-worktree-missing-after-push.md` (worktree troubleshooting)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

