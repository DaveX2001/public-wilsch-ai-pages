---
publish: true
---

# Meeting Agenda: Hardware-Strategie + Website-Update für FSO
[[client-wilsch-group]]

## Meeting Goal

Hardware-Strategie und Website-Update für die FSO am 27. März abstimmen. Drei Ergebnisse:

1. **Deployment-Fahrplan** — vom DGX Spark-Empfang (18. März) bis zur einsatzbereiten FSO-Demo
2. **Website-Prioritäten** — welche Inhalte müssen bis zum 27. März aktualisiert oder hinzugefügt werden
3. **Phase-2-Richtung** — Mac Studio/M5 Ultra-Entscheidung terminieren

## Pre-Read

- [Hardware-Strategie: Lokale KI-Inferenz — Kaufentscheidung](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/hardware-strategie-lokale-ki-inferenz)
- [Wilsch AI Website (deployed)](https://wilsch-ai-internal-wilsch-ai-site.vercel.app/) — aktueller Stand vor Änderungen

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. DGX Spark-Deployment bis FSO am 27. März
⏱️ 10 min

Zwischen Abholung (18. März) und FSO-Demo (27. März) liegen 9 Tage. In dieser Zeit muss die Hardware eingerichtet, die Serving-Engine installiert und die REKERS-Pipeline (#629) benchmarkfähig sein.

- Playbook aus dem Research: llama.cpp am Tag 1 (Hardware validieren), vLLM für die FSO-Demo (API + Web-UI)
- Page Index Tree Generation und Tree Traversal (#1137) auf DGX Spark vs. Power 10 benchmarken
- Offen: wer macht was — Marius (Setup), David (Pipeline), Thomas (Netzwerk)?

**To resolve:** Aufgabenverteilung und Meilensteine für die 9 Tage zwischen Abholung und FSO festlegen.

### 2. Spyre-Vergleichsdaten verifizieren
⏱️ 5 min

Der Spyre-Vergleich im Kaufentscheidungsdokument zeigt die Apple/NVIDIA-Kombination als 7–10× günstiger bei ~75% der Bandbreite. Die IBM Spyre-Spezifikationen stammen jedoch aus Sekundärquellen — für eine Kundenpräsentation brauchen wir IBM-Primärdaten.

- Aktuell im Dokument: 8× Spyre = 1 TB Speicher, 1,6 TB/s Bandbreite, 600W, €100.000–150.000
- Thomas hat IBM-Kontakte — kann er die Specs bestätigen oder korrigieren?
- Ulrich braucht belastbare Zahlen für Kundengespräche

**To resolve:** Weg zur Verifizierung der Spyre-Specs über IBM-Primärquellen klären.

### 3. Mac-Auswahl für Phase 2 (Disaggregated Inference)
⏱️ 10 min

Für den Decode-Part der disaggregierten Inferenz brauchen wir eine Maschine mit hoher Speicherbandbreite. Drei Optionen stehen im Raum:

- **M3 Ultra 256GB** (jetzt verfügbar): 819 GB/s, ~€8.300, bewährt, Thunderbolt 5
- **M5 Max 128GB** (seit 11. März): 614 GB/s, ~€5.100–6.800, portabel, Thunderbolt 5 — aber nur 128 GB RAM
- **M5 Ultra** (erwartet ~Mitte 2026): projiziert ~1,1–1,2 TB/s, 256 GB — Apple hat die 512GB-Option beim M3 gestrichen, Signal für M5 Ultra Mac Studio

**To resolve:** Entscheidungszeitpunkt für Phase 2 festlegen — jetzt kaufen oder auf M5 Ultra warten, und welche Faktoren die Entscheidung bestimmen.

### 4. Beschaffungslogistik für Hardware-Einkauf
⏱️ 5 min

Bei den DGX Sparks kauft Marius lokal in SG/MY. Für Phase 2 (Mac Studio) und zukünftige Hardware-Investitionen brauchen wir klare Zuständigkeiten.

- Ulrich hat bereits festgelegt: WPH (Wilsch Power Hosting) übernimmt Leasing/Kaufverträge, nicht Wilsch AI OÜ
- Import-Fragen: Versand von Asien nach Deutschland, Zoll, Garantieansprüche
- Laufende Kosten: wer trägt Strom, Hosting, Wartung?

**To resolve:** Beschaffungsprozess für zukünftige Hardware klären — Entity, Importweg, Kostenträger.

### 5. Format des Kaufentscheidungsdokuments
⏱️ 5 min

Das Kaufentscheidungsdokument existiert als Design Doc auf der Hippocampus-Seite. Unklar ist, ob Format und Sprache für die Zielgruppe (Thomas, Ulrich) passen.

- Aktuell: Deutsch, Markdown, als Webseite veröffentlicht
- Alternativen: PDF-Export für E-Mail-Versand, Präsentationsformat, oder Webseite reicht
- Detaillevel: technische Details sind in #929-Kommentaren ausgelagert — richtige Trennung?

**To resolve:** Zielformat und Verteilerweg für das Kaufentscheidungsdokument festlegen.

### 6. Website-Update für FSO am 27. März
⏱️ 15 min

Die Website hat aktuell 11 Landingpage-Sektionen, 3 live Case Studies (Legal Bot, AVO, Archibus), eine Hardware-Optionen-Seite (IBM Power) und eine N-Komm-Seite. Alles liegt hinter einem Login (Supabase, invite-only). FSO-Teilnehmer können die Seite ohne Zugangsdaten nicht sehen.

- Quick Win: Marokko-Case-Study ist fertig gebaut, aber nicht im Router verdrahtet (3 Zeilen Code)
- Offen: soll die Seite für die FSO öffentlich werden, oder bekommen Teilnehmer Login-Daten?
- Offen: braucht die Seite neue Inhalte (GPU/DGX-Seite, Benchmark-Ergebnisse, Disaggregated Inference)?
- 2 Placeholder-Case-Studies (Betonhersteller, Faceless YouTube) — entfernen oder fertigstellen?
- Bestehende Case Studies aktualisieren: Archibus Phase 2 abgeschlossen, Rohdex fertig, DGX-Benchmarks als neuer Inhalt
- Story Arc: David entwickelt als Nicht-Entwickler produktiv mit KI-Tools — eigenes Narrativ für die Website?

**To resolve:** Priorisierte Liste der Website-Änderungen bis zum 27. März und Zugangsmodell für FSO-Teilnehmer.

## Meeting Format

- **Type:** Review + Entscheidung
- **Teilnehmer:** Marius, David, Ulrich
- **Erwartung:** Design Doc gelesen, Website kurz angeschaut
- **Outcome:** Aufgabenverteilung für die nächsten 13 Tage bis FSO

## Related

- **Issue:** [#929 — Rent Mac Studio Ultra 256/512GB RAM](https://github.com/DaveX2001/deliverable-tracking/issues/929)
- **Design Doc:** [Hardware-Strategie: Lokale KI-Inferenz](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/hardware-strategie-lokale-ki-inferenz)
- **FSO-Demo Pipeline:** [#629 — KI Projektähnlichkeit POC](https://github.com/DaveX2001/deliverable-tracking/issues/629)
- **Website Repo:** [DaveX2001/wilsch-ai-site](https://github.com/DaveX2001/wilsch-ai-site)
- **Session:** 68e26b44-fcd5-4995-830d-b67616444ffe
