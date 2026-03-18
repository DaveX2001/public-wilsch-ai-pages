---
publish: true
---

# Archibus Bulk-Import Pipeline — Post-Mortem Analysis
[[archibus-post-mortem]]

Post-mortem of the Archibus bulk-import pipeline project (March 4–18, 2026). Covers scope expansion through witness ceremonies, runtime gap discovery, and corrective actions — including validation of IITR post-mortem correctives.

---

## Problem Statement

The Archibus bulk-import pipeline was designed and implemented in 3 days (March 4–6, 2026). All four pipeline issues (#1055 Foundation, #1056 Step 0+1, #1057 Step 2a, #1064 Step 2b) were witnessed, merged to staging, and producing correct mapping contracts. The pipeline logic was sound.

The problem surfaced afterward: each witness ceremony revealed that "demo-ready" required more than correct contract output. Test rubric #1075 — originally scoped as a simple golden-contract comparison — expanded through 11 ceremony findings (F1–F11) into a full staging deployment architecture: subdomain routing, server path conventions, deployment identity in CLAUDE.md, MCP discovery files, correct reference data, and runtime environment validation. Separately, the bridge tool infrastructure (#1154) had ACs designed, reviewed, and redesigned — then invalidated when Spike A (#1167) validated the actual runtime capabilities.

This is NOT a single catastrophic assumption failure like IITR's PageIndex collapse. The pipeline logic was correct throughout. The gap was between "pipeline produces correct output" and "system is demo-ready for the client" — and that gap was only discovered incrementally through live witness ceremonies, not defined upfront.

**Preconditions:**

* Spike primitive did not exist in the workflow — it was invented from the IITR post-mortem (analyzed March 17), during the Archibus timeline
* No mechanism to define the full acceptance surface before the first witness ceremony
* Pipeline designed for Claude Code; demo target was LibreChat — runtime gap unvalidated
* March 20 Rein meeting created time pressure to show progress

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Shared understanding of why the Archibus project took 12 days from "pipeline works" to "still not demo-ready," and what to change |
| **Success** | Scope expansion pattern documented, IITR corrective actions validated against Archibus evidence, new corrective proposed for acceptance surface definition |
| **Done test** | All corrective actions either implemented in the workflow or tracked as CCI issues — no remaining "we should do X" without a home |

---

## Approach

### Part 1 — Root Cause Chain

The failure cascaded through four stages:

**Stage 1: Pipeline built without runtime validation (March 4–6).** The JA designed the Setup Phase pipeline (Steps 0→1→2a→2b) through multiple extraction passes. David implemented all four issues in 3 days — prompt-based MCP tools tested in Claude Code locally. All witness ceremonies passed, all PRs merged to staging. The pipeline logic was correct. But it was designed for and tested in Claude Code — not the target demo runtime (LibreChat). No spike existed in the workflow at this point to validate whether the runtime could actually execute the pipeline.

**Stage 2: Acceptance surface defined after implementation (March 8).** The test rubric (#1075) was added to the design doc two days after the pipeline was already built and merged. Its original scope — a simple golden-contract comparison — was appropriate. Marius immediately simplified further, removing conversation shape evaluation to focus purely on contract output quality. But defining the acceptance surface after implementation meant that infrastructure requirements (deployment, routing, reference data quality) were not yet visible.

**Stage 3: Scope expansion through witness ceremonies (March 14–17).** Each Dev Lead witness ceremony surfaced infrastructure gaps that weren't part of the original test rubric scope:

| Finding | Gap |
|---------|-----|
| F1 | Missing subdomain — SSH tunnel required instead of `*.wilsch-deployment.com` |
| F2→F6 | Wrong instruction (move tracking files) faithfully executed → files deleted → correction required |
| F3 | BEM reference data written for API schema, not AI agent perspective |
| F4 | Asset type enum corrupted — old types polluting new values |
| F5 | Domain fix used wrong apex (`wilsch-ai.com` vs `wilsch-deployment.com`) |
| F8 | Repos in `/home/marius/` instead of `/home/shared/` per ADR 002 |
| F9 | CLAUDE.md missing deployment identity (container, port, path) |
| F10 | No `.mcp.json` for local MCP server discovery |
| F11 | Test data files missing from rebuilt container |

Each ceremony added new requirements to "demo-ready." The test rubric that started as "compare contract output" grew to encompass the full staging deployment architecture.

**Stage 4: Bridge tool ACs invalidated by late runtime validation (March 16–18).** The bridge tool infrastructure (#1154) had ACs designed, received 5 review comments from Marius, and was redesigned (AC2+AC4 merged, passes simplified). Then Spike A (#1167) validated LibreChat + FastMCP on March 17 — revealing that #1154's ACs were stale. Marius blocked the issue and declared the ACs should not be picked up as-is. The SA pass on #852 will create fresh implementation issues. The AC design and review work was waste — but the spike primitive that caught it was itself invented during the Archibus timeline from the IITR post-mortem.

### Part 2 — Process Failures

Three breakdowns compounded the root cause:

**Acceptance surface discovered, not defined.** The test rubric (#1075) was created after implementation and scoped to contract output quality only. This was appropriate for validating pipeline logic — but left infrastructure requirements (deployment routing, server conventions, reference data quality, deployment identity) invisible until the Dev Lead ran live witness ceremonies. Each ceremony surfaced 3–5 new findings, expanding the definition of "done" incrementally. The corrective: define the full acceptance surface — including infrastructure requirements — before the first witness ceremony, not through it.

**AC design before runtime validation.** The bridge tool (#1154) went through a full AC lifecycle: 5 ACs written, 5 review comments from Marius, AC redesign pass (merged AC2+AC4, simplified passes). Then Spike A validated what the runtime could actually do, invalidating the ACs. The spike primitive didn't exist when #1154 was created — it was invented from the IITR post-mortem during the Archibus timeline. In hindsight, AC design for the bridge tool should have waited until after runtime validation. This is the same "approved-but-invalid" anti-pattern from IITR, but with smaller blast radius — Marius blocked the issue before David could implement against stale ACs.

**Stale issue disposition unclear.** Pipeline issues #1057 and #1064 have merged PRs on staging but remain open 12 days later. The disposition is genuinely ambiguous: the pipeline logic passed witness, but the acceptance surface expanded to include PSM persona behavior and runtime deployment. Whether these issues should close (pipeline logic done) or stay open (full acceptance surface not met) reflects the same scope expansion problem — the definition of "done" kept growing after the original scope was satisfied.

### Part 3 — Corrective Actions

**Define acceptance surface before first witness ceremony.** The central Archibus-specific corrective. The test rubric should define not just WHAT to verify (contract output) but WHERE it runs (deployment), HOW it's accessed (routing, subdomains), and WHAT supporting infrastructure must exist (CLAUDE.md identity, reference data, MCP discovery). Use the witness ceremony to VALIDATE the acceptance surface, not to DISCOVER it. When the Dev Lead runs the ceremony and finds infrastructure gaps, those gaps represent missing upfront design — not ceremony findings.

**Spike primitive in the workflow — validated.** Spike A (#1167) is the first successful application of the spike primitive invented from the IITR post-mortem. It validated LibreChat + FastMCP in one day, caught the #1154 AC staleness before implementation started, and produced a running system that the Dev Lead could witness. The Archibus project straddles the transition: early pipeline work (March 4–6) followed the old process (decompose before validating runtime), Spike A (March 17) followed the new process (validate before decomposing further). The project itself is the before-and-after evidence that the spike primitive works.

**Trunk-first decomposition — validated by contrast.** The early pipeline decomposed 4 issues at once (#1055–#1064). When the runtime gap surfaced, all four were affected by scope expansion. Post-spike, the SA pass on #852 will create fresh implementation issues with runtime constraints already validated. The contrast demonstrates trunk-first: decompose only up to the proof point, validate, then decompose further.

**Stale issue disposition — still relevant.** Apply the IITR heuristic: minor pivot (ACs wrong, scope intact) → redesign ACs in place. Fundamental pivot (scope broken, comments polluted) → close and create fresh. For Archibus specifically: #1154 should be closed and recreated with post-spike ACs. #1057 and #1064 need explicit disposition — either close (pipeline logic is done) or redefine what "done" means now that the acceptance surface expanded.

**Persist working config — still relevant.** F9 (missing deployment identity in CLAUDE.md) and F11 (test data missing from rebuilt container) are instances of the same IITR pattern: config knowledge dying between sessions. When staging config works, write it to CLAUDE.md or compose immediately — including container names, ports, server paths, and test data inclusion rules.

---

## Source

* **Issue Chain:** [#1055](https://github.com/DaveX2001/deliverable-tracking/issues/1055) (Foundation), [#1056](https://github.com/DaveX2001/deliverable-tracking/issues/1056) (Step 0+1), [#1057](https://github.com/DaveX2001/deliverable-tracking/issues/1057) (Step 2a), [#1064](https://github.com/DaveX2001/deliverable-tracking/issues/1064) (Step 2b)
* **Test Rubric:** [#1075](https://github.com/DaveX2001/deliverable-tracking/issues/1075) — 11 ceremony findings (F1–F11)
* **Bridge Tool:** [#1154](https://github.com/DaveX2001/deliverable-tracking/issues/1154) — ACs invalidated by Spike A
* **Spike A:** [#1167](https://github.com/DaveX2001/deliverable-tracking/issues/1167) — LibreChat + FastMCP validated
* **PSM Persona:** [#1094](https://github.com/DaveX2001/deliverable-tracking/issues/1094) — behavioral improvement
* **Design Doc Hub:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852) — 78+ comments, full design history
* **IITR Post-Mortem (template):** [Published doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-post-mortem) | [#1169](https://github.com/DaveX2001/deliverable-tracking/issues/1169)
* **Post-Mortem Issue:** [#1179](https://github.com/DaveX2001/deliverable-tracking/issues/1179)
* **Session:** cc40a672-9d21-45a7-93ff-476e302a3800
