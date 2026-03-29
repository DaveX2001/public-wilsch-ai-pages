---
publish: true
---

# REKERS Evaluierungsliste — Validierungsdesign Besprechung
[[client-rekers]]

## Besprechungsziel

Validierungsdesign für den V006 Machbarkeitsbericht klären, damit die POC-Tests mit einer gemeinsamen Definition von "richtigen Ergebnissen" starten können.

1. **Evaluierungsliste abgestimmt** — Einigung auf Testanfragen, erwartete Treffer, und CSV-Vorlage (4 Spalten)
2. **Umgang mit "nichts gefunden" geklärt** — Einigung auf Testfälle, bei denen kein Treffer die richtige Antwort ist
3. **Eingangsdefinition abgestimmt** — Einigung auf was eine neue Anfrage bei Ankunft enthält (Auslöser für das Abgleichsystem)

## Vorablektüre

- [Entwurfsdokument — Machbarkeitsbericht](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report) (Teil 4: Validierungsdesign)
- [Datenauswertung — Testdaten](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)

---

## Diskussionspunkte

*Einstiegspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Testanfragen aus den 14 gelieferten Projekten

Ansatz: 10 Projekte als Referenz, die 11. als Testanfrage. CSV-Vorlage mit 4 Spalten steht bereit: Testanfrage, Erwarteter Treffer, Fehlfährte (ja/nein), Begründung. Ausschlussverfahren (Leave-one-out): 9 Projekte als Datenbestand, das 10. abfragen, Ergebnis mit Erwartung vergleichen.

- Für jede Testanfrage liegt der erwartete Treffer vor — z.B. "Testanfrage X → Projekt Y, weil Gebäudetyp und Höhe übereinstimmen"
- Ausgefüllte CSV-Vorlage durch Herrn Sasse

### 2. "Nichts gefunden" als gültiges Ergebnis

Nicht jede Anfrage hat einen Treffer. Das System muss auch sagen können: "Dafür haben wir kein vergleichbares Projekt." Ein Gebäudetyp den REKERS noch nie gebaut hat, oder ein Kunde ohne Referenzhistorie — solche Fälle gehören in die Evaluierungsliste.

- Einigung auf Testfälle, bei denen kein Treffer die richtige Antwort ist
- Klärung ob diese aus den bestehenden 14 Projekten kommen oder als neue Szenarien konstruiert werden

### 3. Der Anfrageprozess — vom Kundenkontakt zur Anfrage im System

Wir wissen wie die historischen Projekte aussehen (14 geliefert), aber nicht wie eine neue Anfrage bei Eingang aussieht. Das System braucht einen definierten Startpunkt. Projekt 35764 zeigt: 9 Dokumente vom Architekten (bbarchitektur.de), 12 von REKERS, 22 von Dritten (Baugenehmigungen, Gutachten). Die Trennung zwischen "was bei Erstanfrage vorliegt" und "was REKERS im Laufe der Bearbeitung hinzufügt" ist in den Daten nicht sichtbar.

- Beschreibung eines typischen Eingangs — was liegt vor, bevor REKERS anfängt zu arbeiten
- Einschätzung ob Kontaktweg (Architekt, Bauherr, Generalunternehmer) die verfügbaren Informationen beeinflusst
- Zuordnung der 9 Ähnlichkeitskriterien zu dem, was realistisch aus der Erstanfrage ableitbar ist

### 4. Zweck der "Angebote ohne Material" CSV

Die Datenlieferung enthielt eine CSV mit 4.307 Angebotszeilen (141 Spalten), die im Workshop nicht besprochen wurde. Ich konnte den Zweck dieser Datei nicht zuordnen — wofür war sie gedacht?

## Besprechungsformat

- **Typ:** Arbeitssitzung
- **Teilnehmer:** Herr Sasse + Marius + Thomas Erhard (ggf. Kalkulator)
- **Erwartung:** Herr Sasse kommt mit Kenntnis der 14 gelieferten Projekte und einer Einschätzung, welche davon "ähnlich" zueinander sind
- **Ergebnis:** Gemeinsames Verständnis der Evaluierungsmethode und Einigung auf den Inhalt der Evaluierungsliste. Die CSV wird anschließend von REKERS ausgefüllt.

## Verweise

- **Vorgang:** [#629 — REKERS: KI-gestütztes Angebotssystem - Workshop 2](https://github.com/DaveX2001/deliverable-tracking/issues/629)
- **Entwurfsdokument:** [Machbarkeitsbericht](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Datenauswertung:** [Testdaten-Bericht](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/data-assessment-testdaten)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

