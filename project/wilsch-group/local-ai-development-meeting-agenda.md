---
publish: true
---

# Meeting-Agenda: Lokale KI-Entwicklung — Kommerzielles Angebot
[[client-wilsch-group]]

## Meeting-Ziel

Klärung der kommerziellen Aspekte des lokalen KI-Entwicklungsangebots. Die technische Grundlage (Hardware, Modelle, rechtlicher Rahmen) ist definiert. Offen sind: Preisgestaltung und DPA-Erstellung für lokale Infrastruktur.

1. **Preismodell festlegen** — wie die drei Stufen (Cloud, Hybrid, Voll-Lokal) bepreist werden
2. **Hardware-Kostenverteilung klären** — wer die Mac Studio-Investition (~9.500€) trägt

## Pre-Read

- [Design Doc: Lokale KI-Entwicklung](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/local-ai-development-offering) — insbesondere das Drei-Stufen-Modell und die Hardware-Entscheidung

---

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht beschränkt auf diese.*

### 1. Preismodell für die drei Stufen
⏱️ 20 min

Stufe 1 (Cloud) ist unser Standard-Angebot. Stufe 2 (Hybrid) und Stufe 3 (Voll-Lokal) erfordern zusätzliche Hardware-Investition (~9.500€ Mac Studio) und operativen Aufwand. Die Frage ist, ob und wie dieser Mehraufwand an den Kunden weitergegeben wird.

- Stufe 1 hat laufende API-Kosten (~15€/MTok Input, ~75€/MTok Output bei Claude)
- Stufe 2/3 hat keine laufenden API-Kosten, dafür Hardware-Amortisation
- Mögliche Modelle: Stundensatz-Aufschlag, fester Infrastruktur-Zuschlag pro Projekt, oder gleicher Preis bei anderer Marge

**Zu klären:** Welches Preismodell positioniert lokale Entwicklung als Mehrwert statt als Zusatzkosten.

### 2. Hardware-Investition und Kostenverteilung
⏱️ 15 min

Der Mac Studio M3 Ultra (512GB) kostet ~9.500€ als Sofortkauf. Ein Upgrade auf M5 Ultra folgt im Frühjahr 2026. Die Maschine ist unsere interne Infrastruktur — keine Kunden-Hardware.

- Bei 3 Jahren Nutzung: ~265€/Monat Amortisation
- Hardware dient allen Stufe-2/3-Kunden gleichzeitig (sequentielle Nutzung)
- Alternative: Kosten als allgemeinen Betriebsaufwand in alle Stundensätze einrechnen

**Zu klären:** Ob die Hardware-Kosten dediziert auf Stufe-2/3-Projekte umgelegt oder als allgemeine Infrastruktur behandelt werden.

## Meeting-Format

- **Typ:** Arbeitsgespräch
- **Erwartung:** Thomas und Ulrich haben das Design Doc gelesen (Drei-Stufen-Modell verstanden)
- **Ergebnis:** Entscheidung zu Preismodell und Kostenverteilung → Design Doc wird aktualisiert

## Verknüpfungen

- **Issue:** [#834: Evaluate Local AI Development Hardware & Offering](https://github.com/DaveX2001/deliverable-tracking/issues/834)
- **Design Doc:** [Lokale KI-Entwicklung](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/local-ai-development-offering)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

