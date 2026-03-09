---
publish: true
---

# Meeting-Agenda: FSO-Seminar Scoping — 27. März 2026
[[uwi-retainer]]

## Meeting Goal

Das FSO-Seminar am 27. März braucht einen Inhaltsplan. Seit dem letzten Seminar am 6. Februar hat sich die technische Landschaft weiterentwickelt — Vision-Inferenz auf Power 10, Mac Studio als lokale Entwicklungsplattform, Kernel-Optimierung, und Ostermann als realer Kundenfall. Diese Entwicklungen ergeben vier potenzielle Präsentationsthemen, die priorisiert und entschieden werden müssen.

1. **Seminar-Inhalte festlegen** — Welche der vier Themen (Power 10, Ostermann, Mac Studio, Kernel-Optimierung) präsentiert werden, in welchem Format (Live-Demo vs. Feasibility Story), und ob Kundenprojekte als Case Studies gezeigt werden dürfen
2. **Mac Studio Kaufentscheidung** — Hardware-Beschaffung ist zeitkritisch: 18 Tage bis zum Seminar, Lieferzeiten bei Auslandsbestellung unklar
3. **Epic + Aufgabenteilung** — Nach Inhaltsabsprache: Epic für den FSO-Meilenstein erstellen und Vorbereitungsaufgaben in Sub-Issues zerlegen

## Pre-Read

- [FSO-Seminar 6. Februar — Transcript](https://app.fireflies.ai/view/01KGS021VPV39W9J7XTC5A6PM9) — Letztes Seminar: 23 Teilnehmer, Talk-to-Your-Data Demo, KI-Bilder, Case Studies
- [Mac Studio Listings (#929)](https://github.com/DaveX2001/deliverable-tracking/issues/929) — Drei Optionen: M3 Ultra 512GB (€13.500 Japan), M2 Ultra 192GB (€7.399 eBay DE), M4 Max 128GB (€3.199 eBay DE)
- [Design Doc Part 7 — Feasibility Report](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report) — Vision-Inferenz auf Power 10: POC-Architektur, Modell-Kandidaten, Performance-Projektionen
- [#713 — Kernel Optimization](https://github.com/DaveX2001/deliverable-tracking/issues/713) — $3.500-Angebot für Qwen 8B Profiling, 3–5 Tage Assessment

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Seminar-Inhalte: Vier technische Entwicklungen seit Februar
⏱️ 20 min

Das Februar-Seminar war Awareness/Education: Firmenvorstellung, Talk-to-Your-Data Demo, KI-Bilder. 23 Teilnehmer aus Ulrichs Kundennetzwerk — darunter potenzielle Käufer wie AVO, Rekers und Ostermann. Seitdem sind vier Themen mit unterschiedlichem Reifegrad entstanden.

- **Power 10 Vision-Inferenz** (#952) — POC-Architektur für PDF-Verarbeitung steht, Benchmarks vorhanden (55→500 tok/s). Live-Demo oder Feasibility Story
- **Ostermann** (#866) — Kunde hat Infrastrukturdaten investiert (AS400-Diagramm, Dokumentation, Sicherungskonzept). Social Proof: andere investieren bereits
- **Mac Studio Lokale Inferenz** (#929) — USP: vollständige lokale KI-Entwicklung ohne Cloud-Abhängigkeit, DDR4/5-Knappheit als Marktvorteil. Live-Demo oder Story
- **Kernel-Optimierung** (#713) — R&D-Investition in Inferenz-Geschwindigkeit auf Power 10, konkrete Benchmark-Daten

**To resolve:** Welche Themen ins Seminar kommen, in welchem Format (Live-Demo vs. Story), und ob das Seminar beim Awareness-Ansatz bleibt oder einen stärkeren Sales-Anteil bekommt.

### 2. Kernel-Entwickler Engagement: $3.500 Profiling-Assessment für Power 10 Inferenz
⏱️ 10 min

Ein konkretes Angebot liegt vor: 3–5 Tage Qwen 8B Profiling-Assessment auf Power 10 für $3.500. Das Assessment würde TTFT- und TPS-Baselines erstellen und Optimierungspotenzial identifizieren — Grundlage für die Performance-Verbesserung, die im Seminar als R&D-Investition gezeigt werden könnte.

- Zeitfenster: Assessment vor dem 27. März würde Live-Benchmarks als Seminar-Material liefern
- Aktueller Baseline: 55 tok/s — Optimierungsziel 500 tok/s (10x)
- Doppelter Wert: Interne Infrastruktur-Verbesserung UND Seminar-Story über ernsthaftes R&D-Engagement

**To resolve:** Ob die $3.500-Investition jetzt getätigt wird und ob das Timing auf das Seminar abgestimmt werden soll.

### 3. Mac Studio Kaufentscheidung — drei Optionen, 18 Tage bis zum Seminar
⏱️ 10 min

Drei Secondhand-Angebote stehen zur Auswahl. Wenn die Mac Studio als Live-Demo im Seminar gezeigt werden soll, muss die Entscheidung jetzt fallen — Lieferung, Einrichtung und Demo-Vorbereitung brauchen Zeit.

- **M3 Ultra 512GB** — €13.500 (Mercari Japan) — maximale Kapazität, Auslandsversand
- **M2 Ultra 192GB** — €7.399 (eBay Deutschland) — schnellere Lieferung, geringerer Speicher
- **M4 Max 128GB** — €3.199 (eBay Deutschland) — Backup-Option, günstigster Einstieg
- Unabhängig vom Seminar strategisch wichtig: lokale Entwicklungsplattform für alle KI-Projekte

**To resolve:** Welche Konfiguration beschafft wird und ob das Seminar als Deadline für die Betriebsbereitschaft gilt.

## Meeting Format

- **Type:** Discovery / Planung
- **Dauer:** ~40 min (20 + 10 + 10)
- **Erwartung:** Mac Studio Listings vorab ansehen (#929), Februar-Seminar-Ablauf im Kopf haben
- **Outcome:** Entscheidung über Seminar-Inhalte, Mac Studio Kauf, und anschließend Epic-Erstellung mit Aufgabenteilung

## Related

- **Epic:** [#650 — UWI Retainer](https://github.com/DaveX2001/deliverable-tracking/issues/650)
- **Meilenstein:** [\[UWI\] 2026-03-27 — FSO seminar](https://github.com/DaveX2001/deliverable-tracking/milestone/11)
- **Referenzierte Issues:** [#929](https://github.com/DaveX2001/deliverable-tracking/issues/929), [#952](https://github.com/DaveX2001/deliverable-tracking/issues/952), [#713](https://github.com/DaveX2001/deliverable-tracking/issues/713), [#866](https://github.com/DaveX2001/deliverable-tracking/issues/866)
- **Letztes Seminar:** [FSO 6. Feb Transcript](https://app.fireflies.ai/view/01KGS021VPV39W9J7XTC5A6PM9)
- **Source:** 🗒️ Session ff224ad1

