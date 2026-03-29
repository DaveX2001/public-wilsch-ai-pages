---
publish: true
---

# Developer Position Beta — Plugin Scope & Delivery
[[traceline]]

Design decisions for shipping the Developer Position as a beta product. Scope: what goes in the plugin, how users interact, and architectural choices.

---

## Problem Statement

The Developer Operations Manual defines a complete workflow (Path A: Spec-Design, Path B: Spec-Implement with Implementation + Verification sessions). This workflow is implemented as Claude Code skills and commands built over months of internal use. Shipping it to 10 beta users requires determining which components are load-bearing for the experience versus personal customization, and how to package them without exposing internal infrastructure (GitHub as backend, tracking repos, context trickle automation).

**Preconditions:**
- Developer Operations Manual v3 exists and defines the target experience
- Plugin delivery is the only viable distribution method (Claude Code plugin system)
- GitHub Issues is the persistence layer (beta decision, not permanent)
- Beta audience is ~10 developers, not general public
- Claude Code's native plan mode conflicts with the system's own planning workflow

---

## Success Definition

- **Goal:** A beta user installs the plugin and completes Path A (spec-design) + Path B (spec-implement + verify) on their own project without Marius explaining infrastructure
- **Success:** Beta users run the workflow independently; friction points surface through flag-for-improvement, not through confusion
- **Done test:** Christoph builds and completes the tutorial without asking Marius for clarification → plugin is ready for first 1-2 users
- **Rollout cadence:** 2 users first (David Eberle, Dylan Brodeur) → 2-3 day observation → expand to 10. Purpose of the first 2: surface distribution quirks (GitHub setup, onboarding, packaging) with close contacts who give direct feedback. Expansion criterion: distribution quirks resolved (Marius judgment — the system is proven internally, distribution is the new variable). One-off seeding pattern.
- **Launch target:** Monday 2026-02-23

---

## Approach

### Part 1: Plugin File Inventory

The Developer Operations Manual is the inclusion filter. Every file that maps to a manual step is load-bearing; everything else is cut.

**Shared (all sessions):**

| Component | Type | Beta Status |
|-----------|------|-------------|
| `/onboarding` | Command | Ship (simplified) |
| `/issue-comment` | Command | Ship as-is |
| `/feedback` | Command | Ship (renamed from /flag-for-improvement, modified routing) |
| `deliverable-tracking` | Skill | Ship as-is |

**Path A: Spec-Design**

| Component | Type | Required? | Beta Status |
|-----------|------|-----------|-------------|
| `/rubber-duck` | Command | Required | Ship as-is |
| `ac-create` | Skill | Required | Ship (both Scout phases, Skip option) |

**Path B — Implementation Session:**

| Component | Type | Required? | Beta Status |
|-----------|------|-----------|-------------|
| `/rubber-duck` | Command | Optional | Ship as-is |
| `/requirements-clarity` | Command | Required | Ship (remove discovery script) |
| `/implementation-clarity` | Command | Required | Ship (keep discovery script — worktree) |
| `/evaluation-clarity` | Command | Required | Ship (remove discovery script) |
| `worktree` | Skill | Optional | Ship (ask in implementation-clarity) |

**Path B — Verification Session:**

| Component | Type | Required? | Beta Status |
|-----------|------|-----------|-------------|
| `/rubber-duck` | Command | Optional | Ship as-is |
| `/ac-verify` | Command | Required | Ship as-is |
| `/merge` | Command | Required | Ship as-is |

**Cut for beta:**
- Document templates (backend/frontend README fetch)
- Quick investigation mode in onboarding (keep Full + Skip only)
- Auto-forward between clarity stages
- Discovery script in requirements-clarity and evaluation-clarity
- Transcript agent, email agent, design doc link agent (context trickle)
- Hippocampus knowledge management
- Shutdown ritual, create-contract, emyth, create-pflichtenheft

### Part 2: Onboarding Simplification

Beta onboarding keeps: session context bootstrap (repo + project only), protocol understanding (sequential thinking), brief introduction, issue selection from GitHub, investigation agent.

**Investigation:** Ask with time estimate (~60s). Options: Full / Skip. Quick mode removed.

**Cut:** detected label validation, SSH hosts, GH extensions, CLIs, document template selection, collaborator display. Bootstrap reduced to: which repo, which project, which issues exist.

### Part 3: Confidence & Handoff Loop

