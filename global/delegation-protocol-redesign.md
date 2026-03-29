---
publish: true
---

# Delegation Protocol Redesign — Agent Boundary & Scope
[[cci-619-investigation-authority]]

Design doc for redesigning the investigation delegation protocol in CLAUDE.md. Defines when, what, and how agents should be used across five delegation contexts.

---

## Problem Statement

The investigation delegation protocol in CLAUDE.md defines **when** to delegate (3+ tool calls with uncertain outcomes) but not **what** agents can do or **how much** they should return. This creates a vacuum: the AI defaults to "send the complete task to an agent," which produces the cheapest-path behavior — delegating execution, verification, and full content reading alongside investigation.

Triage across 6 conversations surfaced 12 instances of 4 sub-patterns:

| Pattern | Instances | Example |
|---------|-----------|---------|
| Agents as readers, not finders | 6 | "Fetch and return FULL content" — user corrected: "they're finders, not readers" |
| Verification delegated | 4 | AC verify sent to Task agent — user: "subagent is just for investigating" |
| Execution delegated | 1 | Production fix (SSH + sed + restart) sent to sub-agent |
| Reproducibility gap | 1 | Sub-agent findings not re-verifiable in main context |

The protocol was originally designed for **online research** (parallel web searches with summaries + attributed sources). It got applied to code search, documentation loading, verification, and execution without scope constraints for each context.

**Preconditions:**
- The delegation trigger (3+ uncertain tool calls) works and should be preserved
- Position protocols (Developer, JA, Dev Lead) are stable
- The /improve-system pipeline and observation system are mature
- The manage-artifact v2 design doc provides the diagnostic methodology (PSM + Skills Guide)

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | The delegation protocol encodes beliefs per delegation context that hold under pressure — the AI self-debates before delegating inappropriately, rather than following rules that get reinterpreted |
| **Success** | Each of the 5 delegation dimensions has a belief that enters native thinking and creates backpressure against wrong delegation. Agents are scoped to investigation/finding, main context handles all judgment and execution |
| **Done test** | In Session C, when the AI considers delegating verification, execution, or full content reading, does the thinking trace show self-debate? If YES → beliefs hold. If the AI delegates without self-debate → beliefs didn't enter thinking |

---

## Approach

### Part 1: Master Belief

> "Agents bring context, main thread executes. Parallelism lives in investigation, not execution."

This is the inverse of how most agent frameworks work. Others build multi-agent execution — many agents doing work in parallel. This model: many agents **investigating** in parallel, feeding one executor (main context) that works serially, visibly, with the user watching.

The general with scouts. Scouts fan out and bring intelligence. The general decides and acts. You don't send scouts to fight battles.

