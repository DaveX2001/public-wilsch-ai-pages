---
publish: true
---

# Beta Launch — Final Scope Decisions
[[traceline]]

## Meeting Goal

Resolve the remaining undefined markers from the design doc so plugin cleanup and tutorial building can proceed with locked scope.

1. **Repo model locked** — shared plugin repo vs per-user forks decided
2. **Rollout plan agreed** — criteria for 1-2 → 10 user expansion
3. **Feedback mechanism decided** — voice notes: must-have or defer
4. **Positioning language locked** — what word describes what we're selling
5. **Availability schedule agreed** — fixed meeting cadence
6. **Script trigger decided** — how users discover and run the setup script

## Pre-Read

- [Developer Position Beta — Plugin Scope & Delivery](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope) (design doc, Parts 1-11)
- [Developer Operations Manual v3](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/developer-operations-manual-wilsch-ai-services)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Where beta user issues live determines everything downstream
⏱️ 10 min

GitHub is backend-only — users never see it directly. But every system component (commit linker, onboarding, issue-comment) needs a target repo. For beta, users WILL interact with GitHub Issues (reading specs, tracking files).

- Shared plugin repo: all beta users' issues visible to each other and us
- Per-user forks: independent, more setup, users don't see each other
- Long-term: CLI + own UI abstracts GitHub away entirely
- Core tension: support visibility vs user privacy

**To resolve:** The repo model that balances support visibility against user privacy for 10 beta developers.

### 2. The expansion criteria from 2 to 10 users aren't defined
⏱️ 10 min

Start with 1-2, trickle to 10. The trigger for expansion affects how much polish the first version needs. Beta user list created: [Traceline Beta User List](https://docs.google.com/spreadsheets/d/1NL6ZeNIE67t7jE9JJfwQXlneN4UYrcASvlNVcIu5n68/edit) (schema: First Name, Last Name, Email, GitHub Username).

- Time-based (expand after 1 week) vs signal-based (first 2 complete a full cycle) vs feeling-based
- Who are the first 2: Christoph + one external, or two externals?
- Beta user selection criteria: developers only? Claude Code experience required?

**To resolve:** The expansion trigger and selection criteria that determine when and who joins the beta.

### 3. Voice note feedback may not be needed for the first 2 users
⏱️ 5 min

Text-based flag-for-improvement ships. Voice notes add friction reduction but require infrastructure (WhatsApp, Telegram, or Fireflies).

- The first 2 users are close collaborators — text may be enough
- Building voice infrastructure before knowing if 2 users even need it might be premature

**To resolve:** Whether voice note feedback is a launch requirement or a week-2 addition.

### 4. We still don't have a word for what we're selling
⏱️ 15 min

The README needs a vision section — "what way of thinking we're selling." Current candidates don't land: "assistant" (no differentiation), "interaction patterns" (too academic), "behavioral scripts" (too clinical). The actual differentiators are tweakability and compounding knowledge, but the framing isn't locked.

- Intent (Augment Code) executes FOR you. Traceline executes WITH you — you learn
- The system bends to your workflow, not the other way around
- Each markdown artifact goes through iteration until deterministic — the output looks simple, the process that produced it isn't

**To resolve:** The positioning language that conveys both simplicity and the evolutionary refinement cycle for the README vision section.

### 5. Availability commitment needs a fixed schedule
⏱️ 5 min

The Feb 15 transcript mentioned "12-14 Uhr German time, not every day." Beta users and tutorial building need predictable availability.

- Fixed days vs flexible with minimum commitment
- Sync meetings vs async-first with scheduled check-ins

**To resolve:** A fixed weekly schedule that supports both tutorial building and beta user onboarding.

### 6. The setup script exists but users don't know how to find it
⏱️ 10 min

The setup script handles settings.json, CLAUDE.md protocol copy, dependencies (gh, uv, LSP binaries, Claude Code version pin). The script's contents are defined (Part 10 of design doc) but the trigger mechanism isn't.

- Plugin install triggers it automatically (postinstall hook)?
- README links to a URL the user runs manually?
- Christoph's tutorial includes it as step 1?

**To resolve:** The mechanism by which a beta user discovers and executes the setup script after plugin install.

## Meeting Format

- **Type:** Working session
- **Expectation:** Christoph has read the updated design doc (Parts 1-11)
- **Outcome:** All 6 undefined markers resolved → design doc updated → scope locked for implementation

## Related

- **Issue:** [CCI#504: Cloud Code Operating Launch](https://github.com/DaveX2001/claude-code-improvements/issues/504)
- **Design Doc:** [Developer Position Beta — Plugin Scope & Delivery](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/developer-position-beta-scope)
- **Previous Agenda:** [Beta Launch Scope (2026-02-16)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/traceline/meeting-agenda-beta-launch-scope-2026-02-16)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

