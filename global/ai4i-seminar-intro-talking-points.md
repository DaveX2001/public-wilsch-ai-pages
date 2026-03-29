---
publish: true
---
# AI4i Seminar – Vorstellung Marius Wilsch
[[client-wilsch-group]]

## Sprechernotizen (~2 Min.)

### 1. 42 University, Marokko (~20s)
- Studium an der 42 – keine Lehrer, keine Vorlesungen
- Prinzip: Du bekommst ein Problem, du findest die Lösung
- Selbstständiges Denken als Grundlage

### 2. ChatGPT, November 2022 (~20s)
- 3 Monate ins Studium, dann kommt ChatGPT
- Sofort klar: Das verändert alles
- Nicht nur ein Tool – ein neues Paradigma

### 3. Der Klick-Moment (~30s)
- 42 hat den Bedarf geschaffen: Probleme selbst lösen, schnell lernen
- KI hat genau diesen Bedarf erfüllt – sofort anwendbar
- Keine Theorie, sondern direkter Einsatz vom ersten Tag an
- Seitdem: KI ist mein tägliches Werkzeug

### 4. Evolution durch Kundenprojekte (~20s)
- Jedes Projekt vertieft die Expertise
- Das Feld ist erst 3 Jahre alt – wir lernen alle noch
- Praxiserfahrung schlägt jede Zertifizierung

### 5. Team-Brücke (~20s)
- Ich bringe die KI-Tiefe mit
- Thomas Erhard bringt die Systemkompetenz – IBM Power, Infrastruktur
- Ulrich Wilsch bringt 40 Jahre Geschäftserfahrung und Kundenbeziehungen

### 6. Teaser + Abschluss (~10s)
- Gleich zeigen wir Ihnen live, was damit möglich ist

---

## Live Demo: Chat2DB auf IBM POWER (~10 Min.)

### Die Geschichte

Bisher: Ulrich braucht einen Geschäftsbericht — ruft Marius an — Marius schreibt SQL — schickt Ergebnis zurück. **Abhängigkeit vom Techniker.**

Jetzt: Ulrich stellt die Frage selbst, auf Deutsch, direkt an die Datenbank. **So einfach wie den Nachbarn fragen.**

### Ablauf

1. **Ulrich** stellt die Frage laut ("Ich will wissen: ...")
2. **Marius** tippt sie in OpenWebUI
3. Antwort erscheint
4. **Marius** erklärt kurz: "Das hätte vorher X gedauert — jetzt eine Frage"

### 3 Fragen (validiert 2026-02-06)

> An welchen Maschinen (MA) kann der Adapter EC2U angeschlossen werden?

→ 9009 und 9105. "Statt manuell Tabellen durchsuchen — eine Frage."

> Zeige mir alle Maschinen (MA) in der Prozessorklasse P05?

→ 9028 (Power10 S1012, 4 Kerne). "Kein SQL nötig — nur die richtige Frage."

> Welche Maschine hat in der Prozessorklasse P10 die meisten Kerne?

→ 9824 (Power11, 2x30 = 60 Kerne). "Das Modell vergleicht und rechnet selbst."

### Danach: Publikum fragt
Ulrich: "Hat jemand eine Frage an die Datenbank?"

### Technik-Checkliste
- `make tunnels` + `make remote-test` vor der Präsentation
- mcpo instabil → bei Fehler: `ssh IBM-POWER 'make mcpo-stop && sleep 2 && make mcpo-start'`
- OpenWebUI: http://localhost:3000

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

