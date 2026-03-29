---
publish: true
---

# Meeting-Agenda: Validierungspaare für Soßen Sourcing POC
[[client-avo]]

## Meetingziel

Wir bauen ein gemeinsames Verständnis auf, wie der Validierungsprozess für den Rezeptähnlichkeits-Algorithmus funktioniert, und erheben erste Bewertungspaare.

1. **Gemeinsames Verständnis** — Wir besprechen, warum wir Ihre Einschätzung brauchen und wie der Rückkanal funktioniert (Sie geben Paare → wir zeigen Ergebnisse → Sie geben Feedback)
2. **Bewertungspaare** — 5 bekannte ähnliche Rezeptpaare aus dem 62-Rezeptpool mit Ihren Anmerkungen
3. **Format vereinbart** — Wir besprechen die XLSX-Vorlage gemeinsam und passen sie bei Bedarf an

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Das zeigt unser Algorithmus Ihnen zurück

Wir entwickeln ein Rezeptvergleichssystem — für jede Rezeptur die 5 ähnlichsten mit Begründung. So wird die Ausgabe aussehen:

```
Abfrage: R261800

Rang  Rezeptur  Bezeichnung           Überlappung  Anteil  Gesamt
#1    R261700   Tomatensoße Mild      85%          97%     92%
#2    R482000   Paprikasoße Basis     62%          78%     71%
#3    R954700   Tomatensoße Scharf    58%          82%     68%
#4    R445100   Chilisoße Rot         51%          65%     57%
#5    R887200   Kräutersoße           48%          71%     55%
```

- **Überlappung:** Wie viele Zutaten beide Rezepturen gemeinsam haben
- **Anteil:** Wie nah die Mengen bei gemeinsamen Zutaten beieinander liegen
- **Gesamt:** Gewichtete Kombination aus Überlappung und Anteil

### 2. Zur Kalibrierung benötigen wir bekannte Rezeptpaare von Ihnen

- Wir testen, ob der Algorithmus die richtigen Ergebnisse liefert
- Dazu benötigen wir bekannte ähnliche Paare aus Ihrer Erfahrung
- In der XLSX-Vorlage finden Sie die Liste aller 62 Rezepturen aus dem Testpool
- → [XLSX-Vorlage öffnen (Google Drive)](https://docs.google.com/spreadsheets/d/1yQyUYYF_cPZRsjUIE9ZUv-AHl2-74k4N)

| Rezeptur_A | Ähnlichste (Pflicht) | Weitere (optional) | Anmerkung |
|------------|---------------------|-------------------|-----------|
| R...       | R...                | R..., R...        | Freitext  |

- **Rezeptur-IDs** aus dem Testpool auswählen
- **Anmerkung:** Frei notieren, warum diese Rezepturen ähnlich sind — hilft uns, „ähnlich" aus fachlicher Sicht zu verstehen

### 3. So funktioniert der Rückkanal

- Sie geben uns Paare → wir lassen den Algorithmus laufen
- Wir schicken Ihnen die Ergebnisse per E-Mail als XLSX zurück
- Sie sehen für jede Rezeptur die 5 ähnlichsten — mit Rezeptname, Überlappung, Anteil, Gesamtwert
- Sie können direkt in der Datei anmerken, ob die Ergebnisse Sinn ergeben
- So verbessern wir den Algorithmus schrittweise, ohne jedes Mal ein Meeting zu brauchen

## Meetingformat

- **Art:** Erkundung — gemeinsames Verständnis + Erhebung von Bewertungspaaren
- **Teilnehmer:** Herr Behrens, Thomas Erhard, Marius Wilsch
- **Erwartung:** Wissen darüber, welche Rezepturen als ähnlich gelten
- **Ergebnis:** Vereinbarte XLSX-Vorlage + 5 ausgefüllte Bewertungspaare

## Verknüpfungen

- **Issue:** [#513: AVO: KI_PROJEKT_SOSSEN_SOURCING POC](https://github.com/DaveX2001/deliverable-tracking/issues/513)
- **Design Doc:** [Published](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/avo_fullstack/design-doc-sossen-sourcing-poc)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

