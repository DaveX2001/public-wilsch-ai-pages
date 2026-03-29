---
publish: true
---

# 004: Standardize Slash Command Development Framework

[[prompt-engineering]]

## Status
**Proposed** | Date: 2025-09-01

## Context
- Slash commands developed inconsistently across different stages and structures
- `/first_principles` shows value of systematic approach but other commands lack standardization
- ADR002 provides clear stage progression but no enforcement mechanism
- Mix of ad-hoc prompt creation and template-based approaches creates maintenance burden
- Need systematic way to generate, version, and evolve commands
- Anthropic's 10-component template provides proven structure but not consistently applied

## Decision
We will adopt a comprehensive slash command development framework combining metadata standardization, template consistency, and systematic generation.

**Three-Part Framework:**

1. **Metadata via Frontmatter**
```yaml
---
description: "[YYYY-MM-DD] [Stage X] Command purpose"
argument-hint: [optional argument hints]
---
[command content following Anthropic template]
```

2. **Template Consistency** 
All commands must use Anthropic's 10-component structure mapped to ADR002 stages:
- Stage 1: Component 7 only (Immediate task)
- Stage 2: Components 1, 2, 7 (Task context, Tone, Immediate task)
- Stage 3: Components 1, 2, 3, 4, 5, 7 (Add Background, Detailed rules, Examples)
- Stage 4: All components including Output formatting and Thinking process

3. **Generation Tool and Standards**
- `/stage_prompt $1 $2` meta-command using positional arguments ($1=stage, $2=command)
- File references with `@anthropic-prompt-template.md` for template inclusion
- Structured commands use `$1, $2, $3` vs `$ARGUMENTS` for free-form input

**Completion Criteria:**
- All existing commands updated with standardized format
- `/stage_prompt` tool operational for generating new commands  
- Documentation updated with development guidelines

## Consequences

### ✅ Positive
- **Systematic consistency** - All commands follow same structure and progression
- **Quality assurance** - Template prevents ad-hoc prompt creation errors
- **Native compatibility** - Uses Claude Code's frontmatter system without parsing conflicts
- **Maintainability** - Stage tracking visible in command descriptions and tooltips
- **Efficiency** - Meta-command generates properly structured prompts
- **Knowledge transfer** - Consistent format aids team understanding

### ❌ Negative
- **Initial overhead** - Updating existing commands requires time investment
- **Learning curve** - Team must understand Anthropic template components
- **Learning curve** - Team must understand frontmatter format and positional arguments

### ⚪ Neutral
- **Process change** - Shifts from ad-hoc to systematic command development
- **Tool dependency** - Relies on `/stage_prompt` for consistent generation
- **Documentation maintenance** - Template and mapping must stay current

## Alternatives Considered

| Alternative | Rejection Reason |
|-------------|------------------|
| **Keep ad-hoc approach** | Leads to inconsistent quality and maintenance burden |
| **Metadata only** | Doesn't address template inconsistency root cause |
| **Template only** | No version tracking or systematic generation |
| **Different template system** | Anthropic template already proven and documented |
---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

