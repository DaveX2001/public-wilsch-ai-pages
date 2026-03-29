---
publish: true
---
# ADR: Evaluation-Driven Development for AI Systems

[[prompt-engineering]]

## Status
**Proposed** | Date: 2025-09-30

## Context
- AI systems are typically optimized without reliable measurement infrastructure
- Changes are made based on intuition or anecdotal feedback, not empirical data
- Unclear whether optimizations actually improve system performance
- Effort gets wasted on wrong problems due to lack of baseline understanding
- Need systematic methodology to enable measurable, iterative improvement

## Decision
We will adopt **evaluation-driven development** as the standard approach for AI system improvement.

**Implementation Specifics:**
- Build automated evaluation infrastructure before optimization work
  - Test data (representative examples)
  - Execution pipeline (generate AI outputs systematically)
  - Structured output format (machine-readable for analysis)
- Delegate deep analysis to AI agents with clear parsing instructions
- Establish accurate baseline metrics before any optimization
- Change one variable at a time to isolate impact attribution
- Re-measure after each change to validate improvement empirically
- Iterate: measure → identify issue → change → measure → validate

## Consequences

### ✅ Positive
- **Clear baselines** justify optimization work and prioritize efforts
- **Measurable impact** for each change - validate improvements empirically
- **Systematic improvement cycles** replace guesswork with data
- **AI agents handle analysis** - delegate parsing/counting to automated tools
- **Iterative refinement** enables continuous improvement with feedback loop
- **Single-variable changes** allow clear attribution of cause and effect

### ❌ Negative
- **Upfront time investment** before seeing actual improvements to system
- **Requires discipline** to build measurement infrastructure before features
- **Initial baseline may reveal** uncomfortable truths about current performance
- **Measurement accuracy challenges** - may need to improve evaluation tools first

### ⚪ Neutral
- **Shifts development priority** from features to measurement infrastructure
- **Changes workflow pattern** from "implement → hope" to "measure → improve → validate"
- **Requires structured output** formats (XML, JSON) for reliable parsing

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Ad-hoc manual testing** | No systematic measurement of impact, unclear if changes actually help |
| **Intuition-based optimization** | Wasted effort on perceived problems that may not be real bottlenecks |
| **Manual evaluation only** | Doesn't scale, human inconsistency in measurement, time-consuming |
| **Optimize first, measure later** | Without baseline, can't prove improvements or prioritize high-impact work |

---

**Key Resources:**
- Proof case: This methodology achieved 6.25x improvement (8% → 50%) through measured iterations
- Core principle: "If you can't measure it accurately, fix the measurement infrastructure first"
- Validation: Each improvement cycle revealed ground truth closer than previous perception
---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

