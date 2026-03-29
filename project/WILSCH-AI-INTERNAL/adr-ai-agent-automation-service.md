---
publish: true
---

# ADR: AI Agent Automation Service
[[claude-code-sdk-agents]]

**Status:** Draft | **Date:** 2026-01-16 | **Author:** VeloxForce

## Executive Summary

**Problem:** Knowledge workers waste hours on repetitive admin tasks that require attention but not intelligence.

**Solution:** Small, autonomous AI agents that handle specific tasks permanently. One-time setup, client owns everything.

**Value Proposition:**
- Zero ongoing vendor lock-in
- Agent runs forever after setup
- Typical ROI: 3-6 months payback

## Problem Statement

### The Hidden Tax on Knowledge Work

Every business has tasks that are:
- **Repetitive** - Same process, different inputs
- **Attention-demanding** - Can't be ignored, but doesn't need expertise
- **Time-consuming** - Adds up to hours per week

Current "solutions" don't work.

**The sweet spot:** Tasks that are too small for a hire, too annoying to do yourself, and too custom for generic software.

## Solution: Autonomous Task Agents

Small AI agents built with Claude Code SDK that:
1. **Run autonomously** - No supervision required
2. **Handle edge cases** - LLM understands context, not just rules
3. **Integrate seamlessly** - Works with your existing tools (Gmail, Drive, GitHub, etc.)
4. **Belong to you** - Source code, no subscription, modify anytime

## How It Works

| Week | Activity |
|------|----------|
| Week 1 | Define task + requirements |
| Week 2 | Build + test agent |
| Week 3 | Deploy + handoff |
| → | Agent runs forever |

## Use Cases (Proven)

### 1. Meeting Transcript Processing

**Problem:** After each client call, someone needs to:
- Review 30-60 min transcript
- Extract action items
- Update project tracker
- Flag decisions for follow-up

**Time cost:** 15-20 min per meeting × 20 meetings/month = 5-7 hours/month

**Agent solution:**
- Fetches transcripts from Fireflies/Google Drive
- Parses for decisions, action items, mentions
- Creates/updates GitHub issues automatically
- Moves processed files to DONE folder

**Delivered value:**
- 2-3 min per transcript (vs. 15-20 min manual)
- No meetings slip through cracks
- Searchable decision history

### 2. Expense Receipt Collection

**Problem:** Finance apps flag "missing receipts" but:
- Receipts are scattered (email, Drive, phone)
- Deadline pressure at month-end
- Missing receipts = taxable income risk

**Concrete example:** 13 expenses flagged → EUR 288 potential tax exposure

**Agent solution:**
- Searches Gmail/Drive for matching receipts
- Matches vendor + date + amount
- Forwards to accounting inbox (e.g., Xolo)
- Reports what's still missing

**Delivered value:**
- Month-end receipt hunt: 0 hours (was 2-3 hours)
- Complete audit trail
- No missed deductions

### 3. Email → Task Sync

**Problem:** Client requests arrive via email. Manual process:
- Read email
- Decide: new task or update existing?
- Create/update in project tracker
- Label email as processed

**Time cost:** 5-10 min per email × 10-15 emails/week = 1-2 hours/week

**Agent solution:**
- Scans Gmail for unprocessed client emails
- Matches to existing issues (client label, keywords)
- Creates new issues or adds comments
- Labels email as processed

**Delivered value:**
- Inbox zero for client emails
- Nothing falls through cracks
- Full email→issue traceability

## Delivery Model

### What You Get

- Working agent (source code yours)
- Documentation
- 2 weeks support included

### What's Optional (Add-on)

- Extended support
- Additional integrations
- Training

### Timeline

| Week | Activity |
|------|----------|
| Week 1 | Requirements + architecture decision |
| Week 2 | Build + internal testing |
| Week 3 | Client testing + handoff |

## Pricing

### One-Time Setup

Standard agent pricing with no recurring fees.

### Why This Model

- **No subscription** - You pay once, agent runs forever
- **You own it** - Source code is yours, no lock-in
- **API costs** - You pay your own (typically EUR 5-20/month)
- **Optional support** - Add if you need ongoing changes

### ROI Example

| Item | Value |
|------|-------|
| Standard agent | EUR 5,000 |
| Time saved | 6 hours/month |
| Hourly value | EUR 75 (loaded cost) |
| Monthly savings | EUR 450 |
| Payback | 11 months |
| Year 2+ | Pure savings |

## Why Us

**David** - Dedicated builder, iterates fast, gets agents working in days not months

**Marius** - Supervises quality, ensures agents handle edge cases correctly

**Track record:**
- 4+ agents in production
- Running autonomously for months
- Combined savings: 10+ hours/week

## Decision Criteria

### This service is right for you if:
- [ ] You have a specific task that takes 2+ hours/week
- [ ] The task is repetitive but requires some judgment
- [ ] You want to own the solution, not rent it
- [ ] You're comfortable with one-time investment vs. monthly fees

### This service is NOT for you if:
- [ ] You need enterprise-grade SLA
- [ ] Task requires real-time human oversight
- [ ] You prefer vendor-managed solutions

## Next Steps

1. **Discovery call (30 min)** - Define the task, assess feasibility
2. **Proposal** - Scope, timeline, fixed price
3. **Decision** - Go/no-go
4. **Build** - 2-3 weeks to working agent

## FAQ

**Q: What if the agent breaks?**
A: You get 2 weeks support included. After that, you can extend support or fix it yourself (you have the source code).

**Q: What about API costs?**
A: Agents use Claude API. Typical cost: EUR 5-20/month depending on volume. You pay this directly to Anthropic.

**Q: Can I modify the agent later?**
A: Yes. You own the source code. Any developer can modify it.

**Q: What if my requirements change?**
A: Minor changes during build are included. Major scope changes are quoted separately.

---

*VeloxForce - Bespoke AI Development*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

