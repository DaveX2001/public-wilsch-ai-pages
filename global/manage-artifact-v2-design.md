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
| [The Complete Guide to Building Skills for Claude](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) | 9 | 5 | Mechanical Bible — skill/command writing, 5 workflow patterns, progressive disclosure, troubleshooting. Supersedes Agent Skills course for skill-building |
| [Prompting Best Practices](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/claude-prompting-best-practices) | 9 | 7 | Claude 4.6 behavioral changes, multi-context window workflows, named failure modes |
| [Effective Harnesses for Long-Running Agents](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents) | 10 | — | JSON > markdown, initializer agents, progress files, instruction persistence patterns |
| [Intro to Agent Skills](https://anthropic.skilljar.com/introduction-to-agent-skills) | 9 | 7 | ~~Superseded by Skills Guide for skill-building.~~ Retains value for Academy course structure and eval module integration |

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

### Methodology Foundation Findings (Pass 3)

Tier 1 sources consumed and cross-referenced against existing methodology. Five high-leverage techniques identified for shaping AI thinking through instructions:

| # | Technique | Source | Mechanism |
|---|-----------|--------|-----------|
| 1 | Persona selection | [Anthropic PSM](https://www.anthropic.com/research/persona-selection-model) ([deep dive](https://alignment.anthropic.com/2026/psm/)) | Identity framing selects holistic persona from pretraining space — not compliance |
| 2 | WHY over WHAT | [Best Practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices) + [Codified Context](https://arxiv.org/abs/2602.20478) | Explanations create generalizable heuristics. Rules only cover anticipated cases |
| 3 | Thinking is promptable | [Adaptive Thinking](https://platform.claude.com/docs/en/build-with-claude/adaptive-thinking) | Native interleaved thinking shaped through system prompt — guide WHAT to think about |
| 4 | Right altitude | [Context Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) | Not too prescriptive (brittle), not too vague (no signal) |
| 5 | Consequences not commands | [ContextCov](https://arxiv.org/abs/2603.00822) + Dev protocol | "X causes Y" gives thinking layer reasoning material |

**Persuasion principles (Meincke et al.): Deprecated for Claude 4.6.** Authority ("YOU MUST"), Commitment, Social Proof cause overtriggering on Claude 4.6. Strong language necessary for pre-4.6 models now produces overeagerness. Unity (shared identity) still aligns with persona selection. The calibration flipped — dial back, don't amplify.

**Model-generation note:** These findings are calibrated for Claude 4.6. Strong language effects may differ on future model generations. The methodology should be model-aware, not model-locked.

### Methodology Foundation Findings (Pass 4)

**The Two-Bible Model.** Instruction artifacts require two distinct reference frameworks depending on which dimension is being fixed:

| Bible | Scope | Artifact Types | Persistence Mechanism |
|-------|-------|---------------|----------------------|
| [PSM](https://www.anthropic.com/research/persona-selection-model) (Behavioral) | How the AI thinks — persona, beliefs, psychology | Protocol (CLAUDE.md) | Beliefs enter native thinking, create self-debate under pressure |
| [Skills Guide](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) (Mechanical) | How to write instructions — structure, patterns, triggers | Skills/Commands (merged by Anthropic) | Progressive disclosure, validation gates, explicit step ordering, specificity |

**Evidence for separation:** The ARCHIBUS Setup agent (#629 witness observation, session e1a2095a) was written with mechanical patterns only (procedural steps, detection gates, checklists) and no PSM persona. Result: technically functional but template-following behavior — "Step 0 ✅" with checkmarks instead of the prescribed "senior CAFM colleague" behavior. Mechanical without behavioral = template agents.

**Evidence for combination needed:** The CCI #604 auto-advance required a PSM fix (belief transformation, session 97b6ea37) after 5 mechanical fix passes failed. Conversely, a PSM-informed protocol still needs mechanical structure for workflow steps. Neither Bible alone is sufficient.

**Command/Skill merger (Anthropic, 2026):** Official docs state: "Custom commands have been merged into skills." The `.claude/commands/` folder remains backward-compatible but skills are the recommended format. The [orchestration pattern](https://github.com/shanraisshan/claude-code-best-practice/blob/main/orchestration-workflow/orchestration-workflow.md) (Command → Agent → Skill) shows role separation persists in practice, but the writing format is unified. The Skills Guide PDF covers how to write both.

### Part 2: Artifact Type Physics

With Claude Code, there are four artifact types that govern agent behavior, each with a different enforcement mechanism:

| Type | Enforcement | Failure Mode |
|------|------------|-------------|
| **Skill** (includes commands) | Direct invocation (`/name`) or trigger matching (AI discovers) | Instruction ignored mid-execution, doesn't fire, or fires wrong |
| **Protocol** | Persistent context (CLAUDE.md) | Decays over long sessions |
| **Hook** | Automatic (code enforcement) | Config error — but no compliance needed |

Hooks always work because they bypass instruction compliance entirely. Skills and protocols are the fragile types — they need the AI to keep following rules across many autonomous turns. The fix methodology should account for which type is being fixed, because the failure modes and fix approaches differ.

**Format merger note (Pass 4):** Anthropic's official docs state "Custom commands have been merged into skills." Both use the same SKILL.md format and YAML frontmatter. The `.claude/commands/` folder is backward-compatible. The dimensional distinction (Doing vs Knowing) still holds — a skill handling workflow steps serves a different behavioral function than a skill providing domain knowledge — but the engineering format is unified. The [Skills Guide PDF](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) covers how to write both.

The first diagnostic question in any fix session should be: **is this behavior best addressed through instructions (command/skill/protocol) or enforcement (hook)?** Some behaviors may be structurally unfixable through instructions alone.

### The Three Dimensions of Instruction Artifacts

Beyond enforcement mechanism, each instruction-based artifact type governs a different dimension of AI behavior. This dimensional mapping is the foundation for evidence-to-fix routing (Part 3).

| Artifact Type | Dimension | Governs | Fix when... |
|--------------|-----------|---------|-------------|
| **Protocol** | Thinking | Beliefs, mental models, hidden reasoning | AI reasons toward the wrong action |
| **Skill (Doing)** | Doing | Steps, gates, workflow lifecycle | AI skips a step or takes a shortcut |
| **Skill (Knowing)** | Knowing | Domain knowledge, experience, reference | AI lacks knowledge to do its job |
| **Hook** | Enforcement | Structural guarantees | Instructions can't reliably ensure compliance |

**The principle: "When we get thinking right, we get behavior right."** The AI's decisions happen in the hidden thinking trace (native thinking, sequential thinking, interleaved reasoning) before visible output. Protocol shapes this layer. Commands shape what the AI does after thinking. Skills provide what the AI needs to know. When thinking is correct, doing follows naturally. **When thinking is wrong, no amount of procedural instruction overrides it.**

**Evidence:** CCI #604 Theme 1 — five procedural fix passes targeting the /probe command failed to resolve JA auto-advance behavior. Conversation-reader analysis of two Session C conversations (9f14b021, 5459d299) revealed the AI's thinking trace explicitly reasoned: "This is resolved. Let me move to the next item." The belief ("user answered = item done") overrode the procedural instruction ("don't advance"). The fix needed to target protocol (thinking), not command (doing).

**Architectural implication:** The Developer protocol is pure thinking-layer — behavioral principles only, procedural detail lives in commands. [The JA protocol](https://github.com/veloxforce/claude-user-configs/blob/45d27f3/personas/ja/CLAUDE.md) mixes thinking and doing — procedural rules embedded alongside behavioral principles. This mixing gives JA fixes higher blast radius: every fix touches the core protocol, potentially destabilizing other behaviors. Separating these layers is a prerequisite for reliable instruction fixes.

The /probe auto-advance (CCI #604) was initially hypothesized as potentially unfixable through instructions. Dimensional analysis (above) revealed it as a thinking-layer problem misdiagnosed as a doing-layer problem — procedural fixes targeted the wrong dimension. Whether a hook is still needed remains open after attempting a protocol-level (thinking) fix.

The mechanical precedence between instruction layers IS specified by Anthropic: settings follow Managed → CLI → Local → Shared → User; CLAUDE.md loads with project > user precedence; skill name collisions resolve enterprise > personal > project. Output styles modify the system prompt directly while CLAUDE.md arrives as a user message after it ([source](https://docs.anthropic.com/en/docs/claude-code/output-styles#output-styles-vs-claude-md-vs-append-system-prompt)).

**Undefined:** What remains unspecified is **behavioral conflict resolution at runtime** — when a CLAUDE.md protocol and a skill's instructions give contradictory behavioral guidance, Anthropic's docs explicitly state: "Claude may pick one arbitrarily" ([source](https://docs.anthropic.com/en/docs/claude-code/memory#choose-where-to-put-claude-md-files)). This matters for diagnosing fix failures — the fix might be correct but overridden by a competing instruction in a different layer. The "pick arbitrarily" admission confirms this is intentionally unspecified, not undiscovered documentation.

To decide which artifact type is the right fix, the methodology needs a mapping from failure domain to artifact type. Initial framing:

| Artifact Type | What It Fixes | Example |
|--------------|--------------|---------|
| **Command** | Doing — the AI doesn't follow the right steps when the user triggers a specific process | /capture gate loop, /probe question format |
| **Skill** | Knowing — the AI doesn't know HOW to build a specific kind of artifact | Prepackaged thinking for design docs, meeting agendas |
| **Protocol** | Thinking — the AI believes the wrong thing regardless of context or which command is running | Output formatting, git conventions, investigation delegation |
| **Hook** | Enforcement — instruction-based compliance isn't reliable enough, need a structural guarantee | Blocking tool calls without prerequisites, auto-formatting |

**Related:** [ADR-008: Distinguish Workflows, Protocols, and Agents](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/distinguish-workflows-protocols-agents-ai-systems) defines the architectural hierarchy (Protocol → Agent → Workflow). This ADR predates the artifact type routing table but establishes the conceptual framework for why different artifact types exist and what layer they operate at.

### The Thinking Mechanism (Pass 3)

Native interleaved thinking — the reasoning that fires automatically between every tool call — is where behavioral decisions happen. Confirmed by Anthropic's docs ("Adaptive thinking automatically enables interleaved thinking") and interpretability research (visible thinking ≠ internal computation, but scaffolding influences outcomes).

**Three channels, one primary:**

| Channel | Mechanism | Behavioral governance? |
|---------|-----------|----------------------|
| Native interleaved | Always fires, can't be skipped | **Primary** — decisions happen here |
| Sequential thinking (MCP) | Requires invocation (doing-layer decision) | No — native thinking can override |
| AoT (MCP) | Requires invocation (doing-layer decision) | No — native thinking can override |

MCP thinking tools add visible reasoning text, but native thinking makes the actual behavioral decision after processing their output. For behavioral governance, protocol instructions target native thinking. MCP thinking tools remain useful for analytical depth in commands/skills.

**How instructions shape native thinking:**

Instructions don't control the internal computation directly — they shape the context that native thinking operates on. The mechanism: protocol instruction → shapes context → native thinking processes context → decision. Effective when instructions create beliefs (reasoning material). Ineffective when instructions create rules (compliance targets).

### Empirical Validation: Principles vs Rules (Pass 3)

Dev session analysis (34d514fe, #1056 implementation) vs JA Session C conversations (9f14b021, 5459d299):

**Dev protocol — belief holds under pressure:**

User says "download it please." The AI's thinking trace:

> 1. *"But wait — I'm in requirements-clarity phase, investigation only, no writes."*
> 2. *"Actually, downloading a test fixture file isn't implementation..."*
> 3. *"But to be safe, I'll note it as a requirement and handle it in implementation."*
> 4. *"Actually the user said 'download it please' — that's a direct instruction. But I'm in requirements-clarity where I CAN'T create/write files."*

Four rounds of self-debate. Direct user instruction loses to internalized belief. No rule cited — the protocol created a belief, not a compliance pattern. 83% of 18 thinking blocks showed this belief-based reasoning.

**JA protocol — rule reinterpreted under pressure:**

Thinking trace at the failure moment (Session C1):

> *"I think S1 is resolved. Let me move to S2."*

One line. No debate. No self-argument. When caught and asked to introspect (Session C2):

> *"The source of this behavior: I think I'm pattern-matching on 'the user answered the question = item is done.'"*

The rule "user clicks Next" didn't enter the thinking trace — it was absent, not overridden. A belief ("only the user determines completion") would have competed. A rule ("don't advance without Next") didn't.

**The contrast:** Beliefs create internal arguments that persist under pressure. Rules create compliance targets that fold under pattern-matching.

The JA protocol's bones are there (identity, some WHY, authority model). The fix is separation: extract procedures into commands, strengthen the principles that are partially present.

**Undefined:** Artifact type sub-categories and edge cases. What belongs in a command vs. a protocol when the behavior spans multiple commands? What belongs in a skill vs. a command when the process is interactive AND produces an artifact? Need empirical validation from existing artifacts that work well (Developer position) vs. ones that don't (CCI #604). Cross-reference with ADR-008's architectural hierarchy.

### Part 3: The Evidence-to-Fix Methodology

Part 3 defines the manage-artifact v2 skill's core content — the diagnostic methodology that rubber-duck loads during /improve-system. The skill's role is not routing to fix types (skill-creator handles skill building/fixing). The role is providing the diagnostic lens: given a behavioral failure, determine what's wrong and where to fix it.

The methodology follows a CBT (Cognitive Behavioral Therapy) parallel validated across two evidence sessions: observation → underlying belief → restructured belief (one abstraction level up) → generalization. The same flow applies whether creating an artifact from scratch or improving one after Session C evidence — only the starting point differs.

Six steps, each validated against empirical evidence:

### Step 1: Dimensional Diagnosis (Validated)

**The question:** "Is this a thinking problem, a doing problem, a knowing problem, a noise problem, or an output format problem?"

**Method:** Use conversation-reader to extract the thinking trace from the failure moment in the Session C conversation. Read the AI's hidden reasoning immediately before the failure. Walk through each dimension sequentially — each gets its own moment.

**The sequential walk (dependency-driven order):**

**1. Thinking** — Is the AI actively reasoning toward the wrong action?
- Signal: deliberate but wrong logic in the trace. Example: *"That's enough to resolve item 1. Let me move on."*
- If yes → PSM/CBT reframing (Step 3). This is the most common root cause and has the highest blast radius.

**2. Noise** — Is correct reasoning being overridden by a competing instruction?
- Signal: the trace shows the AI considered the right action, then pivoted due to another instruction or convention conflict.
- If yes → find and remove the competing instruction (Step 4). Depends on first seeing correct reasoning — comes after thinking.

**3. Doing** — Is a step absent from the trace entirely?
- Signal: the AI didn't reason about the step at all — it's missing, not argued against.
- If yes → add the procedure to the skill (Step 5 via skill-creator).

**4. Knowing** — Is the AI searching but can't find what it needs?
- Signal: searching behavior — the AI wanted to do the right thing but lacked information.
- If yes → add reference material to the skill's references/ folder (Step 5 via skill-creator).

**5. Output format** — Is reasoning correct AND steps followed, but output wrong?
- Signal: the AI's thinking and procedure were right, but the produced output doesn't match the desired form.
- If yes → add examples/templates ([Prompting Best Practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)). Depends on ruling out thinking + doing first — comes last.

**Shortcut:** If procedural fixes keep failing, it's thinking. If a procedural fix works first try, it's doing. If the AI was searching for something, it's knowing. If correct reasoning was overridden, it's noise. If everything is right except the output form, it needs examples.

**Validated against:** Two Session C conversations from CCI #604 (9f14b021, 5459d299) — thinking problem misdiagnosed as doing for 5 fix passes. Session a19f6dde — compound thinking+doing failure (JA /probe convergence: "answer = completion" belief amplified by "Next X/N" counter). Session 24d94df5 — system prompt contradicting SKILL.md on gating, PSM correctly predicted belief-layer wins over rule-layer. CCI #619 — output format failure despite correct beliefs (compound: belief + template fix).

**Provenance note (Pass 6):** Tests 2 and 3 from the original three-test model were cut — traced to Pass 5 extraction session (b0269523), where they were constructed to fill a classification table rather than extracted from distinct evidence. Applying the Augment pruning rubric to the methodology itself: they weren't failure-backed. The single-test sequential walk replaces them.

### Step 2: Self-Diagnosis (Validated — prompt format Undefined)

After reading the thinking trace (Step 1), ask the failing model why it behaved that way. This produces a different signal from retrospective trace analysis: the model's own explanation of its reasoning pattern.

**Evidence:** JA AI self-diagnosed in Session C2 — *"I'm pattern-matching on 'user answered = item done.'"* This directly named the belief that 5 procedural fix passes had missed. The model identified its own failure mode when prompted to introspect.

**Source:** [Zack Witten's self-diagnosis technique](https://www.youtube.com/watch?v=T9aRN5JkmL8) (Tier 2) — "ask the model why it got it wrong and ask it to rewrite the instructions."

**Undefined:** The exact prompt format for self-diagnosis. Current evidence suggests open-ended ("Why did you advance?") outperforms specific ("Did you follow rule X?"), but this needs formalization.

**Undefined:** A sub-agent implementation — load the Session C conversation into an agent (reproducing the original context window up to the failure point), then ask it to introspect. This would let you "ask past you" with the full original context preserved, rather than relying on retrospective analysis. Needs prototyping.

### Step 3: CBT/PSM Reframing (Validated)

The core transformation. Parallels Cognitive Behavioral Therapy: don't fix the behavior directly — fix the belief that produces it. Changed beliefs generalize to situations the observations never covered.

**The 4-step transformation:**

1. **Observe** the specific behavior (from Step 1 thinking trace)
2. **Identify the underlying belief** (from Step 2 self-diagnosis or trace analysis)
3. **Go UP one abstraction level** — PSM question: "What kind of person would behave correctly?" This forces an answer at the trait level, not the rule level. Don't negate the old belief ("don't advance") — find the higher belief that makes the old behavior unnecessary ("every answer opens territory I haven't explored yet")
4. **The restructured belief generalizes** — it covers the specific observed failures AND novel situations the observations never mentioned

**Evidence — JA /probe convergence (session a19f6dde):**

| Step | Concrete |
|------|----------|
| Observe | AI advances after answers without probing deeper |
| Belief | "answer = slot filled = advance" (from thinking trace: "that's enough") |
| Go UP | "What question does this answer give me? If nothing comes to mind, I haven't listened carefully enough" |
| Generalizes | Prevents premature advancing, batch-resolving, throughput pressure, AND covers novel situations |

**Evidence — ARCHIBUS Setup (session 24d94df5):**

| Step | Concrete |
|------|----------|
| Observe | Agent acknowledges gates but doesn't execute them |
| Belief | System prompt: "no gates" contradicts SKILL.md: "wait for confirmation" |
| Go UP | "The implementer's attention is scarce — never ask them to decide two things in one exchange" |
| Generalizes | Covers hierarchy gating, enum pacing, fast/slow lane — all from one belief |

**Compound detection:** After identifying the belief, check: is there mechanical scaffolding amplifying it? If yes, the fix requires BOTH PSM (change the belief) AND Skills Guide (remove the structure causing the belief). Both sessions showed compound failures — mechanical structures contradicting protocol beliefs.

**Why beliefs over rules:** PSM theory + empirical evidence. Dev protocol belief created 4 rounds of self-debate in thinking traces (83% of blocks). JA protocol rule was absent from thinking — not overridden, absent. Beliefs create internal arguments that persist under pressure. Rules create compliance targets that fold under pattern-matching.

### Step 4: Structural Cause Analysis

Skills Guide question: "What instruction or structure is causing or amplifying the unwanted belief?"

For thinking-layer fixes, this is the compound check — does mechanical scaffolding reinforce the wrong behavior? For doing-layer fixes, this IS the primary diagnosis — which structural element needs changing?

**Two mechanisms for one goal (behavior):**
- **PSM:** Change/add the belief → direct behavioral change
- **Skills Guide:** Remove/restructure the instruction causing the belief → indirect behavioral change by removing the cause

Behavior is always the frame. Mechanics serve behavior. A "mechanical fix" that doesn't serve a behavioral goal is noise.

### Step 5: Fix Execution

| Dimension | Artifact | Execution Tool |
|-----------|----------|----------------|
| **Thinking** | Protocol (CLAUDE.md) | PSM/CBT methodology → inline edit |
| **Doing** | Skill (workflow patterns) | skill-creator |
| **Knowing** | Skill (references/ folder) | skill-creator |
| **Compound** | Protocol + Skill | PSM first (thinking governs doing), then skill-creator |

For compound fixes: both changes in one commit for attributable testing in Session C. If you split across commits, you can't distinguish which change produced the behavioral shift.

### Step 6: Session C Criteria

Define one testable behavior before testing: "After [trigger], the AI should [desired action] instead of [old action]." Post to the issue before running Session C.

**Minimal attributable change:** The fix should be small enough that if behavior improves, you can attribute it to THIS change. Large fixes with multiple variables make Session C results uninterpretable.

### Remaining Hypotheses (Pass 5+)

**Undefined:** The evaluator-optimizer pattern from Anthropic's cookbooks (separate evaluator with binary verdict + structured feedback + memory of prior attempts) could formalize the verification step. Needs investigation.

**Undefined:** The "error cascade" finding (Where LLM Agents Fail, arxiv:2509.25370) — errors cascade across steps, which explains why single-instruction patches fail. How to formalize root cause attribution in the diagnosis phase?

**Resolved hypotheses (moved to Steps above):**
- ~~Which artifact type should fix this?~~ → Step 5 routing table
- ~~Self-diagnosis as Step 2?~~ → Yes, Step 2 (prompt format still Undefined)
- ~~How minimal can the change be?~~ → Step 6 (minimal attributable change)

### Part 4: Structural Patterns for Instruction Persistence

Patterns surfaced from Anthropic's engineering blog and academic research. These were convergence findings from Passes 1-3, predating the [Skills Guide PDF](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) (Pass 4). The Skills Guide carries more authority for skill-building patterns — its 5 workflow patterns (sequential orchestration, multi-MCP coordination, iterative refinement, context-aware tool selection, domain-specific intelligence) are Anthropic's consolidated guidance. The patterns below remain valid for persistence-specific concerns (state survival across context windows) but need re-evaluation against the Skills Guide where they overlap:

| Pattern | What It Does | Source |
|---------|-------------|--------|
| JSON state files | Resist inappropriate overwriting better than markdown | Effective Harnesses |
| Progress files as external memory | State survives context window refreshes | Effective Harnesses |
| Initializer agent with different prompt | First context window (setup) vs. subsequent (iteration) | Effective Harnesses |
| Targeted strong language | Reserve authority phrasing for true safety boundaries only ("unacceptable to remove tests"). **Claude 4.6 update:** blanket "YOU MUST" / "No exceptions" causes overtriggering — surgical deployment only | Effective Harnesses |
| XML tags as instruction anchors | Named behavioral rules (`<default_to_action>`) | Prompting Best Practices |
| Context continuation prompt | Tell Claude that compaction is happening | Prompting Best Practices |
| Git as state tracking | Claude 4.6 excels at discovering state from filesystem | Prompting Best Practices |
| Constitution (always-loaded doc) | Persistent conventions + orchestration protocols | Codified Context paper |

**Undefined:** Which patterns should manage-artifact v2 recommend by default? The new version should prescribe specific structural patterns per artifact type (Part 2), not treat all artifacts the same.

**Partially resolved:** The "Codified Context" paper documents a three-component architecture. Mapping to our architecture:

| Codified Context | Our Architecture | Notes |
|-----------------|-----------------|-------|
| Constitution (always loaded, ~660 lines) | CLAUDE.md (protocol) | Always in attention window. Governs thinking. |
| Specialized agents | Commands + Skills | Invoked on demand. Governs doing + knowing. |
| Cold-memory knowledge base | Hippocampus | Cross-project knowledge retrieval. |

**Undefined:** The paper's governing constraint is conciseness (~660 lines). Our CLAUDE.md files are longer. Does length impact thinking-layer effectiveness? Need empirical measurement.

### Part 5: The Session B Workflow (Validated)

Part 3 defines what to do per pattern. This part defines how to orchestrate a full Session B — the wrapper that makes Part 3 repeatable.

**The workflow:**

1. **Triage** — /improve-system reads observations from the release epic, clusters them into patterns, and produces a triage matrix. The matrix is the material to work from — without it, diagnosis is ad-hoc.

2. **Read artifacts** — Read the current state of every artifact being diagnosed (CLAUDE.md, SKILL.md, agent body, and their surrounding context). Observations describe symptoms. The artifact contains the cause. Diagnosing from observations alone produces symptom-level fixes that don't stick.

3. **Meta-diagnostic** *(iteration only)* — When a prior fix exists, ask: "Why didn't the last fix work?" Apply Step 1 (Dimensional Diagnosis) to the fix itself. This prevents repeating the same fix at the same altitude. First-time Session B skips this step — there's no prior fix to evaluate.

4. **Walk each pattern through the 6 steps** — One pattern at a time. Each step within each pattern is a discussion window between user and AI — not a checkbox. The user shapes the fix at every stop. Patterns that appear to dissolve under a prior pattern's fix are still walked — the walk validates the dissolution claim rather than assuming it. The user can trigger live empirical testing at any step when evidence for a fix direction is needed.

5. **Implement in-session** — The fix is written in the same session that diagnosed it. This is architecturally necessary: the context window accumulated during diagnosis IS the implementation context. Handing implementation to a separate session requires rebuilding diagnostic understanding from scratch — the most expensive failure mode. Artifacts are memory; the best time to write memory is when understanding is live.

6. **Session C criteria** — Step 6 per pattern produces testable behavioral statements. Session C is organic — observe during natural usage rather than deliberate testing. If the fix doesn't hold, new observations feed the next iteration's triage.

**Evidence — what goes wrong when steps are skipped:**

| Skipped Step | Failure (session) | Consequence |
|---|---|---|
| Read artifacts | Diagnosed from observation memory only (a7efac67) | 15 min triage produced symptom-level analysis. User redirected: "Are you actually aware of the prompt and the skill?" After reading, diagnosis found the three-layer context stack issue. |
| Read artifacts | Diagnosed L14 vs L46 from conversation memory (0667ce35) | Artifact could have changed since those sessions. User redirected: "Read current CLAUDE.md first." |
| 6-step discipline | Disposition walk with mechanical options (e856612d) | Had PSM dimensions upfront but abandoned them during the walk. Fix approach should depend on dimension, but dimensions weren't used to determine fix type. |

**Resolved (Pass 6):** Fourth dimension (examples/templates) — not a separate dimension. Output format is the fifth signal in Step 1. Examples are part of the writing principles layer (Part 6).

**Resolved (Pass 6):** Reference front-loading — the three reference documents (PSM, Skills Guide, Prompting Best Practices) map to artifact types, not steps. Each serves both diagnostic and writing roles. See Part 6.

**Undefined:** Pruning placement — should the practitioner prune the artifact BEFORE the diagnostic walk (generic audit: "does each line prevent a failure?") or AFTER diagnosis informs what to prune (targeted removal based on diagnostic findings)? Step 2 (Read artifacts) is a natural pruning opportunity. Step 3 (Meta-diagnostic) already prunes failed prior fixes. Broad Augment-style auditing vs targeted diagnostic pruning needs empirical validation from a real Session B.

---

### Part 6: Writing Principles for Instruction Artifacts

Part 3 defines the diagnostic lens — WHAT's wrong. This part defines the writing principles — HOW to write the fix artifact so it actually holds. Like SOLID for code, these principles govern the quality of instruction artifacts (CLAUDE.md, SKILL.md, hooks). All principles are design hypotheses pending empirical validation through Session B/C cycles.

The methodology has two distinct layers. The diagnostic lens (Part 3) figures out what's broken. The writing principles (this part) govern what comes out. A correct diagnosis with a poorly-written fix still fails.

#### Three References, Three Artifact Types

Each reference document governs a different artifact type and serves both diagnostic and writing roles for its domain:

| Reference | Artifact Type | Location | Diagnostic Role | Writing Role |
|-----------|--------------|----------|-----------------|--------------|
| [PSM](https://alignment.anthropic.com/2026/psm/) | Persona CLAUDE.md | `~/.claude/personas/{pos}/CLAUDE.md` | "What kind of person would behave this way?" | Write beliefs that condition persona traits |
| [Skills Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/complete-guide-building-skills-claude) | SKILL.md | `~/.claude/skills/{name}/SKILL.md` | "Which workflow pattern is broken?" | Progressive disclosure, 5 patterns, structure. Key tool: skill-creator |
| [Prompting Best Practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices) | Crosses both | Both locations | "Is reasoning correct but output wrong?" | Examples, WHY over WHAT, format control |

The [Skills Explained](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/skills-explained-prompts-projects-mcp-subagents) taxonomy backs the separation: Skills = procedural knowledge ("here's how to do things"), Projects = background knowledge ("here's what you need to know"). In Claude Code, the codebase IS most of the background knowledge — Claude can already see it. Project CLAUDE.md should only carry what Claude can't derive from the repo: business context, client info, system access, architecture decisions not obvious from code.

#### Separation of Concerns

Each artifact layer has one job. Fixing one layer doesn't require touching another.

| Layer | Concern | Artifact | Changes when... |
|-------|---------|----------|-----------------|
| **WHO** | Position identity, beliefs, persona traits | Persona CLAUDE.md | The position's identity or behavioral principles change |
| **WHAT** | Cross-position conventions | Global CLAUDE.md | A convention that applies to ALL positions changes |
| **WHERE** | Business context, system access | Project CLAUDE.md | Project facts or client context changes (TBD — CCI #646) |
| **HOW** | Procedures, workflows, steps | SKILL.md | The procedure for that specific task changes |
| **MUST** | Deterministic guarantees | [Hooks](https://docs.anthropic.com/en/docs/claude-code/hooks) | The enforcement logic or blocked pattern changes |

**Position-based, not project-based.** The persona layer carries identity — each position (JA, Developer, Dev Lead, System Engineer) has its own persona CLAUDE.md. Skills serve positions, not projects. Project CLAUDE.md is minimal context. See the [plugin architecture](https://github.com/WILSCH-AI-SERVICES/wilsch-ai-team-plugin) for the physical layout.

**Skills inherit persona from CLAUDE.md — they don't carry their own beliefs.** A skill executes WITHIN the persona context that CLAUDE.md provides. The ARCHIBUS Setup agent (session e1a2095a) demonstrated what happens without persona: mechanically correct but template-following behavior. The Developer protocol (pure beliefs in CLAUDE.md, procedures in skills) works nearly perfectly — proof that separation works.

#### Approved Best Practices

Four principles from [Prompting Best Practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices) (General Principles + Output/Formatting sections), applicable when writing any fix artifact:

| Principle | Source Section | Mechanism | Status |
|-----------|--------------|-----------|--------|
| **WHY over WHAT** | General Principles: "Add context to improve performance" | Providing motivation makes instructions generalizable. Same mechanism as PSM — beliefs generalize, rules don't. | Confirmed |
| **Use examples (3-5)** | General Principles: "Use examples effectively" | "Most reliable way to steer output format, tone, and structure." The cross-cutting fix for output drift. CCI #619 proof: belief + template compound. | Confirmed |
| **Tell what to do, not what not to do** | Output/Formatting: "Control the format of responses" | Positive framing avoids the compliance/negation trap. Connects to Pass 3 finding: persuasion principles deprecated for Claude 4.6 — aggressive negation causes overtriggering. | Confirmed |
| **Match prompt style to output style** | Output/Formatting: "Control the format of responses" | The artifact's own formatting is an implicit example of the output format it produces. | Hypothesis — needs validation |

**Deeper investigation needed:** Prompting Best Practices sections 3-5 (Tool Use, Thinking/Reasoning, Agentic Systems) contain named failure modes and structural patterns relevant to the methodology. Marked for future extraction passes.

#### Pruning Rubric

From practitioner evidence — [Augment](https://www.augmentcode.com/blog/your-agents-context-is-a-junk-drawer), [Vercel](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals), [Wortmann](https://www.wordman.dev/blog/agent-instructions). Convergent finding: instruction density degrades performance. Every line you add pushes something else out.

For each line in an instruction artifact, ask ([Wortmann's rubric](https://www.wordman.dev/blog/agent-instructions)):

- **Failure-backed?** Did this prevent a real issue? Connects to the observation system — a fix without a source observation is noise.
- **Tool-enforceable?** Could a [hook](https://docs.anthropic.com/en/docs/claude-code/hooks) handle this instead? Code is deterministic; language interpretation isn't ([Skills Guide](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/complete-guide-building-skills-claude), Troubleshooting section).
- **Decision-encoding?** Does this capture domain judgment the agent can't derive from code? Layer-dependent: persona beliefs vs project gotchas vs architecture decisions (deeper definition connects to CCI #646).
- **Triggerable?** Does it include a condition, not just a value statement? Connects to progressive disclosure — "When X, read Y" not just "See Y."

If it fails all four, delete it.

**Status:** Confirmed as writing principle. **Undefined** as diagnostic tool — using the rubric to diagnose WHY an instruction failed (not just whether to keep it) needs empirical validation.

#### Coding Principles for Instruction Artifacts (Design Hypotheses)

Five software engineering principles that transfer to instruction artifact design. All are design hypotheses pending empirical validation.

| Principle | Code Meaning | Instruction Artifact Meaning | Evidence |
|-----------|-------------|------------------------------|----------|
| **Separation of Concerns** | Each module has one job | Beliefs in persona CLAUDE.md, procedures in SKILL.md, enforcement in hooks | JA protocol mixed thinking+doing → 5 failed fix passes. Developer protocol separates them → works. |
| **Single Responsibility** | Each class has one reason to change | Each artifact changes for one reason only | See table above (WHO/WHAT/WHERE/HOW/MUST) |
| **YAGNI** | Don't build for hypothetical requirements | Don't write instructions for hypothetical failures. Wait for observations, then write. | Augment: "every line pushes something else out" |
| **Convention over Configuration** | Follow conventions, only configure deviations | Don't instruct what Claude already knows from pre-training. Persona level: standard roles don't need over-specification. Procedure level: known workflows don't need skills. | Vercel: "prefer retrieval-led reasoning over pre-training-led reasoning" — 100% pass rate. DHH: "convention set 20+ years of training data" |
| **Composition over Inheritance** | Compose behavior from small pieces | A position's behavior = persona + global + project + multiple skills, not one monolithic config | Prevents regression toward monolithic CLAUDE.md files |

---

## Source

- **Session (Pass 1):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/d2fa50bf-8e4c-4419-973b-acbc80736827.jsonl
- **Session (Pass 2):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/4ca3eeb3-c387-47ff-8a38-9e6d206e4ce4.jsonl
- **Evidence Sessions:** 9f14b021 (IITR #954, Session C1), 5459d299 (REKERS, Session C2) — dimensional diagnosis validation
- **External:** [Orchestration Workflow](https://github.com/shanraisshan/claude-code-best-practice/blob/main/orchestration-workflow/orchestration-workflow.md) — Command → Agent → Skill pattern
- **Session (Pass 3):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/d9255fc6-0f63-48c1-8813-02ee8e34e039.jsonl
- **Dev session analyzed:** 34d514fe-a8ee-4a46-87b9-9105b43d676f (DaveX2001, #1056 implementation — 83% belief-based thinking)
- **Interpretability transcript:** [How Models Think](https://youtu.be/fGKNUvivvnc) (Anthropic team — Jack, Emanuel, Josh)
- **Session (Part 5 — Session B Workflow):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/d0303262-fcde-48c0-8b34-d0ae953ae438.jsonl
- **Evidence conversations (Part 5):** 72aba94d (CCI #619 Session B — reference workflow), 0667ce35 (CCI #619 iteration), e856612d (failure — dimensions abandoned), a7efac67 (failure — artifact not read)
- **CCI #604 observations:** Last 20 comments — RESOLVE probing calibration, Session C pass validation
- **Anthropic sources (Pass 3):** [Adaptive Thinking](https://platform.claude.com/docs/en/build-with-claude/adaptive-thinking), [Best Practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices), [Context Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents), [PSM](https://www.anthropic.com/research/persona-selection-model) ([deep dive](https://alignment.anthropic.com/2026/psm/))
- **Academic (Pass 3):** [Codified Context](https://arxiv.org/abs/2602.20478) (283 sessions), [ContextCov](https://arxiv.org/abs/2603.00822) (723 repos), [Deliberative Alignment](https://arxiv.org/abs/2412.16339) (reasoning over specs in thinking)
- **Position Epic:** [CCI #600 — System Engineer](https://github.com/DaveX2001/claude-code-improvements/issues/600)
- **Release Epic Evidence:** [CCI #604 — JA Lifecycle Violations](https://github.com/DaveX2001/claude-code-improvements/issues/604) (47 comments, 5+ fix passes)
- **Current manage-artifact:** `~/.claude/skills/manage-artifact/SKILL.md` (created Dec 12, 2025)
- **Improve System Architecture:** `~/.claude/hippocampus/global/improve-system-architecture.md`
- **Contract Strategy:** `~/.claude/hippocampus/project/soloforce/contract-strategy-retainer-model-design.md` — Part 6
- **CCI Board Structure:** `~/.claude/hippocampus/project/soloforce/cci-board-structure-design.md` — §6
- **Session (Pass 4):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/6f79e8bf-9154-4f23-b2eb-cfc82083b9f5.jsonl
- **Mechanical Bible:** [The Complete Guide to Building Skills for Claude](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) (Anthropic, 30 pages)
- **Anthropic docs (Pass 4):** [Skills documentation](https://docs.anthropic.com/en/docs/claude-code/skills) — "Custom commands have been merged into skills"
- **Community reference (Pass 4):** [shanraisshan/claude-code-best-practice](https://github.com/shanraisshan/claude-code-best-practice) — Command → Agent → Skill orchestration, Billion Dollar Questions
- **ARCHIBUS witness (Pass 4):** Session e1a2095a — mechanical-only skill producing template behavior (CCI #629 comment, 2026-03-10)
- **CCI #604 observation (Pass 4):** PSM beliefs fold under internal convergence pressure — [comment](https://github.com/DaveX2001/claude-code-improvements/issues/604#issuecomment-4029318643)
- **Session (Pass 5 — creation mode):** a19f6dde-d274-4681-95f0-d9dc412e04ca — CBT parallel crystallized, /improve-system full diagnostic pass, compound fix (belief #1 + /probe structural removal)
- **Session (Pass 5 — improvement mode):** 24d94df5-c52d-438d-ba50-a13e4cce1c48 — ARCHIBUS Setup pass 3, system prompt contradiction diagnosis, belief #5 (attention scarcity) + SKILL.md simplification
- **CBT seed:** [CCI #629 comment](https://github.com/DaveX2001/claude-code-improvements/issues/629#issuecomment-4019317575) (2026-03-08) — PSM quote + "CBT could help"
- **CBT crystallization:** [CCI #629 comment](https://github.com/DaveX2001/claude-code-improvements/issues/629#issuecomment-4046795013) (2026-03-12) — observation → belief → restructured belief → generalization
- **Session (Pass 5 — extraction):** b0269523-6613-4303-9cf0-8c04d564b462 — JA extraction encoding Part 3 methodology into design doc
- **Session (Pass 6 — diagnostic + writing principles):** c10c3e10-1482-4e06-b1d5-769b6fdf50fd — Step 1 simplified (5 sequential dimensions), Part 6 writing principles layer, three-reference model, separation of concerns, coding principles transfer, pruning rubric
- **Practitioner evidence (Pass 6):** [Vercel — AGENTS.md outperforms skills](https://vercel.com/blog/agents-md-outperforms-skills-in-our-agent-evals), [Augment — junk drawer](https://www.augmentcode.com/blog/your-agents-context-is-a-junk-drawer), [Wortmann — agent instructions](https://www.wordman.dev/blog/agent-instructions)
- **Prompting Best Practices (Pass 6):** [Full document saved](/tmp/prompting-best-practices.md) via markitdown — JS-rendered page, read_website returns empty. Context7 used as fallback for partial content.
- **Conversation provenance (Pass 6):** b0269523 chunk 2-3 read via conversation-reader — traced Tests 2/3 origin to Pass 5 table construction (not evidence-backed)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

