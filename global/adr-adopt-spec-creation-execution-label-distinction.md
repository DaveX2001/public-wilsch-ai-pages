---
publish: true
---
# ADR: Adopt Maker/Spec-Design and Maker/Spec-Implement Label Distinction

[[team-scaling]]

## Status

Accepted (2026-01-22) | Updated (2026-01-23)

## Context

After 6-8 weeks working with a client project, a clear pattern emerged: **I cannot do everything alone anymore.**

Key learning: by the time I tried to delegate, there was no time to specify work for handoff. Issues existed with bodies (What/Why) but tracking files were empty - no ACs, no clear "done" criteria for someone else to verify against.

The distinction isn't about whether issues have structure. It's about **what the issue outputs**:

- Some issues output **specifications** (workflow definitions, architecture decisions, design documents)
- Some issues output **implementations** (code, features, configurations from a specification)

The verification method differs fundamentally between these types:
- **Spec-design** outputs artifacts - verified by DoD ("does this exist?")
- **Spec-implement** outputs behavior - verified by DoD + ACs ("does code behave as specified?")

This distinction determines what's delegable: ACs enable someone else to verify without your judgment.

## Decision

**Adopt two labels under MAKER that distinguish issues by context requirement:**

| Label | Meaning | Context Requirement |
|-------|---------|---------------------|
| `maker/spec-design` | Output is a specification | Needs context OUTSIDE the issue |
| `maker/spec-implement` | Output is implementation | Everything needed is IN the issue |

### Hierarchy

```
MAKER (focused work)
├── maker/spec-design   ← Designing specifications
└── maker/spec-implement ← Implementing from specifications

MANAGER (quick coordination)
└── unchanged
```

### Core Distinction

**Context containment determines type:**
- If the work requires exploration, decisions, external knowledge → `maker/spec-design`
- If the issue is self-contained and executable as-is → `maker/spec-implement`

### Verification Rationale

| Type | DoD | ACs | Why |
|------|-----|-----|-----|
| **maker/spec-design** | Required | Optional | Output is artifact, not behavior |
| **maker/spec-implement** | Required | Required | ACs enable delegation |

ACs are designed for behavioral verification. Spec-design outputs artifacts, not behavior - DoD handles verification ("artifact exists", "decision documented").

## Research Basis

| Term | Source |
|------|--------|
| Discovery vs Delivery | Marty Cagan (Inspired) |
| Problem Space vs Solution Space | Product management discipline |
| Work ON vs Work IN the business | Michael Gerber (E-Myth Revisited) |

**The backpressure insight** (from banay.me): Systems with automated feedback mechanisms enable delegation. Without backpressure, the context holder spends time manually catching errors.

## Consequences

**Positive:**
- Clear visual distinction: "needs design" vs "ready for delegation"
- Enables working ON the business vs IN the business
- Scalable: as team grows, spec-implement capacity grows

**Negative:**
- Additional labels to maintain
- Handoff point requires judgment

## Implementation

See [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) for:
- Detailed workflows by type
- Stage definitions
- Tools and skills
- Gap analysis

## Related

- [Issue Lifecycle Router](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/issue-lifecycle-router) - Implementation workflows
- [Don't Waste Your Backpressure](https://banay.me/dont-waste-your-backpressure/) - Feedback mechanisms

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

