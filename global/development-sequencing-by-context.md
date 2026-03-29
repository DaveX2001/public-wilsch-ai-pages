# Development Sequencing: When to Go Schema-First vs UI-First
[[development-standards]]

---
publish: true
date: 2026-01-07
type: pattern
learned-from: issue-449-fdw-schema-alignment
---

## The Lesson

Development sequencing depends on context. The approach that works for greenfield features fails for integration work.

## Decision Framework

| Context | Approach | Rationale |
|---------|----------|-----------|
| **Greenfield feature** | UI-first + local state | Fast iteration, see results immediately |
| **Integration/migration** | Schema-first | Existing system IS the contract |
| **API-driven** | Contract-first (types) | Enables parallel frontend/backend work |

## The Pain Point That Taught This

Building v2 frontend with column names (`audio_url`, `name`, `rule_3600_plus`) that differed from v1 schema (`path_to_voice`, `title`, `rule_600_plus`).

Result: ~20 frontend files needed rework when schema alignment was finally done.

If schema alignment happened FIRST, frontend would have used correct names from day one.

## Rule of Thumb

> **New features:** UI-first is fine - you control the schema.
> **Integration work:** Schema-first is mandatory - the existing system controls the contract.

## Signals: When to Go Schema-First

- Integrating with existing system (v1/v2 migration, third-party APIs)
- Migration work where data has existing shape
- Multiple consumers of same data
- Any work involving FDW, bridges, or data sync

## Signals: When UI-First is Fine

- Greenfield feature with new tables you control
- Prototyping / exploring UX before committing
- You are the sole consumer of the data contract

## Reference

- [Issue #449: FDW Schema Alignment](https://github.com/DaveX2001/deliverable-tracking/issues/449)
- [ADR: FDW Table Replacement Strategy](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/paul_youtube_fullstack/adr-fdw-table-replacement-strategy)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

