---
publish: true
---

# Mohamed Onboarding Reference
[[team-scaling]]

Quick reference for Mohamed during and after onboarding.

## 1. Daily Grooming

**What:** Data quality gate for issues - ensure correct assignee, blocked/ready status, context clarity.

**Doc:** [GROOMING.md](https://github.com/DaveX2001/deliverable-tracking/blob/main/GROOMING.md)

**Learning:** You'll learn by doing it daily with David.

---

## 2. System Access

| System | What you provide | Method |
|--------|-----------------|--------|
| GitHub | Email/username | Invite to repos |
| Supabase | Email | Org invite |
| SSH | Public key | Added to servers |
| Claude Code | (shared setup) | Live during kickoff |

### Plugin Installation

After Claude Code is set up, install the team plugin:

```bash
# Step 1: Add marketplace
claude plugin marketplace add MariusWilsch/claude-code-team-plugin

# Step 2: Install plugin
claude plugin install claude-code-team-plugin@wilsch-ai-plugins
```

**Repo:** [MariusWilsch/claude-code-team-plugin](https://github.com/MariusWilsch/claude-code-team-plugin)

This gives you all the commands, hooks, and skills listed below.

### MCP Server Installation

Add the shared MCP tools server:

```bash
claude mcp add -s user -t http hand-picked-tools https://metamcp.iitr-cloud.de/metamcp/hand-picked-tools/mcp
```


**Learn more:** [Model Context Protocol (MCP)](https://www.anthropic.com/news/model-context-protocol)

---

## 3. Tools & Commands

### Slash Commands (you invoke)

| Command | Purpose |
|---------|---------|
| `/onboarding` | Start a work session |
| `/requirements-clarity` | Clarify WHAT to build |
| `/implementation-clarity` | Clarify HOW to build |
| `/evaluation-clarity` | Define success criteria |
| `/issue-comment` | Post standardized updates |
| `/rubber-duck` | Think through problems |
| `/flag-for-improvement` | Escape hatch when stuck |

### Reference Documentation

| Topic | Link |
|-------|------|
| Ship with Confidence | [ship-with-confidence](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ship-with-confidence) |
| Model Context Protocol | [MCP Announcement](https://www.anthropic.com/news/model-context-protocol) |
| Slash Commands | [Claude Code Docs](https://code.claude.com/docs/en/slash-commands) |
| CLAUDE.md | [Learning Doc](https://docs.google.com/document/d/1U4x9o_oAfLBeQqKCUK_odQAA3D6SZSVA5A6VU-B0X2k/edit?tab=t.0#heading=h.vm57fatrx2rm) |
| GitHub Board SOP | [SOP Doc](https://docs.google.com/document/d/1h_BoV1HW9i4wkixyEHY26UsoNRH8qZomtOJPQhPKJCA/edit?usp=sharing) |
| AC/DoD Framework | [ac-dod-framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/ac-dod-framework) |
| Flag-for-Improvement | [improve-system-architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture) |

### Command Files (for reference)

- [onboarding.md](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/onboarding.md)
- [requirements-clarity.md](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/requirements-clarity.md)
- [implementation-clarity.md](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/implementation-clarity.md)
- [evaluation-clarity.md](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/evaluation-clarity.md)
- [rubber-duck.md](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/rubber-duck.md)
- [flag-for-improvement.md](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/flag-for-improvement.md)
- [issue-comment.md](https://github.com/MariusWilsch/claude-code-team-plugin/blob/main/plugins/claude-code-team-plugin/commands/issue-comment.md)

---

## 4. Explain Once Session

**Format:** Live screen share, recorded for rewatching

### Part 1: Concepts (~45 min)
- Navigator/driver mental model
- Clarity workflow (requirements → implementation → evaluation)
- Skills and commands overview
- AC/DoD framework

### Part 2: Walkthrough (~45-90 min)
- Using issue #508 as live example
- See tracking.md structure
- Run /onboarding, clarity phases, execute, commit

---

## 5. Support Model (First 2 Weeks)

### Communication Channels

| Channel | Use for |
|---------|---------|
| WhatsApp group | Daily updates, async questions |
| WhatsApp call | Urgent blocks - **call, don't text and wait** |
| `/flag-for-improvement` | System issues to fix later |

### Escalation Rule

> **Stuck for 15+ minutes?**
>
> Tried: (1) reading the error, (2) searching codebase, (3) asking Claude?
>
> **→ CALL MARIUS**

### Mindset

> "I EXPECT you to call. Not calling when stuck is worse than calling too much."

Better to interrupt than be stuck in silence.

---

## Related

- [Mohamed Onboarding Plan](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/mohamed-onboarding-plan) (full plan for Marius)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

