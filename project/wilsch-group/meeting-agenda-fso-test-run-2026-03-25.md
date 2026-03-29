---
publish: true
---

# FSO Test Run — Analogien & Webseiten-Korrekturen

[[client-wilsch-group]]

## Meeting Goal

Analogien auswählen, die Ulrich beim FSO-Seminar (27. März) einem Geschäftsführer in 10 Sekunden nacherzählen kann. Drei technische Konzepte brauchen jeweils eine Alltagsanalogie — FP4, MoE, und CPW≠TFLOPS. Dazu: faktische Korrekturen an der Webseite freigeben.

1. **Analogien ausgewählt** — je eine Analogie pro Konzept, die Ulrich sofort einsetzen kann
2. **Webseiten-Korrekturen freigegeben** — Preise, Seitenzahlen, Fußnoten bereit für David

## Pre-Read

- [FSO Präsentation — Webseite](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/fso-praesentation-ki-performance) — aktuelle Version der Webseite, die beim Seminar gezeigt wird
- [Transcript: Dad-Call 25. März](https://app.fireflies.ai/view/01KMHMVY4FH6ZAMKFBC9PJ997K) — Ulrichs Feedback zu den erklärungsbedürftigen Stellen

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Niedrige Präzision (FP4) — Analogie für Laien
⏱️ 5 min

Ulrich wurde gefragt: „Warum kann man bei niedriger Präzision gleiche Qualität bekommen?" — keine Antwort parat. Drei Analogie-Kandidaten stehen zur Auswahl. Alle erklären dasselbe: die Aufgabe braucht nicht die volle Genauigkeit.

- **Toleranzklassen:** „Wenn Sie ein Fertigteil gießen, brauchen Sie Maße auf den Zentimeter — nicht auf den Mikrometer. FP4 ist die Toleranzklasse für KI: die Genauigkeit, die zur Aufgabe passt."
- **MP3:** „FP4 ist für KI das, was MP3 für Musik war — zehnmal weniger Daten, aber das Lied hört sich gleich an. Das Modell wirft die Nachkommastellen weg, die für das Ergebnis keine Rolle spielen."
- **JPEG:** „Ein JPEG-Foto sieht identisch aus wie das Original bei einem Achtel der Dateigröße — FP4 macht dasselbe mit den Zahlen im KI-Modell."

**To resolve:** Welche Analogie kann Ulrich einem Geschäftsführer in einem Satz nacherzählen — und welche passt zum FSO-Publikum (AS/400-nativ, nicht KI-affin).

### 2. Dense vs. Mixture of Experts — Hauptgeschichte für Faktor 2
⏱️ 5 min

Ulrich fragte: „Was ist MoE? Wer macht das?" Die aktuelle Webseite bündelt FP4 und MoE in einem Punkt. Entscheidung aus der Extraktion: MoE wird die Hauptgeschichte (Dense vs. MoE), FP4 der Enabler darunter. Drei Analogie-Kandidaten für MoE:

- **Fachleute/Unternehmen:** „Das Modell hat 500 Spezialisten, aber pro Auftrag arbeiten nur 3–4. Das Modell weiß selbst, welche es braucht. Ergebnis: Geschwindigkeit eines kleinen Teams, Wissen der ganzen Firma."
- **Arzt/Facharzt:** „Wie beim Hausarzt — der überweist Sie an den richtigen Facharzt, nicht an alle gleichzeitig. Ein eingebauter Hausarzt im Modell entscheidet automatisch."
- **LPAR-Brücke (nur für IT Leiter):** „Wie LPAR, aber umgedreht — pro Anfrage wird die passende Partition aktiviert."

**Compound-Effekt:** FP4 verkleinert jeden Parameter. MoE aktiviert weniger Parameter. Zusammen multiplizieren sie sich — das Wissen der ganzen Firma, die Geschwindigkeit eines kleinen Teams.

**To resolve:** Welche Analogie setzt Ulrich ein — und ob der Compound-Effekt (FP4 × MoE) als Zusatzsatz erwähnt wird oder zu komplex ist.

### 3. CPW misst Datenbanken, TFLOPS misst KI — zwei verschiedene Sportarten
⏱️ 5 min

Das FSO-Publikum denkt in CPW. Die Webseite nutzt TFLOPS. Kein Brückentext vorhanden. CPW und TFLOPS sind nicht konvertierbar — sie messen grundverschiedene Dinge (Transaktionsdurchsatz vs. Rechenleistung). Die Analogie muss die Power 10 als Investment validieren, während sie erklärt, warum KI andere Hardware braucht.

- **Pianist vs. Orchester:** „Ihre Power 10 ist ein virtuoser Pianist — brillant für ERP und Datenbanken, eine Aufgabe nach der anderen, perfekt. KI braucht ein Orchester: 6.000 Musiker gleichzeitig. Wir stellen das Orchester neben Ihren Pianisten."
- **Marathon vs. Sprint:** „Ihre Power ist ein Marathonläufer — Ausdauer und Zuverlässigkeit. KI ist ein anderer Sport: 6.000 parallele Sprints gleichzeitig."

**To resolve:** Ob die CPW≠TFLOPS-Brücke auf die Webseite gehört (Folie 1 als Opener) oder nur mündlich beim Vortrag eingesetzt wird.

## Meeting Format

- **Type:** Test-Durchlauf (Probelauf FSO-Vortrag)
- **Erwartung:** Ulrich liest die drei Analogie-Optionen und wählt je eine aus, die er sofort mündlich einsetzen kann
- **Outcome:** Drei ausgewählte Analogien (FP4, MoE, CPW≠TFLOPS) bereit für die Webseite

## Related

- **Issue:** [#1080 — REKERS: JA design — DGX Spark infrastructure platform](https://github.com/DaveX2001/deliverable-tracking/issues/1080)
- **Webseite:** [FSO Präsentation (GitHub Pages)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/fso-praesentation-ki-performance)
- **Production:** [wilsch-ai-site.vercel.app](https://wilsch-ai-site.vercel.app/)
- **Transcript:** [Dad-Call 25. März](https://app.fireflies.ai/view/01KMHMVY4FH6ZAMKFBC9PJ997K)
- **Session:** `ef9e7e69-cd0e-45d5-8c8d-83c547c2121c`

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

