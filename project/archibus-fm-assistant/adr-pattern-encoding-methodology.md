---
publish: true
---

# ADR: Adopt Pattern Encoding Methodology for AI Best Practice Extraction
[[client-archibus]]

## Status
**Proposed** | Date: 2025-12-16

## Context
- FM Assistant requires domain knowledge to provide value (AI alone cannot know FM best practices)
- Open-ended brainstorming sessions waste time and go in circles
- Domain experts hold tacit knowledge that needs to be made explicit
- AI intelligence layer needs structured patterns to enforce, not ad-hoc rules
- Bruce Platform architecture enables "solve once, works everywhere" via connectors

## Decision
We will use **Pattern Encoding** methodology to systematically extract high-value workflows from domain experts and encode them into the AI intelligence layer.

**The methodology follows a 4-step chain:**

1. **Pain Point** → What hurts? Where is the friction?
2. **Workflow** → Which workflow is affected by this pain?
3. **Best Practice** → What is the correct way to do this workflow?
4. **Encode** → Capture pattern into AI intelligence layer

<img src="pattern-encoding-methodology.png" width="100%" alt="Pattern Encoding Methodology">

## Consequences

### ✅ Positive
- Structured extraction replaces open-ended brainstorming
- Domain experts teach; AI learns and enforces
- Users get cognitive offload (AI knows the "right way")
- Patterns are platform-agnostic (work at Mandala FM level)
- Connectors enable "solve once, works everywhere"

### ❌ Negative
- Requires domain expert availability for extraction sessions
- Methodology takes time to apply per block
- Quality depends on expert articulation of tacit knowledge

### ⚪ Neutral
- Methodology repeats for each Mandala FM block (Assets, Contracts, Works, Costs, Resources)
- Facilitator role required to guide extraction sessions
- Patterns need translation to API specs after extraction

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Open brainstorming** | Dec 11 meeting went in circles for 2+ hours without clear output |
| **AI discovers patterns from data** | Insufficient data; patterns require domain expertise |
| **Copy competitor approaches** | Platform-agnostic architecture requires custom methodology |

---

**Key Resources:**
- [Pattern Encoding Meeting Agenda](pattern-encoding-meeting-agenda.md) - Facilitation guide for extraction sessions
- [Bruce Platform Elements](bruce-elements-diagram.png) - Mandala FM architecture
- [Hybrid Environment ADR](adr-novnc-docker-hybrid-environment.md) - AI + UI integration approach

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

