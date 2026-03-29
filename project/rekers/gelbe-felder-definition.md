---
publish: true
---

# Gelbe Felder - Definition

[[client-rekers]]

## Kontext

**Ziel Workshop 2:** Reverse-Engineering - verstehen, wie diese Felder heute befüllt werden.

Sobald die gelben Felder ausgefüllt sind, berechnet das ERP-System automatisch den Gesamtpreis - ein Knopfdruck genügt. Die Frage ist: Woher kommen die Werte?

## Was sind die Gelben Felder?

Eingabefelder in der Stückliste, die aus Kundenanfragen befüllt werden.

*Beispiele aus Workshop 1 (nicht vollständig):*

| Feld | Beschreibung | Beispiel |
|------|--------------|----------|
| Betongüte | Festigkeitsklasse | C 50/60 |
| Materialnummer | Interne Artikelnummer | 4711 |
| Menge | Stückzahl / Masse | 2.500 kg |
| Bauteiltyp | Art des Fertigteils | Stütze Typ B |
| Abmessungen | L x B x H | 6m x 0,4m x 0,4m |

## Prozess

```
Kundenanfrage    →    Gelbe Felder    →    Preiskalkulation
(Text/Zeichnung)      (Eingabe)            (automatisch)
```

## Quelle

Definition aus Workshop 1: [Fireflies Transkript](https://app.fireflies.ai/view/01K7403385R85MGJ4FKJVSQE67)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

