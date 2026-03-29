# AC and DoD Framework
[[claude-code-architecture]]

---
publish: true
date: 2026-01-18
source: Rubber-duck session analyzing #377, #89, #314, #307
---

## 1. Core Concepts

**Definition of Done (DoD)**, **Acceptance Criteria (AC)**, and **Witness** are separate concepts:

| Concept | Purpose | Question Answered |
|---------|---------|-------------------|
| **DoD** | What to build | "Does it exist?" |
| **AC** | How to verify system state | "Is the system state correct?" |
| **Witness** | How to validate the experience | "Does it work when a human sees it?" |

**Key insight:** Three parallel gates. DoD, AC, and Witness must each pass independently. AC and Witness test different things — AC catches *specified* failures (system state via automation), Witness catches *unspecified* failures (experience via human judgment).

**Empirical observation:** ACs in practice test system state — counts, thresholds, file existence, API responses — not user-observable behavior. Reviewing ACs across IITR (6 issues) and Archibus (4 issues) revealed that verification commands are `curl | jq`, `ssh ... jq`, `docker ps | grep`, not experiential checks. The GWT best practice says "focus on user-observable outcomes," but real ACs are sophisticated sanity checks for specified correctness. This is not wrong — it confirms that ACs and Witness serve genuinely different purposes, not different depths of the same purpose.

**Verification vs Validation (ISO 9000):**
- **Verification** (AC): "Did we build it right?" — AI executes GWT scenarios, records to `verification.jsonl`
- **Validation** (Witness): "Did we build the right thing?" — AI guides human through experience, records to witness section in `tracking.md`

**The teaching test:** The AI that built the feature must prove understanding by teaching the human to experience it step by step. If the AI cannot guide the human to see it work, the AI's understanding of what it built has gaps. Mismatch between AI's prescription and human's observation = feedback signal.

