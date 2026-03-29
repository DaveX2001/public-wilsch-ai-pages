---
publish: true
---

[[claude-code-architecture]]

# ADR: Adopt Three-Tier Invocation Model for Claude Code Extensions

## Status
**Accepted** | Date: 2025-11-29

## Context

Claude Code offers multiple mechanisms for extending behavior: protocol (CLAUDE.md), slash commands, and skills. Without clear guidance on when to use each, we risk:

- Using skills for enforcement rules (unreliable at 20% auto-activation)
- Using protocol for complex workflows (bloats always-loaded context)
- Using commands for always-needed behavior (requires user to remember)

**Research Findings:**

Community testing (Scott Spence, 200+ tests) revealed skill auto-activation reliability:
- **Baseline (no intervention):** ~20% activation rate
- **Simple hook instruction:** ~50% activation rate
- **Forced evaluation hook:** ~84% activation rate
- **Protocol-based explicit invocation:** High reliability in fresh sessions

The skill selection mechanism uses pure LLM reasoning on descriptions - no algorithmic routing, embeddings, or classifiers.

## Decision

We adopt a categorization principle for Claude Code extension mechanisms:

| Mechanism | Use For | Reliability | Trigger |
|-----------|---------|-------------|---------|
| **Protocol** | Enforcement rules | 100% present | Always loaded |
| **Commands** | User-initiated workflows | 100% when invoked | User types `/command` |
| **Skills** | Domain expertise | 20-84% auto | AI decides based on description |

**Categorization Principle:**
- **Skills** → Domain expertise (how to work with X framework/tool)
- **Protocol** → Enforcement rules (all X must go to Y)
- **Commands** → User-initiated workflows (repeatable, explicit processes)

**Protocol-Triggers-Skill Pattern:**

For enforcement rules that require workflow expertise, combine mechanisms:
1. Protocol contains the RULE with explicit `Skill()` invocation instruction
2. Skill contains the WORKFLOW details
3. Protocol triggers skill reliably because instruction is always loaded

Example:
```markdown
# In CLAUDE.md protocol:
When creating any markdown, YOU MUST use Skill(hippocampus) BEFORE proceeding.

# In skills/hippocampus/SKILL.md:
[Detailed workflow: tier selection, phantom nodes, git commit...]
```

**Implementation Guidance (Persuasion Principles):**

When writing protocol enforcement rules, apply these principles to improve compliance:

| Principle | Technique | Example |
|-----------|-----------|---------|
| **Authority** | Imperative language | "YOU MUST", "NO EXCEPTIONS" |
| **Commitment** | Require announcement | "Announce: Using skill X for Y" |
| **Scarcity** | Time-bound | "BEFORE proceeding", "IMMEDIATELY" |
| **Social Proof** | Failure framing | "X without Y = failure. Every time." |
| **Anti-rationalization** | Block shortcuts | "Do NOT rationalize using quick search as alternative" |

Research basis: Meincke et al. (2025) found persuasion techniques doubled LLM compliance rates (33% → 72%).

## Consequences

### Positive
- Clear decision framework for new Claude Code extensions
- Reliable enforcement via protocol-triggers-skill pattern
- Skills reserved for their strength: domain expertise
- Reduced context bloat by keeping workflows in skills, not protocol

### Negative
- Protocol rules require careful writing with persuasion principles
- Cannot rely on skill auto-activation for enforcement
- More complexity than "just use skills for everything"

### Neutral
- Existing skills may need reclassification
- Team must understand the taxonomy to make correct choices

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Hooks for reliability** | 84% still not 100%; adds infrastructure complexity |
| **Skills for everything** | Auto-activation too unreliable for enforcement |
| **Protocol for everything** | Bloats always-loaded context; no progressive disclosure |
| **Commands for everything** | Requires user to remember; no AI assistance |

## Related Documents
- [[claude-code-architecture]]: Distinguish Workflows, Protocols, and Agents
- `~/.claude/hippocampus/global/persuasion-principles-skill-design.md`: Detailed persuasion psychology

## References
- Scott Spence: "How to Make Claude Code Skills Activate Reliably" (200+ tests)
- Meincke et al. (2025): Persuasion techniques in LLM compliance
- Lee Han Chung: "Claude Agent Skills: A First Principles Deep Dive"

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