**Rubber-duck confidence signal** lives in the skill itself (line 61, lines 67-71). Four exit options: Quick fix, Let's plan, Define ACs, Keep thinking. Ships as-is.

**AC Create Scout:** Both phases (Scout Agent + Investigator Agent) ship with existing Skip option. Beta feedback determines whether to cut later.

**AC Create Refinement Gate:** All 4 checks stay (Observable Output, Test Data, Design Questions, Blocking Dependencies).

### Part 4: Clarity Workflow

Three phases ship. No auto-forward — user drives timing.

Discovery script kept in `/implementation-clarity` only (worktree). Removed from `/requirements-clarity` (hook enforces deliverable-tracking) and `/evaluation-clarity` (discovers nothing).

### Part 5: Execute & Task Complete

**Worktree:** Optional. Asked in implementation-clarity. No worktree = no branch enforcement, no draft PR. User's responsibility.

**Task Complete protocol** extended:
1. Push to remote (evidence checkpoint)
2. Show commit hash link + issue link
3. AI proposes next step based on session work
4. AskUserQuestion — "Post as issue comment? Yes / No"
5. If yes → `/issue-comment` with proposed next step

**DoD update:** Task Complete updates DoD checkboxes after execution. Verified working — ships as-is.

### Part 6: GitHub Integration & Repo Architecture

**Principle:** The system works towards the user's tools, not ours. GitHub is the first adapter — architecture is task-manager-agnostic. Target systems need an API, HTTP endpoint, or CLI to integrate. Three-phase progression:
1. **Beta:** Shared plugin repo (our infrastructure). Users interact with GitHub Issues directly.
2. **Next:** Per-user GitHub repos (user-owned infrastructure).
3. **Future:** Any task manager via adapters (GitHub, Linear, Jira, etc.).

**Issue-commit-linker:** Ships for beta. Same-repo = GITHUB_TOKEN sufficient.

**Spec reading:** Local markdown files (tracking.md). GitHub Pages deferred.

**Repo model (resolved):** Single shared repo for beta. Per-user forks rejected as unnecessary complexity for 10 trusted users. Issue visibility is a known tradeoff: all beta users see all issues (including `/feedback` submissions). All 10 beta users are hand-picked, reducing IP risk. Per-user repos (phase 2) are a gating requirement for any IP-sensitive client — enterprise adoption is blocked until visibility isolation exists.

**Beta disclosure requirements:** Two disclosures before user starts:
1. Shared visibility — "Your issues are visible to all beta participants."
2. Conversation data — sessions are collected and feed system improvement (compounding knowledge mechanism).

**Undefined: Data collection & business model** — Conversation data is the improvement fuel. Monetization differential: subscription to curated best practices (system improves via collected data) vs. self-serve (user keeps data private). Legal mechanism for data collection consent undefined. → See Meeting Agenda

### Part 7: Feedback Architecture (Two Loops)

Traceline has two distinct feedback loops. They serve different purposes and should not be conflated.

**Loop 1 — Product/company (how we learn what to build):**
Structured check-ins with beta users produce transcripts. These transcripts become JA source material — the extraction pass system turns them into design doc updates, positioning refinements, and product decisions. This is how the company learns. Without the JA position, transcripts are just recordings. With the JA, they become structured product intelligence. Precedent: Lovable's weekly early-user meetings.

**Loop 2 — Tool (how the tool itself improves):**
`/feedback` (renamed from `/flag-for-improvement` — discoverability for beta users) captures in-session friction. Routes to CCI directly via GitHub Action workflow dispatch in plugin repo. PAT stored as repo secret. `github.actor` captures username. System Engineer diagnoses and fixes the underlying pattern. **Visibility boundary:** beta users see a thank-you confirmation but do not see the CCI board. GitHub App (Traceline Bot) deferred.

Loop 1 is strategic (what to build). Loop 2 is operational (how the tool behaves). Both compound, but through different mechanisms.

**Community layer:** Discord channel for async communication — escape valve for when `/feedback` itself fails or for high-level overall impressions. Structure owned by Christoph.

**Undefined: Feedback resolution notification** — Should users be notified when their feedback is resolved? → See Meeting Agenda
**Undefined: Check-in cadence** — Format and frequency of structured feedback meetings for beta users (2 then 10). Check-in transcripts are the highest-value input for the JA extraction system. → See Meeting Agenda

### Part 8: Plan Mode Prevention

