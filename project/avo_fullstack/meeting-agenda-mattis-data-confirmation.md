---
publish: true
---

# Meeting Agenda: Datenbestätigung für Soßen Sourcing POC
[[client-avo]]

## Ziel des Meetings

Am Ende dieses Meetings sollten wir haben:

1. **Datenumfang bestätigt** — welche Einträge aus alle.jsonl sind Fertigprodukte für den Vergleich
2. **Materialbehandlung vereinbart** — wie Zutatenvarianten (z.B. 00004 vs 00004B) im Ähnlichkeitsvergleich behandelt werden
3. **Datenaufbereitung geklärt** — ob Basisvariante-Filterung auf die Testdaten angewendet wurde

---

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Der Vergleichspool enthält 62 R-Präfix-Einträge
→ *Design Doc: Part 1 — Entry type breakdown*

Aus alle.jsonl (197 Einträge) haben 62 ein R-Präfix (Fertigprodukte), 18 RV (Vormischungsrezepte), 9 RZ (Zwischenmischungen), 6 B-Präfix (nicht klassifiziert).

Wir möchten bestätigen:
- Sind diese 62 die vollständige Menge der Fertigprodukte in den Testdaten?
- Was sind B-Präfix-Einträge — sollten welche davon im Vergleichspool sein?

### 2. Basisvariante-Filterung in den Testdaten
→ *Design Doc: Part 1 — Basisvariante filter*

Das Design sieht vor, dass nur Variante 0 (Grundrezeptur) in den Vergleich eingeht. Unsere Analyse hat keine Variantenduplikate unter den 62 R-Einträgen gefunden.

Wir möchten bestätigen:
- Wurde Variante-0-Filterung bei der Erstellung von alle.jsonl angewendet?

### 3. Materialien mit Suffixvarianten
→ *Design Doc: Part 1 — Material ID consistency*

00004 und 00004B erscheinen als separate Material-IDs. Der POC behandelt sie als unterschiedliche Zutaten — sie matchen nicht miteinander im Ähnlichkeitsvergleich.

Wir möchten verstehen:
- Ist diese Behandlung korrekt?
- Sind Suffixvarianten immer derselbe Grundstoff in unterschiedlichen Qualitätsstufen?

### 4. Stammdaten-CSV für zukünftigen Kategorievergleich
→ *Design Doc: Part 2 — Future enhancement*

Kategorie-Gruppierung (Einkaufsgruppen) wird für den POC-Start nicht benötigt, würde aber ermöglichen zu testen, ob das Erkennen austauschbarer Zutaten die Ergebnisse verbessert.

- Wann kann die Stammdaten-CSV geliefert werden?

### 5. Abdeckung der Tabellenstruktur — was fehlt noch?
→ *Design Doc: Part 2 — Proxy Architecture*

Die [Tabellenstruktur](https://docs.google.com/spreadsheets/d/1GEXqdkFxCtA4xSUjFpZTBUBPk6O_Jx8G) definiert 16 Spalten für die Analyse-Metadatei. Aktuell sind 3 Spalten durch `alle.jsonl` abgedeckt (Artikelnummer, Herstellvorschrift, Mengenanteil) — diese ermöglichen den POC mit zwei Ähnlichkeitskriterien.

Die verbleibenden 10 Spalten bilden drei zusätzliche Vergleichsachsen:
- **Einkaufsgruppe** (Spalte 6) — Austauschbarkeit von Zutaten
- **7 Zertifizierungskennzeichen** (Spalten 7–13) — Bio, Halal, Naturland, Kosher, Vegan, Vegetarisch, TK
- **2 DIA-LIMS Kategorien** (Spalten 14–15) — Artikelgruppe und Rohstoffkategorie

Wir möchten verstehen:
- Welche dieser Spalten sind in der Stammdaten-CSV enthalten, die geliefert werden kann?
- Gibt es Spalten, die aktuell nicht befüllt sind oder deren Datenquelle noch unklar ist (im XLSX mit `??.??` markiert)?

---

## Meeting-Format

- **Art:** Bestätigungssession
- **Teilnehmer:** Mattis, Thomas Erhard, Marius Wilsch
- **Erwartung:** Mattis prüft Datenaufbereitungsentscheidungen
- **Ergebnis:** Freigabe der Datenannahmen für die POC-Implementierung

---

## Verknüpft

- **Issue:** [#513 - AVO: KI_PROJEKT_SOSSEN_SOURCING POC](https://github.com/DaveX2001/deliverable-tracking/issues/513)
- **Design Doc:** [Soßen Sourcing POC](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/design-doc-sossen-sourcing-poc)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

