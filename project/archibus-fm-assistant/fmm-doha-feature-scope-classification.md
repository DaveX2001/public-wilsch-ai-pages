---
publish: true
---

# FMM Doha Airport AI Feature Scope - Classification
[[client-archibus]]

**Source:** Mujahid's MoSCoW Requirements (2026-01-26)

**Timeline:** End of March 2026 (~2 months)

**Purpose:** Determine what can be offered in the FMM quotation

**Related:** [Original MoSCoW Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/mujahid-ai-requirements-moscow)

**Legend:** MH = Must Have, SH = Should Have, CH = Could Have (MoSCoW priority from Mujahid's original doc)

---

## IMPLEMENTED

### MH8: Work Requests via Chat

**What it does:** CAFM operator can create work requests through a chat interface instead of navigating through Bruce BEM menus. Operator says "Zone C needs cleaning" → AI finds the asset → creates the work request.

**Status:** Core MCP tool exists (`create_work_request`). Voice input (speech-to-text) would be a tangent add-on.

---

## TANGENT (buildable within 2-month timeline)

### MH1: Asset Classification

**What it does:** Normalize messy asset names to standard codes. Different clients call the same thing different names ("AHU-01", "Air Handling Unit 1", "A.H.U #1") → AI maps them all to a standard code like `AHU-01`.

**Status:** Tracked under #605 (Asset Code Generator). Feasibility assessment in progress with Mujahid as co-creator.

### MH2: Asset Hierarchy Creation

**What it does:** Auto-build parent-child relationships from flat data. Client provides a spreadsheet with building, floor, room, asset columns → AI structures this into a hierarchy (Building → Floor → Room → Asset).

**Status:** Same as MH1 - the asset code (e.g., `DUP-GF-RBL-AIRCO-001`) encodes the hierarchy. Tracked under #605.

### MH4: Intelligent Legacy Data Mapping

**What it does:** When migrating from an old CAFM system or Excel to Bruce BEM, AI reads both schemas and proposes a column-to-column mapping. Detects where fields don't align and flags mismatches.

**Status:** This IS the core scope of #373 (AI Bulk Data Entry Prototype). Already tracked.

### SH2: Inconsistent Naming Detection

**What it does:** Find spelling variations, abbreviations, and legacy codes in asset data. "Air Conditioning", "A/C", "AIRCON", "AC Unit" → flag these as the same thing, enforce naming standards.

**Status:** Same capability as MH1 - part of the asset code generation work (#605).

### SH6: Smart Work Order Classification

**What it does:** When creating a work request, AI auto-assigns trade, category, priority, and SLA from the description. Operator says "AHU not cooling" → AI infers: trade=HVAC, priority=high, SLA=4hr.

**Status:** Extension of implemented MH8. The LLM already understands context. Needs API team to expose additional API fields (trade, category, priority, SLA) on the CreateWorkRequest endpoint.

---

## SMART INSIGHT UMBRELLA

*Planned product direction. All require the same foundation: historical data access (API team) + analytics definition (what patterns to detect, what "good" looks like).*

### MH5: Predictive Maintenance

**What it does:** Predict equipment failure before it happens. "This AHU has run 8,000 hours and similar units typically fail around 10,000 hours" → alert before breakdown.

**Data needed:** Work order history, runtime data, condition data.

### MH6: Corrective Action Recommendations

**What it does:** When a work order comes in, suggest fixes based on past successful jobs. "Last 3 times this AHU type failed, the fix was replacing the filter and the part used was X."

**Data needed:** Work order resolutions with fix descriptions and parts used.

### MH7: AI Dashboards & Insights

**What it does:** Natural language KPI summaries. Operator asks "What went wrong this month?" → AI summarizes: "SLA compliance dropped 12%, mostly due to HVAC delays in Zone B."

**Data needed:** Work order + KPI data.

### MH9: SLA Breach Prediction

**What it does:** Identify work orders at risk of missing SLA before the deadline. "These 3 work orders have 2 hours left on SLA but no technician assigned" → proactive alert.

**Data needed:** Work orders with SLA deadlines and current status.

### MH10: Compliance Gap Detection

**What it does:** Flag missing inspections, expired certifications, overdue PPMs. "Fire inspection for Building A was due November, it's now January" → compliance alert.

**Data needed:** Inspection/certification schedules, PPM schedules vs completion records.

### SH3: PPM Recommendations

**What it does:** Recommend preventive maintenance frequency and task lists based on similar assets' performance. "Assets like this AHU perform better with quarterly filter changes instead of semi-annual."

**Data needed:** PPM history + breakdown correlation data.

### SH4: Failure Pattern Detection

**What it does:** Identify recurring fault patterns across assets or locations. "Zone C has had 5x more HVAC failures than other zones this quarter" → investigate root cause.

**Data needed:** Work order history across assets and locations.

### SH5: RUL Estimation (Remaining Useful Life)

**What it does:** Forecast when an asset should be repaired or replaced. "Based on age, maintenance history, and similar assets, this chiller has ~18 months of useful life remaining."

**Data needed:** Asset age, maintenance history, life expectancy benchmarks.

### CH2: Root Cause Analysis

**What it does:** When a problem occurs, suggest likely root causes from similar historical work orders. "In 80% of similar cases, the root cause was clogged filters."

**Data needed:** Work order resolutions with diagnosed causes.

### CH3: Demand Forecasting

**What it does:** Predict future maintenance or cleaning demand based on history. "Based on last year, Zone C cleaning requests spike 40% during summer. Plan staffing accordingly."

**Data needed:** Historical work order volumes by time period.

---

## UNTOUCHED (custom development, beyond 2-month scope)

### CH4: Auto-Generated Maintenance Procedures

**What it does:** AI drafts Standard Operating Procedures (SOPs) from historical best practices. "For AHU quarterly maintenance: 1. Check filters, 2. Clean coils, 3. Check refrigerant levels" - generated from what worked well on past jobs.

**Why untouched:** This is generative/execution (producing documents), not just analytics. Different capability class.

### CH5: Technician Recommendation

**What it does:** Recommend best technician for a job based on skills and availability. "For this HVAC repair, assign Ahmed - he's HVAC-certified, available, and nearest."

**Why untouched:** Requires workforce management data (skills, schedules, locations) - completely different data domain from FM operational data.

---

## UNCLEAR (need Mujahid clarification)

### MH3: Auto-Population of Mandatory Fields

**What it does:** When creating/importing an asset, AI infers values for trade, criticality, and PM procedure from context instead of manual dropdown selection.

**Question:** Is this during **bulk import** (AI fills these when importing client's Excel - same pattern as #373) or during **ongoing creation** (AI suggests these when someone manually creates a single asset)?

- If bulk → tangent to #373
- If ongoing → untouched (new `create_asset` capability)

### SH1: Duplicate Asset Detection

**What it does:** Identify when the same asset exists multiple times under different names or in different locations.

**Question:** Is this during **import** (detect "AHU-01" and "Air Handling Unit 1" as same asset before inserting - data quality step) or **ongoing scan** (periodically check live database for duplicates created over time)?

- If import → tangent to #373
- If ongoing → untouched (database hygiene tool)

### CH1: Manufacturer & Model Intelligence

**What it does:** Auto-fill manufacturer, model, and technical specifications from historical records or document parsing.

**Question:** Is this during **bulk onboarding** (parse equipment manuals/spec sheets once to fill all assets) or **ongoing inference** (suggest manufacturer from similar existing assets when adding new ones)?

- If bulk → tangent to #373 (PDF as input format)
- If ongoing → Smart Insight territory

---

## Summary

| Category | Count | FMM 2-Month Scope |
|----------|-------|-------------------|
| Implemented | 1 | Ready now |
| Tangent | 5 | Buildable by March |
| Smart Insight | 10 | Future direction |
| Untouched | 2 | Not in scope |
| Unclear | 3 | Depends on Mujahid answer |

---

## Phases

**Phase 1:** Align on feature scope with Mujahid

**Phase 2:** Get buy-in from Rein (API feasibility) and Miguel (business alignment)

**Phase 3:** Figure out the work and cost

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

