---
publish: true
---

# Bookkeeper System — Design Doc

[[life-vision]]

Design doc for the Bookkeeper position's operational systems at Wilsch AI Services. Defines how financial operations (invoicing, expenses, compliance, payments) flow through Xolo — from manual founder-operated processes today toward AI-managed operations described in the Position Agreement.

---

## Problem Statement

All bookkeeping runs on founder knowledge. Invoicing, expense tracking, compliance verification, and payment reconciliation happen manually through Xolo with no documented process. When the founder is unavailable, financial operations stop.

Four prior attempts to automate expense management (#292, #518, #519, #621) failed because each required the founder's personal portal access — the bottleneck that no amount of automation design could route around. Each attempt was reframed before execution completed, producing strategy churn without operational progress.

The Position Agreement defines a target state: a Bookkeeper managing AI agents that handle routine financial operations. The current state is a founder doing everything manually. No bridge exists between the two.

**Preconditions:**
- Estonian e-resident entity operating through Xolo (all financial ops flow through Xolo)
- Xolo has no public API — automation requires browser automation (Chrome DevTools) or email forwarding
- Founder currently performs all bookkeeping manually — no AI agents operational
- One successful AI-assisted invoice prototype exists (Abtmayr-Reichert, Chrome DevTools)
- Multiple clients with different invoicing needs (T&M, retainer, fixed-price defined but not all active)
- Compliance rules being discovered empirically (Leistungszeitraum, reverse-charge VAT, invoice dating requirements)
- All current clients are EU-based (German) — reverse-charge VAT applies universally
- Portal access for vendor expenses remains bottlenecked on founder

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Every bookkeeping operation has a documented, repeatable process — from invoice creation through payment reconciliation. The documentation is detailed enough for a non-expert to follow and for AI agents to eventually execute. |
| **Success** | All bookkeeper workflows identified in the PA's Work Listing have corresponding process documentation. Each process defines: inputs, steps, compliance checks, and output. The founder can hand the doc to someone and say "follow this." |
| **Done test** | Can I write a meeting agenda with open design questions about the bookkeeper system? If NO → design is complete. |

**Audience:** The company — anyone who needs to understand how bookkeeping operates at Wilsch AI Services.

**Autonomy target:** Progressive. Stage 1: AI proposes, human approves. Stage 2: Autonomous for proven-reliable tasks. Trust earned through demonstrated accuracy.

---

## Approach

### Part 1: Invoice Creation

The primary bookkeeper output. Creates invoices in Xolo for client billing.

**Known invoice types:**

| Type | Status | Example |
|------|--------|---------|
| T&M + pass-throughs | Active | Casa Curata UG — 8h consulting + Namecheap domain/hosting |
| Retainer | Active (starting April) | UWI — 5h/week base + success component |
| Fixed-price | Defined, not yet invoiced | Scoped deliverable, no hourly reporting |
| Hourly (legacy) | Being phased out | "Structurally incompatible with AI-native delivery" |
| Inbound freelancer | Active | Zineb — received, not issued |

**Undefined:** Full invoice type catalog — confirm which types are actively invoiced and whether each requires a different workflow. → *Meeting agenda*

**Invoice creation workflow (T&M — the documented type):**

| Step | Action | Input Source |
|------|--------|-------------|
| 1 | Gather hours | Timesheets (Google Sheets / CSV / XLSX) |
| 2 | Gather pass-through costs | Gmail receipts (Namecheap, etc.) |
| 3 | Look up contract terms | Hippocampus (hourly rate, billing terms) |
| 4 | Look up or create customer in Xolo | Xolo Web UI |
| 5 | Fill invoice form (line items, quantities, rates, discounts) | Xolo Web UI |
| 6 | Add comments (Leistungszeitraum, tax references, contract ref, timesheet note) | Manual |
| 7 | Compliance check before sending | Compliance checklist (see Part 3) |
| 8 | Send to client | Xolo |
| 9 | Share timesheet via Google Sheet | Google Sheets link to client |

**Timesheet format (current → target):**

| Current (2 columns) | Target (3 columns) |
|---------------------|---------------------|
| Hours, Description | Date, Hours (rounded 15min), Description |

Template: [Timesheet - Casa Curata UG](https://docs.google.com/spreadsheets/d/1toFbIRmby-a3UDHpONR_dp91af2mztdYBMWx0IewmnI/edit)

### Part 2: Expense Management

Collecting, categorizing, and forwarding expense receipts to Xolo for compliance.

**Expense sources:**

| Source | Method | Status |
|--------|--------|--------|
| Email-based vendors | Forward receipt to Xolo | Working |
| Portal-only vendors (4) | Manual download from vendor portal | Bottlenecked on founder access |
| Personal Gmail | Forward from Marius's personal email | Requires founder action |
| Wise bank feed | Payment data | Available |

**The portal access bottleneck:**

Four vendor portals require Marius's personal login credentials. This was the root cause of all prior automation failures (#292, #518, #519, #621). No amount of automation design resolves "someone must log into 4 vendor portals."

**Design constraint:** The bookkeeper system must either:
- (a) Transfer portal access to the bookkeeper operator, or
- (b) Establish a recurring minimal-touch routine for Marius to batch-download portal receipts (e.g., monthly 15-minute block)

**Prior failed approaches (lessons learned):**

| Attempt | What happened | Lesson |
|---------|--------------|--------|
| #292 Subscription tracking | Blocked on Roland's input | External dependencies with no resolution path stall indefinitely |
| #518 Receipt collection | 8/13 receipts required Marius's action | Founder as bottleneck = process stops |
| #519 Epic wrapper | Neither child progressed | Coordination without unblocking = no progress |
| #621 Process Refinement | Reframed again → position epic | Bottom-up task approach replaced by top-down position approach |

**Xolo compliance requirements for expenses:**
- TSD reporting deadline: 10th of each month
- Xolo sends automated reminders twice monthly for document uploads
- Receipts rejected for format/VAT issues expand scope beyond simple forwarding

### Part 3: Compliance Verification

Every invoice and expense must meet specific rules before it leaves the company. These rules are discovered through practice — when something goes wrong, it becomes a permanent checklist item.

**Before sending any invoice, check:**

1. **Service period stated** — German clients require "Leistungszeitraum: [Month Year]" in the comments. Required by German tax law. Discovered after the first invoice was sent without it.

2. **Correct VAT treatment** — All current clients are EU businesses, so invoices use 0% reverse-charge (the client handles VAT in their country, not us). The invoice must note: "Reverse charge — Steuerschuldnerschaft des Leistungsempfängers gemäss §13b UStG / Art. 196 EU-MwSt-Richtlinie."

3. **Both VAT numbers visible** — Our Estonian VAT ID (EE102934113) and the client's VAT ID must appear on every EU B2B invoice.

4. **Invoice dated within billing period** — Client accountants reject invoices dated outside the service month. A February invoice must be dated in February or early March — not later.

5. **Contract reference** — T&M invoices include the framework contract reference in comments (e.g., "Rahmenvertrag T&M vom 16.12.2025").

6. **Timesheet shared** — For T&M invoices, confirm the timesheet Google Sheet link was shared with the client. Note it in comments.

**How VAT works for us:**

We're an Estonian company (Wilsch AI Services OÜ). Our VAT treatment depends on who we're invoicing:

- **EU businesses** (all current clients): 0% — reverse-charge. The client pays VAT in their country. We report these on a monthly EC Sales List.
- **Non-EU businesses**: 0% — outside EU VAT scope entirely. No VAT reporting needed.
- **EU consumers** (not currently relevant): We'd charge their country's VAT rate. Unlikely for a B2B agency.
- **Estonian domestic**: 24% VAT. Counts toward the €40K registration threshold.

Xolo handles all VAT returns, corporate tax, and annual reports through their Standard Plan. We don't file these ourselves.

**This checklist grows.** When a new rule is discovered (a client rejects an invoice, Xolo flags a compliance gap, a tax advisor points something out), it gets added here permanently. The checklist is never "complete" — it's always the latest known set of rules.

### Part 4: Payment Reconciliation

When Xolo flags a mismatch between an invoice and a bank payment, the bookkeeper investigates and responds. This happens regularly because payments sometimes split across accounts or dates.

**The resolution loop (from 3 real cases: #686, #687, #899):**

1. **Xolo flags it** — An email from Xolo (typically Gristel Saar) identifies a payment that doesn't match an invoice amount.

2. **Create a tracking issue** — GitHub issue with the discrepancy details and what needs to be resolved.

3. **Investigate** — Check bank records (Wise), payment portals (Google Payments, vendor dashboards), and transaction history to find where the missing amount went. Common cause: one invoice was paid in two separate transactions on different dates.

4. **Respond to Xolo** — Reply in the same email thread with evidence: screenshots, CSV exports, or written explanation of the split payment.

5. **Wait for confirmation** — The issue stays open until Xolo acknowledges the explanation. This can take days.

6. **Close** — Once Xolo confirms, close the issue.

**Common root causes:**
- Payment split across business and personal accounts
- Combined invoices with line items paid on different dates
- Payment description missing required keywords (e.g., "salary/wages" for TSD reporting)

**Xolo reporting deadlines:**
- TSD (payroll tax) reporting: 10th of each month
- Automated document upload reminders: twice monthly from Xolo

### Part 5: Xolo Platform Interface

All financial operations flow through Xolo. Understanding how to interact with it is foundational for every other part of this design.

**Interaction channels:**

| Channel | Used For | Notes |
|---------|----------|-------|
| **Web UI** | Invoice creation, customer management, expense review | Primary interface — all invoice work happens here |
| **Email** | Receipt forwarding, discrepancy communication, compliance alerts | Threaded conversations with Xolo team (Gristel Saar) |
| **Chrome DevTools** | Automated form filling | Prototype demonstrated — Xolo has no API, so browser automation is the only programmatic path |

**No API exists.** Xolo does not offer a public API for invoice creation or expense management. Any future AI automation must use browser automation (Chrome DevTools Protocol) to interact with the Xolo web interface, similar to the approach documented by [Laurent Meyer](https://meyer-laurent.com/automating-my-xolo-invoicing-process) and prototyped in-house for the Abtmayr-Reichert invoice.

**What Xolo handles for us:**
- VAT return filing (monthly)
- EC Sales List reporting (monthly)
- Corporate tax
- Annual reports
- Contact person service (Estonian legal requirement)

**What we handle through Xolo:**
- Invoice creation and sending
- Expense receipt submission
- Payment discrepancy resolution
- Customer record management
- Salary payment confirmation

### Part 6: Path to AI Delegation

The Position Agreement describes the target: a Bookkeeper managing AI Bookkeeping Agents that handle routine operations. This part maps the path from manual (today) to AI-managed (target).

**Progressive autonomy model:**

| Stage | How it works | When to advance |
|-------|-------------|-----------------|
| **Manual** (current) | Human performs all tasks through Xolo | Processes documented in this design doc |
| **AI proposes, human approves** | AI drafts invoices/actions, human reviews and sends | Each task type proven reliable after repeated successful AI proposals |
| **AI autonomous, human spot-checks** | AI executes routine tasks, human reviews exceptions | Track record of zero errors on that task type |

**What the PA delegates to AI agents (target state):**
- Invoice creation (gather inputs, fill Xolo fields, attach documents)
- Xolo platform communication (email-based interactions, translations)
- Payment discrepancy detection and resolution
- Expense receipt forwarding and categorization
- Routine platform administration

**What stays human (per PA):**
- Review and approve AI-generated invoices before client delivery
- Handle true exceptions (human judgment calls, one-off decisions, legal disputes)
- Final compliance verification
- Escalate unresolvable issues to VP / Finance

**Automation constraint:** Xolo has no API. All AI automation must use Chrome DevTools Protocol (browser automation) for Xolo web UI interactions, and email forwarding for expense/receipt operations. This makes automation more fragile than API-based approaches — UI changes can break automation scripts.

**The portal access decision:**
Before AI delegation is viable, the portal access bottleneck (Part 2) must be resolved. Either transfer credentials to the bookkeeper operator, or establish a minimal-touch founder routine for batch downloads.

---

## Source

**Position framework:**
- [Bookkeeper Position Agreement](https://github.com/veloxforce/claude-user-configs/blob/main/hippocampus/global/bookkeeper-position-agreement-wilsch-ai-services.md) — target state definition
- [Position Build Process](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/position-build-process) — Stage/lifecycle framework
- [Organization Chart](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services) — VP/Finance path

**Stage 2 evidence (CCI #606 observations):**
- Invoice creation prototype: Session 0c5fd6f5 (Chrome DevTools end-to-end)
- Leistungszeitraum discovery: Session 0c5fd6f5
- Timesheet template: [Google Sheets](https://docs.google.com/spreadsheets/d/1toFbIRmby-a3UDHpONR_dp91af2mztdYBMWx0IewmnI/edit)
- Payment discrepancy cases: #686, #687, #899 (deliverable-tracking)
- Prior failed attempts: #292, #518, #519, #621 (deliverable-tracking)

**Compliance sources:**
- [Xolo ADR-007](https://github.com/veloxforce/claude-user-configs/blob/main/hippocampus/global/xolo-estonian-entity-service-provider.md) — Platform context
- [Contract Strategy Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/soloforce/contract-strategy-retainer-model-design) — Invoice type definitions
- [Laurent Meyer — Automating Xolo](https://meyer-laurent.com/automating-my-xolo-invoicing-process) — Browser automation feasibility

**Extraction pass:**
- Session: /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-CLAUDE-CODE-IMPROVEMENTS--improvements/03d9d2f4-977e-4c9d-8dd8-1e17634e0a04.jsonl
- CCI: [#606](https://github.com/DaveX2001/claude-code-improvements/issues/606)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

