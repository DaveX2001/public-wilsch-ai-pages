---
publish: true
---

# Dec 18 Assets Session Summary
[[client-archibus]]

**Purpose:** Pattern Encoding methodology applied to Assets block
**Date:** Dec 18, 2025 | Duration: ~2 hours
**Participants:** Marius (facilitator), Rein, Miguel, Ali, Mujahid, Ian, Kaia

**Source:** [Full Transcript](https://drive.google.com/file/d/1AV8qPqE9Lps_o9Uw7b-EZrcTjBsHbihD/view)

---

## Data Entry/Collecting

### Chain 1A: Ad-hoc Entry

| Element | Content |
|---------|---------|
| **Pain** | User-based entry inconsistent, mistakes |
| **Workflow** | Single asset creation |
| **Best Practice** | Asset codes follow IFMA standard |

### Chain 1B: Bulk Entry

| Element | Content |
|---------|---------|
| **Pain** | Days of work, error-prone, inconsistent |
| **Workflow** | Upload from Excel/BIM + sanitization |
| **Best Practice** | IFMA standard + validation (before or after via survey) |
| **Note** | Also applies to initial deployment of Bruce/Archibus |

**Notes:**
- Auto-numbering: AHU001 → AHU002 → AHU003...
- IFMA is recommendation, not hard rule (can vary by company)

**Question:** How to handle non-digitalized data (paper-based)?

---

## Data Quality

### Chain 2: Parent-Child Relationships

| Element | Content |
|---------|---------|
| **Pain** | Parent-child hierarchy broken, relationships not systematized |
| **Workflow** | Auto-linking by asset category |
| **Best Practice** | SF 200 standards for equipment categories |

**Examples:**
- Sprinkler → Parent Board (firefighting)
- Filter → Split AC (HVAC)

**Notes:**
- Category-based linking, not individual assets
- Scale: 1000s of sprinklers link to parent board, not individually maintained

---

## Operations

### Chain 3: Survey/Asset Tracking (Data Currency)

| Element | Content |
|---------|---------|
| **Pain** | Asset data out of date within a week, labor-intensive to track |
| **Workflow** | AI push prompts + scan validation |
| **Best Practice** | Target FM-responsible person; QR/RFID for identification |

**Proposed Mechanism (Ian's idea - not decided):**

| Step | Description |
|------|-------------|
| 1. Trigger | Weekly prompt ("Monday update") |
| 2. Target | Designated FM-responsible person |
| 3. Action | AI prompts to scan/validate assets |
| 4. Compare | Check against system records |
| 5. Exception | Flag missing assets |

**Status:** Idea - needs validation

**Pattern reference:** Similar to [Anthropic Interviewer](https://www.anthropic.com/research/anthropic-interviewer) - AI proactively extracts knowledge from humans at scale

**Notes:**
- Partial data collection still valuable - reduces manual survey effort
- Adoption challenge: Cultural change required
- Target audience consideration: FM managers, not general staff
- Related pattern: Space utilization surveys (same push-prompt approach)

**Open Question:** Effort allocation and responsibility assignment

---

### Chain 4: Lifecycle/Status → Work Request

| Element | Content |
|---------|---------|
| **Pain** | Status changes require manual work request creation |
| **Workflow** | Status change → auto-generate work request |
| **Best Practice** | ? (not stated) |

**Open Decision:** AI vs Backend - needs discussion with Rein

---

### Chain 5: Meter → PM

| Element | Content |
|---------|---------|
| **Pain** | Manual PM scheduling based on readings |
| **Workflow** | Meter threshold → auto-trigger PM work order |
| **Best Practice** | BMS/SCADA integration |

**Open Decision:** AI vs Backend - Rein flagged this as potentially backend workflow

---

### Chain 6: Parts Suggestion

| Element | Content |
|---------|---------|
| **Pain** | Craftsmen order wrong parts, waste time going back and forth |
| **Workflow** | AI suggests parts based on asset type + problem |
| **Best Practice** | Constrain suggestions to compatible parts only |

**Mechanism:** AI validates part compatibility with asset model/version before suggesting

**Also includes:** Suggesting assets to add to work requests

---

### Chain 7: Reporting/Dashboard (Smart Insight)

| Element | Content |
|---------|---------|
| **Pain** | FM/Owner needs asset information on-demand |
| **Workflow** | Query → AI retrieves, aggregates, presents |
| **Best Practice** | ? (open) |

**Use Cases:**
- Compliance queries: "How many F200s per floor?"
- Status queries: "What's delayed? What's in bad condition?"
- Budget forecasting: "Cost to replace all bad-condition assets"
- Calibration tracking: "When was X last serviced?"

**Product:** Relates to Smart Insight (Miguel's correction: AI SHOULD do prediction/analytics)

---

**Related:**
- [Pattern Encoding Meeting Agenda](pattern-encoding-meeting-agenda.md)
- [Pattern Encoding ADR](adr-pattern-encoding-methodology.md)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