Claude Code's native plan mode (read-only, triggered via Shift+Tab or EnterPlanMode tool) conflicts with the system's clarity workflow. Three layers prevent accidental activation:

| Layer | Mechanism | Scope |
|-------|-----------|-------|
| Plugin `hooks.json` | PreToolUse deny on `EnterPlanMode` | Blocks AI from entering plan mode |
| Setup script | `permissions.defaultMode: "default"` in settings.json | Session starts in normal mode |
| Plugin `CLAUDE.md` | "Use clarity workflow, not native plan mode" | Instruction reinforcement |

User-side gap: Shift+Tab still cycles through plan mode (no technical block available). README documents: "stay in normal or auto-accept mode."

### Part 9: MCP & Tool Inventory

Four MCP servers ship bundled in the plugin via `.mcp.json`. All are universal — every user benefits regardless of stack.

| MCP | Purpose | Used By |
|-----|---------|---------|
| Sequential Thinking | Structured reasoning | Cross-cutting (onboarding, rubber-duck, clarity) |
| Chrome DevTools | UI verification | AC Verify (UI layer) |
| Context7 | Library documentation lookup | Cross-cutting |
| Read Website Fast | Web content access | Cross-cutting |

**Cut:** Fireflies (personal infrastructure), Supabase (personal database).

**AC Verify routing** determines which tool verifies each acceptance criterion:

| Verification Layer | Tool | Ships With Plugin? |
|-------------------|------|--------------------|
| UI | Chrome DevTools MCP | Yes |
| API | curl / bash | Yes (built-in) |
| Database | User's database MCP | No — stack-dependent |

Database verification is the only stack-dependent element. README provides guidance: "AC Verify routes database checks through your database MCP" with links to MongoDB, Postgres, Supabase, and MySQL MCP servers. Users configure their own.

**Delivery:** MCPs bundled directly in the plugin (not via meta-MCP layer). Experimental MCP CLI flag stays enabled — removal untested.

### Part 10: Settings & Environment Configuration

The plugin delivery splits between what the plugin handles natively and what a setup script configures.

**Plugin native (no user action):**

| Component | Plugin File | Purpose |
|-----------|------------|---------|
| Hooks | `hooks.json` | PreToolUse deny on EnterPlanMode |
| MCP servers | `.mcp.json` | 4 bundled MCPs |
| LSP config | `.lsp.json` | Language server connections (TypeScript, Python) |
| Skills | `skills/` | Workflow skills (ac-create, worktree, etc.) |
| Commands | `commands/` | Onboarding, clarity phases, etc. |
| Instructions | `CLAUDE.md` | Protocol, workflow rules |

**Setup script (runs once at install):**

The script handles three categories: settings, protocol, and dependencies.

*Settings:* Writes `settings.json` with `env.CLAUDE_CODE_DISABLE_AUTO_MEMORY`, `permissions.defaultMode`, empty allow-list.

*Protocol:* Copies the CLAUDE.md protocol to the user's project. The plugin bundles CLAUDE.md for plugin-level instructions, but the setup script ensures the project-level protocol is in place.

*Dependencies:*

| Dependency | Install Method | Required For |
|-----------|---------------|-------------|
| Claude Code (pinned version) | `claude update` or native installer | Core runtime |
| LSP binaries | `npm install -g typescript-language-server` | Back pressure loop (diagnostics) |
| LSP plugins | `claude plugin install typescript-lsp@...` | LSP configuration |
| gh (GitHub CLI) | `brew install gh` / system package | Issue integration, commit linker |
| uv | `curl -LsSf https://astral.sh/uv/install.sh` | Bootstrap scripts |
| GTA (opt-in) | Install if user enables worktrees | Git worktree automation |
| Chrome DevTools | MCP server binary | UI verification in AC Verify |

Draft inventory — validated with Christoph during tutorial building.

**Onboarding wizard (resolved):** Three-stage flow from "I'm in" to first real session:
1. **Entry:** `curl https://traceline.dev/install | bash` (or equivalent hosted URL)
2. **Setup script:** Minimal prereq check (Claude Code installed?), then walks through what's being installed — transparency and consent before action. Installs plugin + dependencies from the table above.
3. **Tutorial:** Opens `--sdk-url` scripted walkthrough in browser — guides Path A (spec-design) and Path B (spec-implement) using a fake issue.

Beta-level checks only — things change fast, don't over-engineer the install script.

**Idea (future):** `--sdk-url` could script the ENTIRE wizard (install + tutorial as one WebSocket-driven flow). Requires Christoph discussion.

