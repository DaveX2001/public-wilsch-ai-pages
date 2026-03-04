---
publish: true
---

# Manage-Artifact v2 — Instruction Fixing Methodology

[[improve-system-architecture]]

Design doc for redesigning the manage-artifact skill — the instruction-fixing element of the /improve-system pipeline. The loop that builds the loop.

---

## Problem Statement

The /improve-system pipeline works end-to-end: observations accumulate on position epics, cluster into release epics, get diagnosed, and route to manage-artifact for the fix step. Evidence collection is strong. Evidence-to-fix conversion is the weak link.

The contract strategy (Part 6: "The System Gets Smarter") sells the improvement loop as the client-facing differentiator — every time someone uses the system, the system improves. manage-artifact is the mechanism that makes this promise real. If it's unreliable, the competitive moat is hollow.

After 5+ passes on CCI #604 Theme 1 (JA /probe auto-advance), the behavior persists. The current manage-artifact skill (created December 12, 2025) is bloated, unfocused, and built on methodology that predates Claude 4.6's behavioral changes.

**Three likely root causes (hypotheses — not yet confirmed):**

1. **Bloated skill, lost methodology.** The routing table covers 6 artifact types with 8 reference files. The methodology is sound but buried in noise. No clear process a stranger could follow.

2. **Outdated assumptions.** The ADRs (001, 002) derive from the September 2024 Anthropic Deep Dive video. Claude 4.6 models overtrigger on aggressive instruction language ("CRITICAL: You MUST...", "No exceptions") — causing overeagerness, not obedience.

3. **Wrong framing.** manage-artifact treats instruction fixing as prompt engineering (static prompt → response). The actual problem is agentic instruction design — writing instructions that hold across many autonomous multi-turn sessions with tool use. Different physics.

**Preconditions:**
- The /improve-system architecture (A/B/C sessions) is validated and working
- The observation system (position epics, release epics) is mature
- The Developer position works nearly perfectly — proof the methodology CAN work
- CCI #604 provides 47 comments of empirical evidence on fix iteration failures
- These root causes are assumptions informed by empirical patterns, not confirmed diagnoses

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | A focused skill that handles the HOW of instruction fixing — so the user can concentrate on diagnosing the behavioral problem, not on figuring out fix methodology |
| **Success** | Each fix attempt is focused and minimal. Best practices are built in from day one — the skill guides the process rather than the user directing it. Changes are small enough to attribute outcomes (did this specific change help or not?). |
| **Done test** | Can I diagnose a behavior problem and let manage-artifact guide me through a focused fix attempt without having to remind it of best practices? If YES → skill works |

---

## Approach

### Part 1: Methodology Foundation Refresh

