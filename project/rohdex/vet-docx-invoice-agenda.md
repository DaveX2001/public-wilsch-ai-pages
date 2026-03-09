---
publish: true
---

# Meeting Agenda: VET DOCX + Invoice — Rohdex
[[project-rohdex]]

**Date:** Tuesday, 2026-03-11, 12:30
**Attendees:** Marius Wilsch, Konstantin Fitermann

## Meeting Goal

Two decisions that unblock next steps on the ROHDEX Dokumentenverarbeitung system.

1. **VET output format decided** — whether the veterinary certificate needs DOCX output in addition to the current Excel, resolving the last open item in the design doc
2. **Invoice timing agreed** — whether the €720 migration invoice goes out now or after the Tara bugfix is complete

## Pre-Read

- [Design Doc — Part 5: Bekannte Probleme](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen) — VET section shows Undefined marker for output format
- [SLA v4 Wartungsvertrag §1.1](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/sla-v4-wartungsvertrag) — migration scope (€720) and billing terms (§4.2)

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. VET-Ausgabeformat — Excel-only oder zusätzlich DOCX
⏱️ 10 min

Das System generiert alle 8 Dokumenttypen als Excel-Dateien. Im Dezember 2025 sandte Konstantin ein Word-Dokument ("Veterinär BUSAN.docx") — ein Anschreiben an die Veterinärbehörde. Die VET-Template-Review bestätigt: alle 7 dynamischen Felder stimmen mit dem BUSAN-Referenzdokument überein. Die Frage ist nicht der Inhalt, sondern das Ausgabeformat.

- VET-Excel-Output ist vollständig und verifiziert (7/7 Felder korrekt)
- Das System hatte früher DOCX-Support, wurde auf Excel-only migriert
- Falls DOCX gewünscht: Konstantin stellt Word-Template bereit oder wir erstellen eines basierend auf BUSAN.docx

**To resolve:** Ob das VET-Ausgabeformat von Excel-only auf DOCX erweitert werden soll — und falls ja, ob Konstantin ein Template liefert oder wir eines erstellen.

### 2. Rechnungsstellung Migration — jetzt oder nach Tara-Bugfix
⏱️ 5 min

Die Migration auf RDX-APP-01 ist seit dem 5. März live. SLA v4 §1.1 beziffert die Migration auf €720 (8h), inklusive ~1h für den Tara-Bugfix. Der Tara-Fix (Dezimalstellen-Fehler in der Packliste) ist noch offen — er benötigt die Staging-Umgebung.

- System verarbeitet E-Mails produktiv auf RDX-APP-01 (verifiziert 5. März)
- Tara-Bugfix (~1h) blockiert durch Staging-Setup
- SLA v4 §4.2: Rechnungsstellung monatlich zum 1., Zahlungsziel NET 14

**To resolve:** Ob die €720-Migrationsrechnung jetzt gestellt wird (Tara separat als T&M) oder als Gesamtpaket nach Abschluss des Tara-Fixes.

## Meeting Format

- **Type:** Discovery
- **Duration:** ~15 min
- **Expectation:** Konstantin entscheidet beide Punkte — keine Vorbereitung nötig
- **Outcome:** Zwei Entscheidungen die nächste Schritte freigeben

## Reference: Kostenrahmen VET DOCX

| Aufgabe | Aufwand |
|---------|---------|
| DOCX-Library + VetDocxStrategy | ~2h |
| Template-Integration (Konstantin liefert Template) | ~1h |
| Template-Erstellung (wir bauen von Grund auf) | ~2–3h |
| Testing mit bestehenden Datensätzen | ~1–2h |
| Integration in Dokumenten-Pipeline | ~0,5h |
| **Gesamt (Template geliefert)** | **~4–5h = €360–450** |
| **Gesamt (Template von Grund auf)** | **~5–7h = €450–630** |

Abrechnung unter SLA v4 (§4.1: €90/h, 30-Min-Takte).

## Related

- **Issue:** [#1037 — ROHDEX: Clarify VET DOCX output request](https://github.com/DaveX2001/deliverable-tracking/issues/1037)
- **Design Doc:** [Hosting-Anforderungen — Part 5](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen)
- **Epic:** [#909 — Dokumentenverarbeitung SLA & Wartung](https://github.com/DaveX2001/deliverable-tracking/issues/909)