**Why a belief, not a rule:** The manage-artifact v2 evidence (CCI #604, 5 fix passes) shows beliefs create self-debate in thinking traces — the AI argues with itself before acting. Rules get reinterpreted or ignored. The Dev protocol belief created 4 rounds of self-debate in 83% of thinking blocks. A rule ("don't delegate execution") folds under pattern-matching. A belief ("my context is the execution engine") creates backpressure.

**Five dimensions derive from this one belief:**

| Dimension | Agents do | Main context does |
|-----------|----------|-------------------|
| Documentation | **Find** paths, validate links | **Read** content |
| Code | **Search** (specialized tools) | **Process** results |
| Verification | Nothing | **Witness** every step |
| Execution | Nothing | **Execute** visibly |
| Online research | **Discover** sources | **Process** findings |

### Part 2: Documentation — Agent as Finder

**Belief:** "A summary is an interpretation, not the source. Agents validate paths and return links. I read the content — because my judgment on the source is the deliverable, not an agent's interpretation of it."

**Three rationales:**
- **Economics:** Agents run cheaper models (Sonnet/Haiku) in fresh context windows. Don't burn Opus tool calls on searching — let cheaper models find.
- **Parallelism:** Main context can only do one thing at a time. Multiple finder agents in parallel = parallel search radius while main context focuses on processing.
- **Source truth:** Building a worldview on agent summaries = building on interpretations instead of source truth. For documentation, the entire point is reading the full content.

**Agent scope:**
- Validate paths/URLs exist
- Return working links
- Optionally: chunk large files for staged reading
- **Not:** read content, summarize, analyze, describe

**Evidence:** Session test — doc finder agent for "Ship with Confidence" returned validated paths from 4 locations (hippocampus, published pages, 2 GitHub repos) in 17s / 21K tokens. No content read, no summaries. Pattern held.

**Existing embodiment:** The conversation-reader skill already implements this pattern. Triage agents filter which chunks matter (finding). Main context reads chunks (processing). The belief exists in one skill but isn't generalized.

### Part 3: Code — Specialized Tools over Agents

**Belief:** "A specialized tool is always better than a general-purpose agent doing the same thing. Agents are for gaps where no tool exists."

For code search, purpose-built tools already exist. A general-purpose agent running `grep` inside a sub-context is a worse version of the native Grep tool. The self-reflect rule ("specific symbol → Grep, conceptual → semantic tool") is the right pattern — it just needs the right semantic tool standardized.

**Tool landscape (researched):**

| Tool | Type | Interface | When (from their docs) | Cost |
|------|------|-----------|----------------------|------|
| Grep (built-in) | Pattern/regex | Native | Known symbol, exact match | Free |
| WarpGrep v2 (morph-mcp) | Semantic sub-agent | MCP | "How does X work?", exploratory | $0.80/1M tok |
| auggie-mcp (Augment) | Semantic (cloud) | MCP | Q&A over codebase | 40-70 credits/query |
| mgrep (Mixedbread) | Semantic | CLI + Skill | NL grep replacement | TBD |

WarpGrep v2 runs 36 parallel grep/read calls in under 5s. #1 on SWE-Bench Pro. Its own docs prescribe the same split: "exploratory queries → WarpGrep, pinpoint keyword → raw grep."

**Undefined:** Which semantic code search tool to standardize on (WarpGrep vs mgrep vs auggie). Behavioral trigger for switching from Grep to semantic search. Whether auggie-mcp should be dropped (overlapping, community wrapper superseded by official Augment MCP).

### Part 4: Verification — Discovery Delegates, Conclusions Don't

**Belief:** "Discovery can be delegated — it's search. Conclusions cannot — they're judgment. A subagent's conclusion is a claim; the user needs evidence, not claims."

Verification means "does this meet the bar?" — AC verification, findings confirmation, any check where the conclusion affects decisions. The user must witness each step. A subagent reporting "verified ✅" is a claim, not evidence.

**Boundary:**
- **Discovery** (safe to delegate): "Does file X exist?" "What's in this directory?" "Which conversations have evidence?"
- **Conclusions** (must witness): "Does feature X meet the acceptance criteria?" "Is the API claim actually true?" "Does the deploy work?"

**Evidence:** Conv3 (ARCHIBUS) — subagent correctly found parentId was optional, but user still said: "Can you actually confirm this here in the main context agent if this is really true?" The conclusion changed the design. Even correct subagent findings needed witnessing.

**Scope:** AC verification (Dev Lead witness, ac-verify skill), findings confirmation, any "does this actually work?" sanity check. All stay in main context.

### Part 5: Execution — Always Main Thread

**Belief:** Derives directly from the master belief. Execution is the main thread's core function. The user hired the main context to process and execute visibly — not to manage agents that execute invisibly.

**Evidence:** Conv2 (Call2Tanss) — AI delegated SSH + sed + make restart to a Task agent. User rejected immediately: "never fix within subagent." The AI introspected: "Conflated investigation delegation with execution delegation."

**Philosophy:** Others build multi-agent execution (teams of agents doing work). This model inverts it: multiple investigation agents feed ONE executor. Execution is serial, visible, and in the user's view. Always.

### Part 6: Online Research — Behavior over Tools

**Belief:** "Research means evaluating whether I've found the answer. A researcher backtracks and refines. A fetcher downloads and moves on."

The delegation protocol was originally designed for this context — parallel web searches with summaries and attributed sources. The original pattern is sound. The gap is the distinction between **fetching** (downloading a page) and **researching** (evaluating, refining, backtracking).

**Fetcher landscape (tool layer):**

| Tool | Scope | Interface | Content types |
|------|-------|-----------|--------------|
| read-website-fast (current) | Single page | MCP | HTML only — fails on text/plain, PDF |
| Jina Reader | Single page | API + MCP | HTML, PDF, text/plain |
| Markdownify MCP | Single page | MCP | HTML, PDF, images, audio, DOCX, XLSX, PPTX (wraps Microsoft markitdown) |
| Firecrawl | Whole site | CLI + MCP | HTML + JS rendering, anti-bot |
| Cloudflare /crawl | Whole site | REST API | HTML → MD/JSON, edge-native (5 free jobs/day) |
| Tavily | Search + bulk fetch | MCP | LLM-optimized markdown |

**Researcher landscape (behavior layer):**

| Skill | Eval loop mechanism | Source |
|-------|-------------------|--------|
| 199-bio deep-research | 8-phase pipeline (Scope→Plan→Retrieve→Triangulate→Synthesize→Critique→Refine→Package), validation script, 4 modes (quick/standard/deep/ultradeep) | [GitHub](https://github.com/199-biotechnologies/claude-deep-research-skill) |
| Weizhena deep-research | Structured research with human-in-the-loop at every stage transition | [GitHub](https://github.com/Weizhena/Deep-Research-skills) |
| willccbb deep-research | Lightweight config — Brave Search + e2b sandbox + fetch MCPs, Claude drives the loop | [GitHub](https://github.com/willccbb/claude-deep-research) |
| glebis deep-research | Uses OpenAI Deep Research API, clarifying questions loop | [Smithery](https://smithery.ai/skills/glebis/deep-research) |
| VoltAgent subagents | 100+ subagents incl. research-analyst, search-specialist | [GitHub](https://github.com/VoltAgent/awesome-claude-code-subagents) |

**Key insight:** Claude IS the evaluation loop if the belief is right. The research skill (199-bio or custom) provides structure, but the sufficiency evaluation happens in Claude's native thinking — shaped by the protocol belief, not external tooling.

**Undefined:** Fetcher replacement for read-website-fast (Jina Reader vs Markdownify vs Firecrawl). Researcher skill selection (199-bio vs Weizhena vs willccbb vs custom build). Whether the evaluation loop should be encoded as a belief only or as a structured skill.

**Benchmarks discovered:**
- [DeepResearch Bench](https://huggingface.co/spaces/muset-ai/DeepResearch-Bench-Leaderboard) — 100 PhD-level tasks, live leaderboard
- [GAIA](https://huggingface.co/spaces/gaia-benchmark/leaderboard) — 450+ real-world tasks
- [BrowseComp](https://openai.com/index/browsecomp) — 1,266 factual web-nav questions

---

## Source

- **Issue:** [CCI #619 — Investigation Authority](https://github.com/DaveX2001/claude-code-improvements/issues/619)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/95f5c970-a2d2-4e07-8849-e4f245f06b24.jsonl
- **Triage conversations:** cbafca1d (CCI Board), 85783996 (Call2Tanss incident), aee03387 (ARCHIBUS extraction), a0f1d235 (JA proposal), e3be12f1 (JA proposal cont.), 91c2aad3 (Dev Lead witness)
- **Reference:** [Manage-Artifact v2 Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/manage-artifact-v2-design)
- **Reference:** [/improve-system Architecture](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/improve-system-architecture)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

