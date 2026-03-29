---
publish: true
date: 2026-01-03
---

# Session Type Model: Implementation vs Verification Separation

[[claude-code-architecture]]

## Problem Statement

Across 4 analyzed sessions, a consistent pattern emerged: `ac-verify` skill was "noted" during evaluation-clarity but **never actually invoked** during the Verify phase. The AI performed ad-hoc verification (curl tests, manual checks) without following the formal methodology.

**Root cause:** Same-session bias. When the AI has full implementation context, verification becomes confirmation-seeking rather than genuine testing.

## Evidence

### Conversation Analysis (4 Sessions)

| Session | Behavior | Outcome |
|---------|----------|---------|
| Conv 7 | 7 ACs completed, never announced Task Completed | User had to manually close issue |
| Conv 8 | Completed verification, didn't update DoD | User asked "did we update the dod's?" |
| Conv 9 | Fixed ac-verify timing docs, skipped Task Completed | "No tracking file" justification |
| Conv 10 | Noted "ac-verify for Verify phase", did inline curl tests instead | User caught: "you're verifying without using the skill" |

### External Research Validation

| Finding | Source |
|---------|--------|
| **40-60% quality improvement** from separate impl/review agents | Industry studies |
| Review agent should have **NO access to impl context** | Prevents confirmation bias architecturally |
| Multi-session is **2026 best practice** | Emerging workflow patterns |

**Key quote:**
> "When you use the same AI system to both write and review code, you're not getting a second opinion. You're getting confirmation bias at scale."

## The Model

### Two Session Types

| Session Type | Phases | Output | Ends With |
|--------------|--------|--------|-----------|
| **Implementation** | Req-Clarity → Impl-Clarity → Eval-Clarity → Execute → Commit | Code + DoD updated | Task Completed |
| **Verification** | AC-verify → PR review → (Fix or Merge) | verification.jsonl + PR approval | Issue closed |

### Implementation Session Flow

```
Requirements-Clarity → Implementation-Clarity → Evaluation-Clarity → Execute → Commit → Task Completed
        ↓                      ↓                        ↓              ↓         ↓
   Confidence ✓          Confidence ✓            Confidence ✓      Code    Push + announce
```

**Execute CAN include:**
- Basic sanity checks (does it run? does it compile?)
- Quick smoke tests
- Import verification

**Execute does NOT include:**
- Formal AC verification
- PR review
- Merge decisions

### Verification Session Flow

```
Onboarding → AC-verify skill → PR review → Fix issues OR Merge
     ↓              ↓               ↓              ↓
Fresh context  verification.jsonl  Review findings  Close issue
```

**Why separate session:**
- Fresh context = no implementation memory
- No anchoring to "how I built it"
- Genuine adversarial review possible
- Can use different model (Sonnet for review vs Opus for impl)

## Protocol Changes Required

### Remove from CLAUDE.md

The current "Verify" phase (lines 65-78) that sits awkwardly between Commit and Task Completed:

```
# REMOVE THIS:
**Verify: Evidence Creation**
After push, verify the implementation...
```

### Add to CLAUDE.md

Session type awareness in Task Lifecycle:

```markdown
### Session Types

**Implementation Session:**
- Phases: Req-Clarity → Impl-Clarity → Eval-Clarity → Execute → Commit → Task Completed
- Output: Working code, DoD items checked
- Does NOT include formal AC verification

**Verification Session:**
- Trigger: Start new session after implementation is pushed
- Phases: AC-verify → PR review → (Fix or Merge)
- Output: verification.jsonl, PR approval, issue closure
- Requires fresh context (no implementation memory)
```

### Skill Updates

**ac-verify skill:**
- Change discovery from `evaluation-clarity` to new trigger
- Add: "MUST be invoked in separate session from implementation"
- Add: "Do NOT verify work you implemented in this session"

## Why This Works

1. **Matches natural behavior**: User already separates PR review into different sessions
2. **Prevents confirmation bias**: Fresh context = genuine verification
3. **Clear mental model**: "Am I implementing or verifying?" - never both
4. **Validated by research**: 40-60% quality improvement documented

## Related Issues

- #146: Session end mechanism for testing evidence
- #182: DoD checkbox synchronization (related but different)

## Sources

- 4 conversation analyses (conv 7-10 in issue #146)
- [8 AI Code Generation Mistakes Devs Must Fix To Win 2026](https://vocal.media/futurism/8-ai-code-generation-mistakes-devs-must-fix-to-win-2026)
- [Why Your AI Code Reviews Are Broken](https://www.qodo.ai/blog/why-your-ai-code-reviews-are-broken-and-how-to-fix-them/)
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

