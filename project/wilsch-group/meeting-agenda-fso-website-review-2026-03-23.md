---
publish: true
---

# Meeting Agenda: FSO Website Review — KI-Performance Seite
[[client-wilsch-group]]

## Meeting Goal

Review the FSO website (/ki-performance) section-by-section before the March 27 seminar. The page is live with benchmark data — this is about polish, not content creation.

1. **Content clarity** — confirm the numbers and technical explanations land for a non-technical customer audience
2. **Design alignment** — resolve layout inconsistencies and visual polish items flagged during internal review

## Pre-Read

- [KI-Performance Seite (live)](https://wilsch-ai-site.vercel.app/ki-performance) — Login erforderlich
- [Technische Details Unterseite](https://wilsch-ai-site.vercel.app/ki-performance/details)
- [FSO Präsentation Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/fso-praesentation-ki-performance) — Quelldokument für alle Inhalte

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Hardware-Vergleichskarten — Darstellung und Ausrichtung
⏱️ 5 min

Die fünf Systemkarten (Power 10, DGX Spark, Mac Studio, Spire, H100) zeigen Specs wie TFLOPS, Speicher und Preis. Die Werte sind teilweise einzeilig, teilweise umgebrochen — das wirkt uneinheitlich.

- Fünf Karten nebeneinander auf Desktop, responsive auf Mobile
- Werte-Formatierung inkonsistent (manche auf einer Zeile, manche nicht)
- Ulrich hat bereits angemerkt, dass es "ein bisschen viel" wirkt

**To resolve:** Entscheiden, ob die Karten vereinfacht, die Formatierung vereinheitlicht oder das Layout geändert werden soll.

### 2. Benchmark-Zahlen — Kontext für Nicht-Techniker
⏱️ 10 min

Die Benchmark-Sektion zeigt ~8,9 Stunden (Power 10) → ~2,6 Minuten (DGX Spark) = 207× Beschleunigung. Die Zahlen sind korrekt (294 Seiten, 8 parallele Anfragen auf GPU), aber der Kontext fehlt für ein nicht-technisches Publikum.

- "207×" und "~2,6 Minuten" basieren auf 8 gleichzeitigen Verarbeitungs-Slots auf der GPU — das wird nirgends erklärt
- Fußnote zeigt zwei Zahlen: 207× (parallel) vs. 40× (sequentiell) — das könnte verwirren statt klären
- Phasen-Tabelle (Bilderkennung, Prefill, Decode) ist technisch korrekt, aber die Bedeutung für den Kunden unklar

**To resolve:** Festlegen, wie viel technischen Kontext die Benchmark-Sektion braucht, damit die Zahlen für FSO-Teilnehmer verständlich sind, ohne die Wirkung zu verwässern.

### 3. Visuelle Überladung — Effekte und CTA-Sektion
⏱️ 5 min

Zwei Bereiche auf der Hauptseite wirken nicht stimmig: die "Warum so schnell?"-Karten haben einen visuellen Effekt zu viel, und die CTA-Sektion "Vergleichstest anfragen" am Seitenende hat keine klare Rolle.

- "Warum so schnell?"-Karten: Inhalt ist gut (3 Faktoren), aber der Hover-/Glow-Effekt addiert visuelle Komplexität ohne Informationsgewinn
- CTA "Ihr Projekt. Unsere Plattformen." — wirkt weder als Footer noch als eigenständiger Call-to-Action, eher verloren zwischen Kaufentscheidung und Seitenende
- Marius' Einschätzung: Effekte reduzieren, CTA klären oder entfernen

**To resolve:** Entscheiden, ob die Karteneffekte vereinfacht und die CTA-Sektion integriert, umgestaltet oder entfernt wird.

### 4. Kaufentscheidung — Infografik-Bild überarbeiten
⏱️ 5 min

Die Kaufentscheidung-Sektion ("Drei Wege zur lokalen KI") ist stark — drei klare Optionen mit Preisen. Das Infografik-Bild darunter (DGX Spark neben Power 10 Architekturdiagramm) braucht Feinschliff.

- Preisangabe im Bild kommt visuell nicht durch — zu klein oder schlecht positioniert
- Bild selbst ist gut, unterstützt die Aussage
- Marius' Einschätzung: Preistag aus dem Bild entfernen, Rest beibehalten

**To resolve:** Bildanpassung abstimmen — Preistag entfernen oder neu gestalten.

### 5. Details-Seite — Layout und Präzisions-Narrativ
⏱️ 10 min

Die technische Unterseite (/ki-performance/details) hat guten Inhalt, aber die Sektionen wirken zusammengeklumpt — kein visueller Atemraum zwischen den Themenblöcken. Zusätzlich fehlt in der Präzisions-Sektion das Kosten-Narrativ.

- Alle Sektionen (Performance-Faktoren, Präzision, Kleine Modelle, Papier vs. Realität) sitzen direkt aufeinander ohne klare visuelle Trennung
- Präzisions-Tabelle (FP32→FP04) zeigt nur technische Daten — die Geschichte fehlt: höhere Präzision = mehr Ressourcen = mehr Kosten
- FP4 ist erst seit 2024 verfügbar (Blackwell-Architektur), FP8 seit 2022 (Hopper), FP16 seit 2017 (Volta) — diese Timeline zeigt, wie neu die Technologie ist
- FP4 ist ausschließlich für Inferenz geeignet, nicht für Training — genau der REKERS-Anwendungsfall
- Auswirkungen-Tabelle nutzt Text (Möglich/Sehr Langsam) statt ✅/❌ — weniger scanbar

**To resolve:** Spacing-Konzept für die Details-Seite abstimmen und entscheiden, ob das Kosten-Narrativ (Präzision senkt Kosten + Timeline) ergänzt wird.

### 6. Offene Punkte — Gesamteindruck und weitere Sektionen
⏱️ 10 min

Durchgang durch alle weiteren Sektionen, die nicht einzeln besprochen wurden. Gelegenheit für Ulrich, Gesamteindruck und Anmerkungen einzubringen.

- Hero-Sektion, "Warum so schnell?"-Inhalte und Kaufentscheidung-Tabelle wurden intern als gut bewertet — Bestätigung einholen
- Homepage-CTA (Teaser vor IBMPowerSection) — noch nicht besprochen
- Mobile-Darstellung — bisher nicht geprüft

**To resolve:** Alle weiteren Anmerkungen sammeln und priorisieren, die vor dem 27. März umgesetzt werden sollen.

## Meeting Format

- **Type:** Review
- **Teilnehmer:** Marius, Ulrich
- **Erwartung:** Website vorher kurz durchscrollen (5 min)
- **Outcome:** Priorisierte Änderungsliste für David bis 27. März

## Related

- **Issue:** [#1196 — FSO Seminar Webseiten](https://github.com/DaveX2001/deliverable-tracking/issues/1196) (closed — implementation complete)
- **Issue:** [#1080 — DGX Spark infrastructure platform](https://github.com/DaveX2001/deliverable-tracking/issues/1080) (benchmark data source)
- **Design Doc:** [FSO Präsentation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/fso-praesentation-ki-performance)
- **Prior Agenda:** [Hardware-Strategie + Website-Update (2026-03-14)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/meeting-agenda-hardware-website-fso-2026-03-14)
- **Website Repo:** [DaveX2001/wilsch-ai-site](https://github.com/DaveX2001/wilsch-ai-site)
- **Session:** b738d75e-d7ff-4c64-abd0-3d0af831cf5f

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