The original manage-artifact was built on three sources:
- [Prompting 101 | Code w/ Claude](https://www.youtube.com/watch?v=ysPbXH0LpIE) (~Aug 2025): prompt engineering principles, iterative development, 10-component template, 4-stage progression
- [ADR 001: Universal Prompt Engineering Principles](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/universal-prompt-engineering-principles)
- [ADR 002: Iterative Prompt Development Methodology](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/iterative-prompt-development-methodology)
- **Meincke et al. 2025**: persuasion principles (Authority, Commitment, Social Proof)

Since December 2025, Anthropic has published updated guidance that may invalidate assumptions in the current methodology. Best practices may also shift with each model generation — what works for Claude 4.6 may behave differently on the next generation. The methodology needs to be model-aware, not model-locked.

The following sources need to be consumed and cross-referenced against the existing ADRs before defining the v2 methodology. Each resource was scored on two axes by dedicated analysis agents:

- **P** = Instruction Persistence (how well it addresses instructions holding across autonomous multi-turn sessions)
- **E** = Evidence-to-Fix (how well it addresses systematic diagnosis and minimal instruction fixes)

Tier placement = combined score. Resources within a tier are ordered by combined relevance.

**Tier 1 — Must consume first:**

| Resource | P | E | Key Insight |
|----------|:-:|:-:|-------------|
| [Prompting Best Practices](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/claude-prompting-best-practices) | 9 | 7 | Claude 4.6 behavioral changes, multi-context window workflows, named failure modes |
| [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) | 10 | — | JSON > markdown, initializer agents, progress files, instruction persistence patterns |
| [Intro to Agent Skills](https://anthropic.skilljar.com/introduction-to-agent-skills) | 9 | 7 | Skill troubleshooting guide, trigger design, progressive disclosure |

**Tier 2 — High value:**

| Resource | P | E | Key Insight |
|----------|:-:|:-:|-------------|
| [Building with Claude API — Eval module](https://anthropic.skilljar.com/claude-with-the-anthropic-api) | 6 | 8 | Eval workflow: hypothesis → test → grade → fix |
| [Codified Context](https://arxiv.org/abs/2602.20478) | 9 | — | Constitution model across 283 sessions at scale |
| [AI Prompt Engineering: A Deep Dive](https://www.youtube.com/watch?v=T9aRN5JkmL8) | 2 | 6 | Zack Witten's self-diagnosis technique, edge case coverage |

**Tier 3 — Reference:**

| Resource | P | E | Key Insight |
|----------|:-:|:-:|-------------|
| [Agent SDK docs](https://docs.anthropic.com/en/docs/agent-sdk/overview) | 8 | 5 | settingSources gotcha, hooks as diagnostic infrastructure |
| [ContextCov](https://arxiv.org/abs/2603.00822) | 8 | — | Executable constraints from instruction files, context drift detection |
| [ACE — Agentic Context Engineering](https://arxiv.org/abs/2510.04618) | 8 | — | Context as evolving playbook, brevity bias + context collapse |
| [Where LLM Agents Fail](https://arxiv.org/abs/2509.25370) | — | 7 | Error cascade taxonomy, AgentDebug framework |
| [Building Effective Agents](https://www.anthropic.com/engineering/building-effective-agents) | 4 | 6 | Evaluator-optimizer pattern, poka-yoke, ACI design |
| [Cookbooks — evaluator_optimizer.ipynb](https://github.com/anthropics/claude-cookbooks/tree/main/patterns/agents) | 6 | 7 | Reference implementation of eval loop |
| [Agent Skills with Anthropic](https://www.deeplearning.ai/short-courses/agent-skills-with-anthropic/) | 6 | 2 | SKILL.md format, builds skills not fixes them |
| [Zack Witten Prompt Workshop](https://www.youtube.com/watch?v=hkhDdcM5V94) | — | — | Live prompt editing, overlaps with Deep Dive |
| [Interactive Tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial) | — | — | 13 Jupyter notebooks, basic prompt eng fundamentals |

**Content hubs (bookmark for ongoing discovery):**

| Hub | URL |
|-----|-----|
| Anthropic Engineering Blog | anthropic.com/engineering |
| Anthropic Academy | anthropic.skilljar.com |
| Anthropic YouTube | youtube.com/@anthropic-ai |
| Anthropic GitHub | github.com/anthropics |
| DeepLearning.AI (Anthropic filter) | deeplearning.ai → Anthropic courses |

**Tier 4 — From [CURATED-LINKS.md](https://github.com/DaveX2001/claude-code-improvements/blob/main/CURATED-LINKS.md) (unscored):**

| Resource | Key Insight |
|----------|-------------|
| [Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents) | Anthropic's eval methodology — start with 20-50 tasks from real failures |
| [Bloom: Automated Behavioral Evaluations](https://www.anthropic.com/research/bloom) | Anthropic's open-source framework for automated behavioral evals |
| [Lessons from Building Claude Code](https://x.com/trq212/status/2027463795355095314) | Anthropic engineer on agent action space design, AskUserQuestion as sweet spot |
| [Code Field (Inhibition Prompting)](https://github.com/NeoVertex1/context-field) | Claims "Do not X" beats "Do X" — contradicts Anthropic docs, worth investigating |
| [AI Agents Need Memory Control (ACC)](https://arxiv.org/abs/2601.11653) | Separates artifact recall from state commitment, stable multi-turn behavior |
| [Don't waste your back pressure](https://banay.me/dont-waste-your-backpressure/) | Build feedback loops so agents self-correct instead of manual validation |
| [Agentic Design Patterns (424 pages)](https://docs.google.com/document/d/1rsaK53T3Lg5KoGwvf8ukOUvbELRtH-V0LnOIFDxBryE/preview) | 21 agentic patterns including reflection, planning, memory |
| [Stanford/Harvard: Agent Adaptation](https://x.com/alex_prompter/status/2002348526341402979) | Why agentic AI fails: rigid tool use is a hidden failure mode |
| [AGNE: Everything is Context](https://arxiv.org/abs/2512.05470) | File system abstraction for Context Engineering, MCP integration |
| [Context Graphs: Trillion-Dollar Opportunity](https://foundationcapital.com/context-graphs-ais-trillion-dollar-opportunity/) | Decision traces > data — queryable precedent infrastructure |
| [PAHF: Personalized Agents from Human Feedback](https://x.com/dair_ai/status/2025242624790331520) | Agents that learn preferences via pre-action clarification + post-action feedback |
| [ByteDance Mole-Syn: CoT as Molecular Structure](https://x.com/bowang87/status/2025227673820176689) | Why LLMs fail at long reasoning — structure emerges from training, not prompting |

**Undefined:** Cross-reference findings from Tier 1 sources against existing methodology and persuasion principles. Determine what to keep, update, or discard — and note which findings are model-generation-specific vs. universally applicable.

### Part 2: Artifact Type Physics

With Claude Code, there are four artifact types that govern agent behavior, each with a different enforcement mechanism:

| Type | Enforcement | Failure Mode |
|------|------------|-------------|
| **Command** | Direct invocation (user triggers) | Instruction ignored mid-execution |
| **Skill** | Trigger matching (AI discovers) | Doesn't fire, or fires wrong |
| **Protocol** | Persistent context (CLAUDE.md) | Decays over long sessions |
| **Hook** | Automatic (code enforcement) | Config error — but no compliance needed |

Hooks always work because they bypass instruction compliance entirely. Commands and protocols are the fragile types — they need the AI to keep following rules across many autonomous turns. The fix methodology should account for which type is being fixed, because the failure modes and fix approaches differ.

The first diagnostic question in any fix session should be: **is this behavior best addressed through instructions (command/skill/protocol) or enforcement (hook)?** Some behaviors may be structurally unfixable through instructions alone.

**Undefined:** Should some behaviors currently encoded as commands/protocols be converted to hooks instead? The /probe auto-advance (CCI #604) might be structurally unfixable through instructions — a hook that enforces the gate could be more reliable.

**Undefined:** The interaction between instruction layers (CLAUDE.md + skills + commands + output styles) is underspecified by Anthropic. When these conflict, which wins? This matters for diagnosing fix failures — the fix might be correct but overridden by a competing instruction.

To decide which artifact type is the right fix, the methodology needs a mapping from failure domain to artifact type. Initial framing:

| Artifact Type | What It Fixes | Example |
|--------------|--------------|---------|
| **Command** | Interactive workflow — the AI doesn't follow the right steps when the user triggers a specific process | /capture gate loop, /probe question format |
| **Skill** | Manufacturing/production — the AI doesn't know HOW to build a specific kind of artifact | Prepackaged thinking for design docs, meeting agendas |
| **Protocol** | Persistent behavior — the AI always does X wrong regardless of context or which command is running | Output formatting, git conventions, investigation delegation |
| **Hook** | Enforcement — instruction-based compliance isn't reliable enough, need a structural guarantee | Blocking tool calls without prerequisites, auto-formatting |

**Related:** [ADR-008: Distinguish Workflows, Protocols, and Agents](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/distinguish-workflows-protocols-agents-ai-systems) defines the architectural hierarchy (Protocol → Agent → Workflow). This ADR predates the artifact type routing table but establishes the conceptual framework for why different artifact types exist and what layer they operate at.

**Undefined:** This mapping is a first hypothesis. Each artifact type likely has sub-categories and edge cases. What belongs in a command vs. a protocol when the behavior spans multiple commands? What belongs in a skill vs. a command when the process is interactive AND produces an artifact? Need empirical validation from existing artifacts that work well (Developer position) vs. ones that don't (CCI #604). Cross-reference with ADR-008's architectural hierarchy.

### Part 3: The Evidence-to-Fix Methodology

Current workflow: rubber-duck → clarity phases → micro-iteration → test. This is ad hoc. A systematic methodology needs diagnostic questions that guide the fix process. The following questions are hypotheses — not yet validated through empirical data or external methodology. They need cross-referencing against the Tier 1 sources and cookbooks.

**Diagnostic questions to investigate:**
- Which artifact type should fix this? (→ Part 2 mapping)
- Is this an instruction problem, a tool contract problem, or a model capability ceiling?
- If instruction: is it phrasing, placement, or structural?
- How minimal can the change be while still being attributable in Session C?
- Is the current [prompt template](https://github.com/veloxforce/claude-user-configs/blob/main/templates/anthropic-prompt-template.md) (10-component Anthropic structure) still the right framework, or does it need updating for Claude 4.6 and agentic contexts?

**Undefined:** The evaluator-optimizer pattern from Anthropic's cookbooks (separate evaluator with binary verdict + structured feedback + memory of prior attempts) could formalize the verification step. Needs investigation after consuming Tier 1 sources.

**Undefined:** Zack Witten's self-diagnosis technique ("ask the model why it got it wrong and ask it to rewrite the instructions") — should this be a formal diagnosis step?

**Undefined:** The "error cascade" finding (Where LLM Agents Fail, arxiv:2509.25370) — errors cascade across steps, which explains why single-instruction patches fail. How to formalize root cause attribution in the diagnosis phase?

### Part 4: Structural Patterns for Instruction Persistence

Patterns surfaced from Anthropic's engineering blog and academic research. These need validation against the Tier 1 sources before adoption into the methodology:

| Pattern | What It Does | Source |
|---------|-------------|--------|
| JSON state files | Resist inappropriate overwriting better than markdown | Effective Harnesses |
| Progress files as external memory | State survives context window refreshes | Effective Harnesses |
| Initializer agent with different prompt | First context window (setup) vs. subsequent (iteration) | Effective Harnesses |
| Targeted strong language | Reserve authority phrasing for critical constraints only ("unacceptable to remove tests") | Effective Harnesses |
| XML tags as instruction anchors | Named behavioral rules (`<default_to_action>`) | Prompting Best Practices |
| Context continuation prompt | Tell Claude that compaction is happening | Prompting Best Practices |
| Git as state tracking | Claude 4.6 excels at discovering state from filesystem | Prompting Best Practices |
| Constitution (always-loaded doc) | Persistent conventions + orchestration protocols | Codified Context paper |

**Undefined:** Which patterns should manage-artifact v2 recommend by default? The new version should prescribe specific structural patterns per artifact type (Part 2), not treat all artifacts the same.

**Undefined:** The "Codified Context" paper documents a three-component architecture (hot-memory constitution + specialized agents + cold-memory knowledge base). How does this map to the existing CLAUDE.md + skills + hippocampus architecture?

---

## Source

- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/d2fa50bf-8e4c-4419-973b-acbc80736827.jsonl
- **Position Epic:** [CCI #600 — System Engineer](https://github.com/DaveX2001/claude-code-improvements/issues/600)
- **Release Epic Evidence:** [CCI #604 — JA Lifecycle Violations](https://github.com/DaveX2001/claude-code-improvements/issues/604) (47 comments, 5+ fix passes)
- **Current manage-artifact:** `~/.claude/skills/manage-artifact/SKILL.md` (created Dec 12, 2025)
- **Improve System Architecture:** `~/.claude/hippocampus/global/improve-system-architecture.md`
- **Contract Strategy:** `~/.claude/hippocampus/project/soloforce/contract-strategy-retainer-model-design.md` — Part 6
- **CCI Board Structure:** `~/.claude/hippocampus/project/soloforce/cci-board-structure-design.md` — §6
