---
publish: true
---

# Meeting Agenda: Client Sync — Klimafolgenschutz Website Abschluss

[[project-abtmayr-reichert]]

**Datum:** Mittwoch, 2026-03-12
**Teilnehmer:** Marius Wilsch, Michael Reichert

## Ziel des Meetings

Zwei Blocker verhindern die Fertigstellung der Klimafolgenschutz-Website. Alles andere ist gebaut und live.

1. **Stripe Payment — Voraussetzungen klären** — Aktueller Stand Vereinsregister, Blocker-Kette sichtbar machen und Zeithorizont einschätzen, damit wir die Zahlungsintegration planen können
2. **Zertifizierung — Inhalte erfassen** — Kriterien und Ablauf der Zertifizierung „klimaresiliente Gemeinde" erklären oder als Dokument zuschicken, damit wir den Website-Text schreiben können

---

## Gesprächsthemen

*Ausgangspunkte für die Diskussion, nicht abschließend.*

### 1. Stripe Payment Integration — Voraussetzungskette
⏱️ 15 min

Die Zahlungsintegration (#762) ist das letzte große Feature. Die Website ist live, alle anderen Sections sind fertig. Es gibt eine Kette von Voraussetzungen, die abgeschlossen sein müssen, bevor wir Stripe konfigurieren können.

- **Blocker-Kette:** Vereinsregister → Bankkonto → IBAN → Gläubiger-ID (ca. 10 Tage) → Steuernummer
- Wie ist der aktuelle Stand beim Vereinsregister?
- Ohne diese Kette können wir Stripe nicht konfigurieren (SEPA, Karte, Invoicing)
- **Kleinunternehmerregelung:** Die ~€500/Jahr Gemeinde-Beiträge werden als Rechnung gestellt. Der Verein startet als Kleinunternehmer — keine MwSt auf Rechnungen. Sobald die Steuernummer da ist: mit Steuerberater bestätigen
- **Downstream:** Impressum + Datenschutz brauchen ebenfalls die Vereinsregister-Adresse und VR-Nummer (für e-Recht24 Generatoren)

**To resolve:** Aktueller Stand der gesamten Voraussetzungskette und ein realistischer Zeithorizont für die nächsten Schritte.

### 2. Zertifizierung „Klimaresiliente Gemeinde" — Inhalte
⏱️ 10 min

Die Zertifizierungs-Section (#763) ist technisch fertig und ans CMS angebunden. Was fehlt: der Inhalt.

- Erkläre uns den Zertifizierungsprozess im Meeting oder schick uns vorab ein Dokument — damit wir den Schritt-für-Schritt-Ablauf wirklich verstehen
- Gibt es interaktive Elemente (z.B. Checklisten, Selbsttest für Gemeinden), die über statischen Text hinausgehen?
- Geschätzter Aufwand für uns: 2-3 Stunden, sobald der Inhalt steht

**To resolve:** Zertifizierungskriterien und -ablauf erfassen — entweder mündlich im Meeting oder als zugeschicktes Dokument — inklusive Klärung, ob interaktive Website-Elemente nötig sind.

### 3. Falls Zeit: IONOS DNS-Weiterleitung
⏱️ 3 min

klimafolgenschutz.com zeigt noch die alte IONOS-Standardseite statt auf klimafolgenschutz.org weiterzuleiten. Für die Umleitung brauchen wir deine IONOS-Zugangsdaten.

**To resolve:** IONOS-Zugangsdaten erhalten, damit .com auf .org weiterleitet.

## Ablauf

- **Typ:** Discovery
- **Dauer:** ~30 min
- **Teilnehmer:** Marius Wilsch, Michael Reichert
- **Vorbereitung:** Keine — alles wird im Meeting erklärt
- **Ergebnis:** Klarheit über Vereinsregister-Zeithorizont und Zertifizierungsinhalt, damit die letzten zwei Features umgesetzt werden können

## Referenzen

- **Issue:** [#1084 — Meeting Agenda Client Sync 2026-03-12](https://github.com/DaveX2001/deliverable-tracking/issues/1084)
- **Epic:** [#726 — Klimafolgenschutz Website](https://github.com/DaveX2001/deliverable-tracking/issues/726)
- **Blockierte Issues:** [#762 — Stripe Payment](https://github.com/DaveX2001/deliverable-tracking/issues/762) | [#763 — Zertifizierung](https://github.com/DaveX2001/deliverable-tracking/issues/763)
- **Design Doc:** [Klimafolgenschutz Website](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/design-doc-klimafolgenschutz-website)
- **Vorheriges Meeting:** [Payment Setup (Feb 12)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/abtmayr-reichert/meeting-agenda-klimafolgenschutz-payment-setup)

## Quelle

🗣️ [Session 2026-03-10](https://github.com/MariusWilsch/claude-code-conversation-store/blob/main/projects/-Users-daveFem-Desktop-claude-projects-06-ABTMAYR-REICHERT--deliverable/7db61213-f8a9-4c81-b493-66189db7f606.jsonl) — Extraction pass on Marius's feedback (5 items resolved)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

