---
publish: true
---

# Rohdex Hosting — Verantwortungsklärung
[[client-rohdex]]

## Meeting Goal

Drei Themen klären — Kommunikationsstruktur, Infrastruktur-Verantwortung und SLA-Preis:

1. **Aktuelle Situation** — Direkte Zusammenarbeit ohne Vermittler vorschlagen, Konstantins Bereitschaft zur Fortführung klären
2. **Infrastruktur-Konfiguration** — Wer richtet die Server-Umgebung ein (Docker, Firewall, Ports)?
3. **Laufende Verantwortungsgrenze** — Wo endet Marius' Zuständigkeit (App) und wo beginnt Rohdex-IT (Server)?

## Pre-Read

- [Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen-kunde) — Abschnitt 3 (On-Premise) und Abschnitt 4 (Migration) als Kontext für die Diskussionspunkte

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Aktuelle Situation und direkte Zusammenarbeit
⏱️ 5 min

Der SLA-Vertrag wurde am 11. Dezember an den bisherigen Vermittler übergeben — ist aber nie bei Rohdex angekommen. Konstantin hat am 14. Februar bestätigt, dass er keine Unterlagen erhalten hat. Gleichzeitig läuft die Infrastruktur seit über zwei Monaten auf Marius' Servern ohne vertragliche Grundlage.

- Hintergrund: Das Vertragsverhältnis mit dem bisherigen Vermittler wird aufgelöst — Marius übernimmt die direkte Kommunikation
- Das System läuft produktiv und stabil — an der technischen Betreuung ändert sich nichts
- Ab sofort: Marius ist der direkte Ansprechpartner für alles Technische und Vertragliche
- Falls Konstantin bereit wäre, seine Erfahrung mit der bisherigen Kommunikationsstruktur kurz schriftlich zu bestätigen — willkommen, aber keine Voraussetzung

**To resolve:** Konstantins Bereitschaft zur direkten Zusammenarbeit klären und den SLA-Vertrag ohne Vermittler neu aufsetzen.

### 2. Infrastruktur-Konfiguration bei On-Premise-Migration
⏱️ 10 min

Für die Migration auf Rohdex-Infrastruktur muss Docker installiert, Firewall-Ports geöffnet und Netzwerk-Konnektivität hergestellt werden. Aktuell ist nicht definiert, wer diese Arbeiten durchführt.

- Rohdex-IT hat eigene Serverinfrastruktur und verwaltet diese eigenständig
- Marius benötigt SSH-/Remote-Zugang für das App-Deployment
- Eine IT-Checkliste mit Vorbereitungsschritten existiert bereits im Anforderungsdokument

**To resolve:** Die Zuständigkeit für die Infrastruktur-Vorbereitung festlegen — entweder übernimmt Marius die gesamte Konfiguration (mit Zugang von Rohdex-IT), oder Rohdex-IT bereitet die Umgebung nach Checkliste vor und Marius deployt nur die Applikation.

### 3. SLA-Preismodell — Verantwortung bestimmt den Preis
⏱️ 15 min

Das SLA setzt sich aus zwei Bereichen zusammen: App-Verantwortung (325 EUR) und Infrastruktur-Verantwortung (200 EUR). Der Preis hängt davon ab, wer den On-Premise-Server managt.

- App-Ebene (325 EUR): Wartung, Updates, Bugfixes, Monitoring (GlitchTip), Template-Anpassungen — bleibt bei Marius
- Infra-Ebene (+200 EUR): Server-Uptime, OS-Updates, Backups, Netzwerk, Docker-Engine — nur wenn Marius verantwortlich
- Marius managt On-Premise-Server = 525 EUR
- Rohdex-IT managt On-Premise-Server = 325 EUR

**To resolve:** Festlegen, wer die Server-Verantwortung auf der Rohdex-Infrastruktur übernimmt. Damit steht der SLA-Preis fest.

## Meeting Format

- **Type:** Discovery — Telefongespräch mit Konstantin
- **Expectation:** Konstantin klärt vorab mit seinem IT-Dienstleister, welche Aufgaben die IT übernehmen kann
- **Outcome:** Klare Zuständigkeitsmatrix für Migration und laufenden Betrieb → SLA-Preisanpassung kann finalisiert werden

## Related

- **Design Doc:** [Hosting-Anforderungen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/hosting-anforderungen)
- **Issue:** [#766 Client Communication Transition](https://github.com/DaveX2001/deliverable-tracking/issues/766)
- **SLA v3:** [Google Doc](https://docs.google.com/document/d/1zgJA0ToNX3GU0IoyM0yjnmlp4PV0cqS2LQTRjtpJg_U/edit)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

