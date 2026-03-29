---
publish: true
date: 2025-12-08
---

# AI Scenario Brainstorm Meeting Agenda
[[client-archibus]]

Meeting to decide the next scenarios for Bruce BEM AI Platform.

## Background

Last Friday (Dec 5), Rein and Marius discussed architecture for extending the AI assistant. Key conclusion: **scenarios inform architecture** - we need to decide which use cases to support next before determining the technical approach.

**Reference Documents:**
- [FM Assistant Extension Architecture](fm-assistant-extension-architecture) - North Star pattern (GROUP → ELEMENT → ACTION)
- [Bruce Platform Elements](bruce-platform-elements) - Available element groups

## Current State

| Capability | Status |
|------------|--------|
| Search assets | ✓ Working |
| Create work request | ✓ Working |
| Other groups (contracts, costs, notifications) | Not yet supported |

## Meeting Goal

Decide which scenario(s) to support next for the AI assistant.

## Agenda

| Time | Topic | Owner |
|------|-------|-------|
| 5 min | Current state: what works today | Marius |
| 5 min | Goal: expand to other groups (scenarios inform architecture) | Marius |
| 15 min | Open brainstorm: high-value scenarios | All |
| 5 min | Group consensus: pick next scenario(s) | All |

## Development Approach Note

Two possible paths:
1. **Meta-tools:** Build flexible search/create that works across all groups
2. **Scenario-specific:** Build features one scenario at a time

The scenarios we choose will inform which approach makes sense. Some scenarios may need more data complexity than others.

## Attendees

- Miguel (CEO)
- Rein (API Developer)
- Ian (Sales/Market perspective)
- Mujahid (Domain expert - FM operations)
- Ali (Domain expert - FM operations)
- Marius (AI Development)

## Proposed Times

- Wednesday afternoon
- Thursday afternoon

## Open Questions for Brainstorm

- Which element groups matter most to users? (Contracts? Costs? Notifications?)
- What pain points do Mujahid/Ali see in day-to-day FM operations?
- What would be most valuable for sales demos?

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

