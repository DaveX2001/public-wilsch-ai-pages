---
publish: true
---
# ADR: Adopt Maker/Manager Schedule Separation
[[team-scaling]]

## Status

Accepted (2025-12-09)

## Context

As a solo freelancer scaling to work with collaborators (David, future interns), I noticed a productivity problem:

**Before:** Simple notebook → looked at tasks → worked in large chunks → finished. Starting was easy.

**After:** GitHub Projects board → full visibility of all work → constant context switching → struggling to enter deep work mode. Starting became hard.

The board provides transparency and coordination benefits but eliminates the "blinders" that enabled focus. Every glance at the board triggers manager-mode thinking (what's next? what's blocked? what's the priority?) instead of maker-mode execution.

## Decision

**Adopt a two-interface system that separates coordination from execution:**

### 1. Board = Manager Interface
- Full visibility for coordination with David
- Used only during designated manager time blocks
- Handles: grooming, reviews, emails, unblocking

### 2. Book = Maker Interface
- Physical notebook with issue numbers only
- Maximum 3 items per day
- Board stays CLOSED during maker time
- Provides the "blinders effect" that enables deep work

### 3. Schedule Structure

| Time | Mode | Activity |
|------|------|----------|
| 8-12 | **Maker** | Work from book, board CLOSED |
| 12-1 | **Manager** | Grooming, meetings, admin |
| 1-5 | **Maker** | Work from book, board CLOSED |
| 5-7 | **Manager** | Meetings, admin |
| Evening | **Planning** | Select tomorrow's maker items |

### 4. Task Classification

During evening selection, apply the maker question:
> "Can I spend half a day on this AND do I have all context to execute without looking elsewhere?"

- **YES** → Write in book (maker time)
- **NO** → Stays on board (manager time)

## Research Basis

| Finding | Source |
|---------|--------|
| Kanban boards trigger manager-mode thinking | Visual structure optimizes for workflow management, not task execution |
| 23 minutes to refocus after interruption | Gloria Mark (UC Irvine) |
| Half-day minimum for maker time | Paul Graham ("Maker's Schedule, Manager's Schedule") |
| 4 hours max deep work per day | Cal Newport |
| Physical constraints force prioritization | Index card methodology (Newport, Andreessen) |

## Consequences

**Positive:**
- 8 hours protected maker time per day (two 4-hour blocks)
- Meetings contained in manager windows (never split maker time)
- Clear mental separation between coordination and execution
- Scalable: same system works for interns (they get pure maker time, manager handles coordination)

**Negative:**
- Requires evening planning ritual (new habit)
- Board provides less real-time visibility during maker hours
- Two systems to maintain (board + book)

## Implementation

- **GROOMING.md**: Updated with assignment step ("Who does this?")
- **SHUTDOWN-RITUAL.md**: Evening selection ceremony documented
- **Daily practice**: Evening selection → Morning maker → Manager block → Afternoon maker

## Related

- [Maker's Schedule, Manager's Schedule](https://paulgraham.com/makersschedule.html) - Paul Graham (2009)
- [WIP-Limited Kanban ADR](./adr-adopt-wip-limited-kanban-board-for-deliverable-tracking.md) - Board system design
- [Cal Newport's Shutdown Ritual](https://calnewport.com/drastically-reduce-stress-with-a-work-shutdown-ritual/) - Evening planning concept

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