**Source:** [Scrum.org](https://www.scrum.org/resources/blog/what-difference-between-definition-done-and-acceptance-criteria)

---

## 2. AC Scoping Principle

> **AC = User-observable behavior at the boundary where value is delivered**

- Focus on WHAT users observe, not HOW system works internally
- Each feature owns its observable boundary
- Internal effects become observable in downstream features

**Decision tree:**
- Is this describing HOW it's implemented? → Too narrow (implementation detail)
- Is this describing WHAT value users receive? → Correct scope
- Is this describing EVERYTHING the feature does? → Too broad (split the story)

---

## 3. DoD Dependency Analysis

**Pre-phase before AC enumeration for complex DoD items.**

### When to Use

Required when DoD has:
- 3+ items
- Items spanning different layers (database → API → UI)
- Implicit dependencies ("X requires Y first")

### Abstract Layers

| Layer | Code Example | Non-Code Example |
|-------|--------------|------------------|
| **Foundation** | Database, migrations | Research, data gathering |
| **Structure** | API endpoints, services | Process design, templates |
| **Interface** | UI components, flows | Deliverables, presentations |

### Analysis Pattern

1. **Map:** Place each DoD item in its layer
2. **Mark:** Note existing (✓) vs new (NEW) using Scout output
3. **Group:** Create passes where each pass has no internal dependencies
4. **Select:** User chooses which pass to define ACs for

### Pass Selection

Present via AskUserQuestion:
- Pass 1: Foundation items
- Pass 2: Structure items (depends on Pass 1)
- Pass 3: Interface items (depends on Pass 2)
- All items: Full scope

Selected pass filters which DoD items flow to AC enumeration.

### Origin

Pattern emerged from rubber-duck session on 2026-01-04 (issue #420). User feedback: "this pattern has been really nice to me" - provides ownership over scope before diving into AC details.

---

## 4. AC Enumeration Method

**Step 1:** List user choices (inputs)
```
Create Video Step 1:
- script_type: [youtube_url, custom_script, title_gen]
- autopilot: [true, false]
```

**Step 2:** Enumerate combinations
```
3 script_types × 2 autopilot_modes = 6 paths
```

**Step 3:** Map each path to observable outcome
```
(youtube_url, autopilot=false) → Step 2
(youtube_url, autopilot=true)  → Queue
```

**Step 4:** Each distinct outcome = 1 AC

---

## 5. Path Prioritization (Pareto)

| Tier | Path Type | Rule | Coverage |
|------|-----------|------|----------|
| **1 (Always)** | Happy | All valid input combinations | ~80% of users |
| **2 (High value)** | Sad | Critical field validation only | Common errors |
| **3 (Selective)** | Error | Data-mutating operations only | High-impact failures |

**What to skip (80/20):**
- Sad paths for optional fields
- Error paths for read-only operations
- Edge cases requiring complex setup

---

## 6. Given-When-Then Format

### Best Practices

| Do | Don't |
|----|-------|
| Use business language | Reference UI element IDs |
| Focus on observable outcomes | Test database state directly |
| Keep <10 steps per scenario | Include implementation details |
| Be specific ("within 3 seconds") | Be vague ("fast", "good") |
| One behavior per scenario | Test multiple behaviors together |
| Write declaratively | Write imperatively ("click", "verify") |

### Example

**Bad:**
```gherkin
Given the database is running
When I POST to /api/auth with username="test"
Then a JWT token should be generated
```

**Good:**
```gherkin
Given I am on the login page
When I enter valid credentials
Then I should see the dashboard
And I should see a welcome message
```

**Sources:** [Cucumber](https://cucumber.io/docs/bdd/better-gherkin/), [Martin Fowler](https://martinfowler.com/bliki/GivenWhenThen.html)

### Anti-Patterns to Avoid

| Anti-Pattern | Why It's Wrong | Fix |
|--------------|----------------|-----|
| Testing database state | Not user-observable | Test user-facing outcome |
| UI element IDs in Given/When | Implementation detail | Use business language |
| "Fast", "good", "properly" | Subjective, untestable | Use specific metrics ("< 3s") |
| Multiple behaviors per AC | Hard to isolate failures | Split into separate ACs |
| Imperative style ("Click X, verify Y") | Procedure, not behavior | Declarative ("When X, Then Y") |
| "AI reasons correctly" | Unobservable | Observable output (file, response) |
| Ephemeral UI (toasts, spinners) | Hard to capture | Reframe to console.log or API response |

---

## 7. Storage: Local Tracking File

### Location
```
.claude/tracking/issue-{N}.md
```

### Template
```markdown
# Issue #{N}: {title}

## Definition of Done
- [ ] Item 1
- [ ] Item 2

## Acceptance Criteria
### AC1: {scenario name}
Given: ...
When: ...
Then: ...

## Human Witness
<!-- Steps+Expected written during design. Witnessed filled post-implementation via /witness session. -->

### HW1: {scenario name} (from AC1)
**Steps:** {what to do — URL, action, navigation}
**Expected:** {what you should see}
**Witnessed:** {filled by developer during /witness session}
```

### Why local file over GitHub issue body?

| Aspect | Issue Body | Local File |
|--------|-----------|------------|
| Editing | Full body rewrite | Direct file edit |
| Diff visibility | Hidden in "edited" | Git diff shows changes |
| Branching | N/A | AC can branch with worktree |
| Versioning | Manual | Git history |

### Rules
- **Updates:** Edit file → commit → push immediately
- **Issue body:** Links to GitHub URL (not local path)
- **Versioning:** Git history = version history (no changelog needed)

---

## 8. Skill Responsibilities

| Skill | Responsibility |
|-------|---------------|
| **deliverable-tracking** | Create issue + tracking file (with initial DoD) |
| **onboarding** | Verify file exists, check for AC, prompt rubber-duck if missing |
| **rubber-duck** | Where DoD/AC is refined through conversation |
| **ac-create** | AC methodology - 7-step I/O pipeline |

### ac-create Workflow (7 Steps)

| Step | Name | Input | Output |
|------|------|-------|--------|
| **1** | Scout | Issue body, codebase | Context understanding |
| **2** | Analyze | Scout findings | Structured DoD + passes |
| **3** | Enumerate | DoD items | Raw ACs (choices × outcomes) |
| **4** | Format | Raw ACs | Given-When-Then (business language) |
| **5** | Refinement Gate | Draft ACs | Refined ACs + Testability |
| **6** | Save | Final ACs | Tracking file |
| **7** | Inform | Link | User confirmation |

**Key step:** Step 5 (Refinement Gate) runs 4 checks:
1. Observable Output
2. Test Data Availability
3. Design Questions
4. Blocking Dependencies

### System Workflow

```
deliverable-tracking (Issue Creation)
└── Creates issue + tracking file with DoD
    ↓
onboarding (Session Start)
├── Verifies tracking file exists
├── Checks for AC section
└── IF no AC → prompts: "Run /rubber-duck to create AC?"
    ↓
rubber-duck (AC Creation)
├── Invokes ac-create skill (7-step pipeline)
├── Defines Given-When-Then scenarios
└── Updates tracking file
    ↓
worktree (Implementation)
└── Implements against DoD + AC
```

### Migration (Existing Issues)

When onboarding encounters an issue without a tracking file:
1. Prompt user: "Tracking file missing. Create from issue DoD?"
2. If yes: Pull DoD from issue body → create file → commit → push
3. Continue to AC check

---

## 9. Timing

| Element | When Created | Can Be Refined |
|---------|--------------|----------------|
| **DoD** | Issue creation (deliverable-tracking) | Yes (rubber-duck) |
| **AC** | Before implementation (rubber-duck) | Yes (as understanding grows) |

**Key insight:** 40% of early-written AC need revision. JIT creation (just before implementation) produces higher quality AC.

---

## 10. Issue Body Format

### New Format (Minimal)

```markdown
## What?
{description}

## Why?
{motivation}

## Tracking
[DoD + AC](https://github.com/{owner}/{repo}/blob/main/.claude/tracking/issue-{N}.md)

## Notes
{optional context}
```

All details live in tracking file. Issue body is a pointer.

---

## 11. Sources

- [Scrum.org: DoD vs AC](https://www.scrum.org/resources/blog/what-difference-between-definition-done-and-acceptance-criteria)
- [Cucumber: Better Gherkin](https://cucumber.io/docs/bdd/better-gherkin/)
- [Martin Fowler: Given-When-Then](https://martinfowler.com/bliki/GivenWhenThen.html)
- [Mike Cohn: Testing Pyramid](https://www.agilecoachjournal.com/2014-01-28/the-agile-testing-pyramid)
- [AltexSoft: AC Best Practices](https://www.altexsoft.com/blog/acceptance-criteria-purposes-formats-and-best-practices/)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

