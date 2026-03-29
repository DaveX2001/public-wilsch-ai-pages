---
publish: true
---

# Meetingagenda: VET DOCX + Rechnung — Rohdex
[[project-rohdex]]

**Datum:** Dienstag, 2026-03-11, 12:30
**Teilnehmer:** Marius Wilsch, Konstantin Fitermann

## Meetingziel

Zwei Entscheidungen die nächste Schritte am ROHDEX Dokumentenverarbeitungssystem freigeben.

1. **VET-Ausgabeformat entschieden** — ob das Veterinärzertifikat zusätzlich zum Excel auch als DOCX generiert werden soll, letzter offener Punkt im Design Doc
2. **Migrationsabnahme bestätigt** — Konstantin bestätigt dass Migration auf RDX-APP-01 abgeschlossen ist, Rechnung geht direkt raus

## Vorbereitungsmaterial

- [Design Doc — Part 5: Bekannte Probleme](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen) — VET-Abschnitt mit Undefined-Marker für Ausgabeformat
- [SLA v4 Wartungsvertrag §1.1](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/sla-v4-wartungsvertrag) — Migrationsumfang (€720) und Abrechnungsbedingungen (§4.2)

---

## Diskussionsthemen

*Einstiegspunkte, nicht abschließend.*

### 1. VET-Ausgabeformat — Excel-only oder zusätzlich DOCX
⏱️ 10 min

Das System generiert alle 8 Dokumenttypen als Excel-Dateien. Im Dezember 2025 sandte Konstantin ein Word-Dokument ("Veterinär BUSAN.docx") — ein Anschreiben an die Veterinärbehörde. Die VET-Template-Review bestätigt: alle 7 dynamischen Felder stimmen mit dem BUSAN-Referenzdokument überein. Die Frage ist nicht der Inhalt, sondern das Ausgabeformat.

- VET-Excel-Output ist vollständig und verifiziert (7/7 Felder korrekt)
- Das System hatte früher DOCX-Support, wurde auf Excel-only migriert
- Falls DOCX gewünscht: Konstantin stellt Word-Template bereit oder wir erstellen eines basierend auf BUSAN.docx

**To resolve:** Ob das VET-Ausgabeformat von Excel-only auf DOCX erweitert werden soll — und falls ja, ob Konstantin ein Template liefert oder wir eines erstellen.

### 2. Migrationsabnahme und Rechnungsstellung
⏱️ 5 min

Die Migration auf RDX-APP-01 ist seit dem 5. März live. Das System verarbeitet E-Mails produktiv. SLA v4 §1.1 beziffert die Migration auf €720 (8h).

- System läuft produktiv auf RDX-APP-01 (verifiziert 5. März)
- Tara-Bugfix (Dezimalstellen in Packliste) wird diese Woche ebenfalls deployed
- SLA v4 §4.2: Rechnungsstellung monatlich zum 1., Zahlungsziel NET 14

**To resolve:** Bestätigung dass die Migration abgeschlossen ist — Rechnung (€720) wird direkt im Anschluss gestellt.

## Meetingformat

- **Typ:** Discovery
- **Dauer:** ~15 min
- **Erwartung:** Konstantin entscheidet beide Punkte — keine Vorbereitung nötig
- **Ergebnis:** Zwei Entscheidungen die nächste Schritte freigeben

## Verweise

- **Issue:** [#1037 — ROHDEX: Clarify VET DOCX output request](https://github.com/DaveX2001/deliverable-tracking/issues/1037)
- **Design Doc:** [Hosting-Anforderungen — Part 5](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen)
- **Epic:** [#909 — Dokumentenverarbeitung SLA & Wartung](https://github.com/DaveX2001/deliverable-tracking/issues/909)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

