---
publish: true
---

# ADR 2025-12-17: Expense Tracking System
[[estonia-entity]]

**Datum:** 17. Dezember 2025
**Status:** Implementiert
**Kontext:** #292 Subscription Tracking, #290 Annual Plan Evaluation
**Autor:** David + Claude

---

### Problem

Keine zentrale Übersicht über:
- Monatliche Subscriptions (SaaS, Tools)
- Usage-based Services (Claude, Runpod)
- Reisekosten
- Gesamtausgaben

**Konsequenz:** Keine Visibility auf Kosten, keine Basis für Annual-Plan-Entscheidungen.

---

### Entscheidung

**Expense Tracking Dokument** in `hippocampus/project/WILSCH-AI-INTERNAL/expenses-2025.md`

**Struktur:**

| Section | Inhalt |
|---------|--------|
| **Summary** | Monthly totals by category |
| **Fixed Subscriptions** | Xolo, Hetzner, Google, Timing |
| **Usage-based Services** | Claude, Runpod, Fireflies, Supabase, etc. |
| **Travel** | Alle Reisekosten |
| **Transfers** | Inter-company, Personal |
| **Savings Analysis** | Annual vs Monthly, Unused Services |

**Datenquelle:** Wise CSV Export - Alle Money-Out Transaktionen

---

### Q3/Q4 2025 Summary (Oct-Dec)

| Category | Total |
|----------|-------|
| Fixed Subscriptions | €164.45 |
| Usage-based Services | €837.11 |
| Travel | €1,175.44 |
| Transfers | €1,128.00 |
| **GESAMT** | **€3,305.00** |

**SaaS/Tools Only:** €1,001.56/Quartal (~€334/Monat)

---

### Savings Opportunities

**Quick Wins:**
- Morph LLM kündigen (falls ungenutzt) - €8.96/Monat
- Timing App → Annual - ~€38/Jahr
- Google Workspace → Annual - ~€14/Jahr

**Open Questions:**
1. Morph LLM - Wird das noch genutzt?
2. Fireflies - Pro Plan nötig oder reicht Whisper?
3. Supabase - Free Tier ausreichend?
4. OpenRouter - Redundant mit Claude?

---

### Workflow & Next Steps

```
Workflow:
1. Wise CSV exportieren (Money-Out, Zeitraum wählen)
2. expenses-2025.md updaten
3. Monthly Tracking Template ausfüllen
4. Savings Review bei Bedarf

Next Steps:
- [ ] #290 Annual Plan Evaluation (blocked by #292)
- [ ] Monatliches Update-Ritual etablieren
- [ ] Fragen aus Savings Analysis klären
```

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

