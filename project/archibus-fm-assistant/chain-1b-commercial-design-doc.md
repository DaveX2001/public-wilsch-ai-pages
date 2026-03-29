---
publish: true
---

# AI Bulk Data Entry: From Messy Excel to Clean Import

[[client-01_archibus]]

A commercial overview of Wilsch AI's bulk data entry pipeline for Bruce BEM —
turning partner data reformatting from high-effort, low-margin work into an
AI-automated process.

---

## The Problem You Already Know

Every new client onboarding starts the same way. A partner receives an Excel
file — sometimes clean, usually not — containing hundreds of assets that need
to enter Bruce BEM. Buildings, floors, rooms, equipment. Each column named
differently. Each client's hierarchy structured their own way.

Someone on the team opens the spreadsheet and starts mapping. Column by column.
Row by row. Figuring out which fields match the BEM schema, which values need
translating, which rows don't belong. It takes hours. Sometimes days. And when
something goes wrong — a wrong enum, a missing parent location, a field that
doesn't validate — it means re-doing the work.

This is high-effort, low-margin work. The kind of work that keeps experienced
implementers tied to spreadsheets instead of doing what they're good at:
configuring systems and solving client problems.

**And every new client brings another spreadsheet.**

---

## What Your Partners Spend Their Time On

The frustration isn't the complexity — it's the repetition:

- _"Every client sends a different format. We start from scratch every time."_
- _"I know the BEM schema by heart, but matching 30 columns manually still takes a full day."_
- _"When one field fails validation, we re-import the entire batch. Then we find the next error."_

These aren't edge cases. This is the default workflow for every background data
import. The same 35 schema fields. The same hierarchy levels. The same
validation rules. Only the column names change — and that's exactly the part
an AI can handle.

---

## We Ran Your Data

We took a real CAFM asset spreadsheet — the kind partners receive from clients
every week — and fed it to the pipeline.

The AI read the column headers, inferred the building hierarchy from the data,
and mapped every field to the BEM schema. Seven asset types. Two status values.
Location hierarchy four levels deep. It built the import JSON, submitted it to
Bruce BEM's validation endpoint, received structured errors — and fixed them
itself.

No manual intervention. No re-mapping. No second attempt by a person.

_On March 24th 2026, Rein confirmed the imported data was correct in BruceBEM._

---

## How It Works

1. **The AI understands the hierarchy** — it reads the spreadsheet, detects
   which columns represent locations (campus, building, floor, room), and
   proposes the hierarchy structure. The implementer confirms with one click.

2. **The AI maps columns to the BEM schema** — 35 fields, each matched
   semantically. Asset types, status values, and enums are resolved
   automatically against BruceBEM's reference data. The implementer reviews
   the mapping contract.

3. **The AI imports and self-corrects** — it builds the nested JSON, submits
   to BruceBEM's API, and when validation errors come back, it reads the
   structured error response and fixes the data. No human in the loop for
   error correction.

Upload. Confirm. Import. That's the partner's workflow.

---

## What Changes

The spreadsheet that used to take a day takes minutes. The validation errors
that used to mean rework get resolved automatically. The mapping that used to
require a BEM expert now happens through AI inference.

Partners focus on what matters — system configuration, client relationships,
solution design — while the pipeline handles the data grunt work.

Every new client onboarding becomes the same simple process: upload the
spreadsheet, confirm the AI's mapping, and import. The format doesn't matter.
The column names don't matter. The hierarchy depth doesn't matter. The AI
adapts.

**The spreadsheets stop being a bottleneck. The onboarding accelerates.**

---

## What's Next

See it work on your data. Bring a client spreadsheet — any format, any column
naming, any hierarchy depth — and watch the pipeline process it live.

---

_Wilsch AI Services — AI that understands your domain, not just your data._

---

## Source

- **Demo:** Rein demo 2026-03-24 — first live AI bulk import, data integrity confirmed in BruceBEM
- **Design Docs:**
  - [Chain 1B Alignment Document](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-client)
  - [Chain 1B Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design)
  - [Step 1+2 Setup Phase](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/step-1-2-setup-phase-design)
  - [Step 3 Import Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **Transcript:** [Grooming 2026-03-13](https://app.fireflies.ai/view/01KKKMC8YQP6G8M1R69YPQNFN7) — commercial doc assignment
- **Demo Transcript:** [Rein Demo 2026-03-24](https://app.fireflies.ai/view/01KMFZ2BS660P300ASWPJHG4FR)
- **Template Reference:** [Omega Commercial Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/WILSCH-AI-INTERNAL/omega-commercial-design-doc)
- **Epic:** [#373 — AI Bulk Data Entry](https://github.com/DaveX2001/deliverable-tracking/issues/373)
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/6700225e-7f44-4284-b2a4-3313be138d51.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

