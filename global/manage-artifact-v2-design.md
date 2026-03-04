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

The original manage-artifact was built on two sources:
- **ADR 001 + 002** (from [Prompting 101 | Code w/ Claude](https://www.youtube.com/watch?v=ysPbXH0LpIE), ~Aug 2025): prompt engineering principles, iterative development, 10-component template, 4-stage progression
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

**Undefined:** Cross-reference findings from Tier 1 sources against ADR 001 + 002 and persuasion principles. Determine what to keep, update, or discard — and note which findings are model-generation-specific vs. universally applicable.

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

### Part 3: The Evidence-to-Fix Methodology

Current workflow: rubber-duck → clarity phases → micro-iteration → test. This is ad hoc. A systematic methodology needs three phases with clear questions at each step:

**Diagnosis phase:**
1. Observe the behavior failure (from conversation evidence)
2. Classify: which artifact type? which enforcement mechanism? (→ Part 2)
3. Root cause: is this an instruction problem, a tool contract problem, or a model capability ceiling?
4. If instruction: is it phrasing, placement, or structural?

**Fix phase:**
5. Apply the minimal change — small enough to attribute the outcome
6. Prefer structural fixes over instruction patches (tool contract changes, hooks)
7. Prefer positive specification over prohibition ("do X" not "don't do Y")

**Verification phase:**
8. Session C — organic verification in real work
9. Binary verdict: fixed or not fixed. If not, structured feedback on what persists.

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
