---
publish: true
---

# AI Requirements from Mujahid Khan (MoSCoW)
[[client-archibus]]

**Source:** FMM Doha Airport quotation requirements
**Date:** 2026-01-26
**Issue:** #607

---

## Must Have

### Asset Classification
Map assets to standard asset hierarchy with asset coding. Normalize naming variations:
- "AHU-01", "Air Handling Unit 1", "A.H.U #1" → AHU-01

### Asset Hierarchy Creation
Auto-build parent-child relationships:
- Building → Floor → Room → Asset
- Plant → System → Equipment → Component

### Auto-Population of Mandatory Fields
Infer missing values:
- Asset category
- Trade
- Criticality
- PM Procedure

### Intelligent Legacy Data Mapping
Map old CAFM/Excel fields to new system. Detect mismatches and missing relationships.

### Maintenance & Asset Management
Predict equipment failure based on runtime, condition, and historical breakdowns. Reduce unplanned downtime and extend asset life.

### Auto-Recommendation of Corrective Actions
Suggests fixes and spare parts used in past successful jobs.

### AI-Generated Dashboards & Insights
Natural language summaries of KPIs ("What went wrong this month?"). Plain-language reports for management and stakeholders.

### Raise Work Requests via Chat or Voice
Conversational interface for creating work requests.

### SLA Breach Prediction
Identify work orders at risk of missing SLA before it happens.

### Compliance Gap Detection
Flag missing inspections, certifications, or overdue PPMs.

---

## Should Have

### Duplicate Asset Detection
Identify duplicates across locations, sites, and imported files.

### Inconsistent Naming Detection
Find spelling variations, abbreviations, and legacy codes. Enforce naming standards automatically.

### PPM Recommendations
Recommend PPM frequency, task lists, and spare parts based on similar assets' performance.

### Failure Pattern Detection
AI identifies recurring fault patterns across assets or locations.

### Remaining Useful Life (RUL) Estimation
Forecast when an asset should be repaired or replaced based on life expectancy field.

### Smart Work Order Classification
Auto-assign trade, category, priority, SLA from description.

---

## Could Have

### Manufacturer & Model Intelligence
Auto-fill Manufacturer, Model, and other technical specifications using historical records and document parsing.

### Root Cause Analysis Suggestions
Recommends likely causes based on similar historical work orders.

### Demand Forecasting
Predict future space, cleaning, or maintenance demand based on history.

### Auto-Generated Maintenance Procedures
AI drafts SOPs based on historical best practice.

### Technician Recommendation
Recommends best technician based on skills and availability.

---

## AI Report Examples

### Performance Summary
**Query:** "Give me a summary of facility performance for this month."
**Output:** Overall SLA compliance, major risks, high-impact failures, cost vs budget snapshot, key recommendations.

### SLA Breach Risk
**Query:** "Which work orders are at risk of SLA breach in the next 48 hours?"
**Output:** At-risk work orders, root causes (resource, parts, access), preventive actions.

### Operational Risks
**Query:** "What are the top operational risks across my facilities?"
**Output:** High-risk assets, repeated failures, aging assets, compliance gaps.

### Work Order Analysis
**Query:** "Analyze work orders closed this month."
**Output:** Repetitive complaints, slow closures, high-cost jobs, underperforming trades.

### Asset Failure Analysis
**Query:** "Which assets failed more than twice in the last 90 days and why?"
**Output:** Likely root causes, recommended fixes, replace vs repair hints.

### PPM Effectiveness
**Query:** "Is preventive maintenance reducing breakdowns?"
**Output:** PPM compliance vs breakdown trend, assets with ineffective PPMs.

### Critical Asset Health
**Query:** "Show me the health status of critical HVAC assets."
**Output:** Health score, failure probability, remaining useful life (RUL), business impact.

### Asset Replacement Planning
**Query:** "Which assets should be replaced in the next 12 months?"
**Output:** Cost trend, downtime impact, risk score.

### Resource Planning
**Query:** "Do I have enough technicians for next quarter?"
**Output:** Job volume, skill demand, peak workload periods.

### Cost Analysis
**Query:** "Where are we overspending on maintenance?"
**Output:** High-cost assets, vendor inefficiencies, contract gaps.

### Vendor Performance
**Query:** "Which vendors are not meeting SLA or quality targets?"

### SLA Root Cause
**Query:** "Why did SLA compliance drop last month?"

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