**Release pipeline:** Alpha (Marius daily use) → `release-plugin` skill → beta distribution plugin repo (TBD — not the internal Claude Team Marketplace). `clarity-workflow-plugin` repo archived.

**Undefined: Beta distribution repo** — The internal Claude Team Marketplace (`MariusWilsch/claude-code-team-marketplace`) is for internal use only. A separate repo is needed for beta user distribution. → See Meeting Agenda

**Client-side maintenance:** AI behavior maintenance is solved internally via the [improve-system architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) (3-session workflow: capture → diagnose → verify). The open question is making Loop 2 work remotely — which depends on getting conversation data off the client's machine. See Part 7 undefined marker.

*Settings shipped:*

| Setting | Delivery | Rationale |
|---------|----------|-----------|
| Disable auto memory | `env` key in settings.json | Auto memory conflicts with protocol-driven workflow |
| Default permission mode | `permissions.defaultMode` | Session starts in normal mode, not plan mode |
| Allow-list | Empty (not pre-configured) | Users configure their own permissions |
| Auto-forward | Not shipped | Users should observe output before auto-approving |

**LSP:** Plugin configures language server connections via `.lsp.json`. Setup script installs binaries. LSP creates a back pressure loop: AI edits → language server catches errors instantly → AI fixes in the same turn. No compiler run needed.

**Version pinning:** Minimum version 2.1.32 (first to support Opus 4.6). Exact version pinned at launch after stability testing. Version pinning is a value proposition: "We test and determine the most stable version with the best prompt and feature compatibility."

### Part 11: Tutorial & README

**Tutorial:** The `--sdk-url` flag turns the Claude Code terminal into a WebSocket connection, powering a scripted walkthrough. The AI guides the user through Path A (spec-design) and Path B (spec-implement) using a fake issue. Dual purpose: Christoph learns the system while building the onboarding artifact. Litmus test: if Path B can't be scripted with a fake issue, the system isn't self-explanatory.

The tutorial is stage 3 of the onboarding wizard (see Part 10). Users arrive here after the install script has configured their environment. The tutorial is not standalone — it's the guided first experience after setup.

**Owner:** Christoph. "Lass mich erstmal das Tutorial bauen."

**README structure:**
1. **Vision** — what way of thinking we're selling (long-term, not product description — "it's a beta, describing it as a product would be stupid")
2. **Setup** — how to install and get running (setup script output)

**Undefined: Positioning language** — The concept is now articulated: "AI doesn't need more tech people. It needs organizational people." The system treats AI like an employee — position agreements, training (operations manual), standards, accountability. Developer position makes AI-assisted development deterministic (Issues → Code). Broader Traceline: any business function → consistent output via organizational standards. Problem frame: "rogue developer → organizational standards." The NAME is undefined ("Traceline" has reservations, no alternative locked). → See Meeting Agenda

---

## Source

- **Transcripts:**
  - [Developer Position Launch Deep Dive (2026-02-15)](https://app.fireflies.ai/view/01KHG1Y7QQD62BVT24755BR552)
  - [Beta Scope Review with Christoph & David (2026-02-17)](https://app.fireflies.ai/view/01KHN683Z4KYG27KBMV6H0NS8P)
- **Operations Manual:** [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services)
- **Meeting Agendas:**
  - [Product Vision & Launch (2026-02-14)](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/meeting-agenda-product-vision-launch-2026-02-14)
  - [Developer Launch Deep Dive (2026-02-15)](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/meeting-agenda-developer-launch-deep-dive-2026-02-15)
- **Sessions:**
  - `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/3cc5c4cd-ca43-45fc-962d-80081a8dd692.jsonl` (Parts 1-7)
  - `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/3780c35e-84ce-4e34-a868-d5113ff12389.jsonl` (Parts 8-11)
  - `/Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/8e10a9bd-c494-4e3f-9d7b-58a780999d25.jsonl` (Extraction pass: resolved 3 undefined markers, enriched positioning concept, added release pipeline + onboarding wizard)
- **Beta User List:** [Traceline Beta User List](https://docs.google.com/spreadsheets/d/1NL6ZeNIE67t7jE9JJfwQXlneN4UYrcASvlNVcIu5n68/edit)
- **Reference:** [Sourcegraph: Revenge of the Junior Developer](https://sourcegraph.com/blog/revenge-of-the-junior-developer)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

