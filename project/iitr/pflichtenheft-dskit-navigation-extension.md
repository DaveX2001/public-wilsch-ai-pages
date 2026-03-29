---
publish: true
---

# Pflichtenheft: DS-Kit Navigation – Erweiterung
[[client-iitr]]

*Technische Spezifikation für Erweiterung des bestehenden DS-Kit Navigationssystems*

---

## 1. Deckblatt

| | |
|---|---|
| **Projekt** | DS-Kit Navigation – Erweiterung |
| **Auftraggeber** | IITR Datenschutz GmbH |
| **Auftragnehmer** | Wilsch AI Services OÜ |

---

## 2. Zielbestimmung

### Muss (Erweiterungsumfang)
- Website-Extraktion: Kapitel von core.iitr.de extrahieren und indexieren
- Support-Eskalation: "Support kontaktieren"-Button bei unsicheren Antworten

### Später (Wartet auf Kundenzulieferung)
- Integration neuer Masterfragen – wartet auf Zulieferung von Stellmacher (~Ende Januar)
- Zusätzliche Q&A-Paare – Stellmacher liefert erweiterte CSV mit Detailfragen

### Außerhalb Umfang
- Telefon-Support oder Live-Chat
- Automatische Synchronisation der Textinhalte mit core.iitr.de (einmalige Extraktion)
- Änderungen an bestehender POC-Funktionalität

---

## 3. Funktionale Anforderungen

### Website-Extraktion (core.iitr.de)

- **FR-E01:** Textinhalte der DS-Kit-relevanten Kapitel aus core.iitr.de extrahieren (nur Text – keine Videos, keine Transkripte)
- **FR-E02:** Extrahierte Inhalte in Typesense indexieren, Genauigkeit bei DATA-GAP-Fragen verbessern (Q6, Q12, Q13, Q17, Q18, Q24, Q27)

### Support-Eskalation Level 2

- **FR-E03:** Langfuse-Observability-Filter für Gesprächsverlauf deployen
- **FR-E04:** "Support kontaktieren"-Button mit E-Mail-Link und Session-Kontext anzeigen
- **FR-E05:** Auto-Vorschlag des Support-Buttons bei unsicheren Antworten

---

**Preis:** EUR 800 (Fixpreis)


---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

