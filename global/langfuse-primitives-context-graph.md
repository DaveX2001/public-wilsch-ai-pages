---
publish: true
---

# Langfuse Primitives for Context Graph

[[claude-code-architecture]]

**Status:** Idea (not a decision)
**Related:** [Context Graph Langfuse ADR](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/context-graph-langfuse)

---

## Overview

Mapping Langfuse observability primitives to Context Graph requirements. This is exploratory - identifying which primitives fit which needs.

---

## Primitives

### Traces

**What:** Top-level entity representing a single execution flow.

**Context Graph fit:** Each agent decision = one trace. Contains overall input/output and nested observations.

**Docs:** https://langfuse.com/docs/tracing-features/traces

---

### Sessions

**What:** Groups related traces together (multi-turn conversations, workflows).

**Context Graph fit:** Group all decisions for an issue (`session_id = "issue_377"`) or by date.

**Docs:** https://langfuse.com/docs/tracing-features/sessions

---

### Observations (Spans, Generations, Events)

**What:** Nested units within a trace.
- **Spans** - Generic work units (non-LLM)
- **Generations** - LLM calls with model/usage tracking
- **Events** - Point-in-time occurrences

**Context Graph fit:** Store long content (interpretation, alternatives, context_snapshot) in span input/output - no length limit.

**Docs:** https://langfuse.com/docs/tracing-features/observations

---

### Metadata

**What:** Custom key-value pairs attached to traces/observations.

**Context Graph fit:** Short queryable fields - `what`, `why`, `source`, `decision_type`.

**Limitation:** Values max 200 characters.

**Docs:** https://langfuse.com/docs/tracing-features/metadata

---

### Tags

**What:** Strings for filtering/organizing traces.

**Context Graph fit:** Quick filtering - `["decision", "sdk-agent", "match_issue"]`.

**Docs:** https://langfuse.com/docs/tracing-features/tags

---

### Users (user_id)

**What:** Built-in field for user/entity identification.

**Context Graph fit:** Use for client filtering (`user_id = "IITR"`).

**Docs:** https://langfuse.com/docs/tracing-features/users

---

### Scores

**What:** Evaluations attached to traces (numeric, categorical, boolean).

**Context Graph fit:** Track decision confidence, outcome quality.

**Docs:** https://langfuse.com/docs/scores/overview

---

## Potential Mapping

| Context Graph Need | Langfuse Primitive | Why |
|--------------------|-------------------|-----|
| Each decision | Trace | One trace per decision |
| Group by issue | Session | `session_id = "issue_377"` |
| Filter by client | user_id | Built-in, queryable |
| Quick filtering | Tags | `["decision", "match"]` |
| Short fields (what, why) | Metadata | Queryable, 200 char limit |
| Long fields (interpretation) | Span input/output | No length limit |
| Decision confidence | Scores | Numeric evaluation |

---

## Query Example (Idea)

```python
# "How did we handle similar decisions for IITR?"
traces = langfuse.api.trace.list(
    user_id="IITR",
    tags=["decision"],
    session_id="issue_377"
)
```

**Docs:** https://langfuse.com/docs/query

---

## References

- [Langfuse Tracing Overview](https://langfuse.com/docs/tracing)
- [Langfuse Data Model](https://langfuse.com/docs/tracing-features/data-model)
- [Query via SDK](https://langfuse.com/docs/query)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

