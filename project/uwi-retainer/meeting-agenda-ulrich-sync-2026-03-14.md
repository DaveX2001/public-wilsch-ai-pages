---
publish: true
---

# Meeting-Agenda: Ulrich Sync — 14. März 2026
[[client-wilsch-group]]

## Meeting Goal

Vier offene Themen besprechen: Fireflies-Routing-Design für automatische Meeting-Zusammenfassungen, Ostermann-Dokumentation abschließen, Anthropic-Account gemeinsam reparieren, und VoIP-Migration — Placetel als günstigere Alternative zu Dialpad vorstellen.

1. **Fireflies → TANSS Routing Design** — Ticketformat, Routing-Mechanismus, Fallback
2. **Ostermann Dokumentation** — Finalisieren oder schließen
3. **Anthropic Account** — Gemeinsam im Meeting lösen
4. **VoIP Migration — Placetel-Empfehlung** — Günstiger, schneller, Promo endet 31. März

## Pre-Read

- [UWI — Retainer (Epic #650)](https://github.com/DaveX2001/deliverable-tracking/issues/650) — Gesamtkontext
- [Fireflies Auto-Email (#779)](https://github.com/DaveX2001/deliverable-tracking/issues/779) — Auto-Summary System
- [Dialpad VoIP Migration (#784)](https://github.com/DaveX2001/deliverable-tracking/issues/784) — Aktueller Migrationsstatus

---

## Discussion Topics

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Fireflies → TANSS Routing Design
⏱️ 15 min

Automatische 5-Punkte-Zusammenfassungen nach Kundenmeetings ([#779](https://github.com/DaveX2001/deliverable-tracking/issues/779)) — wie soll das Routing in TANSS funktionieren? Drei zusammenhängende Entscheidungen:

- **Ticketformat pro Kunde:** Feste Ticketnummer pro Kunde (AVO, Rekers, etc.) oder neues Ticket pro Meeting/Thema? Einheitliches Muster für alle Kunden.
- **Routing-Mechanismus:** Wie erkennt das System, welchem Kunden ein Meeting-Thema gehört? Über Teilnehmer, gesprochene Keywords, oder manuelle Zuordnung?
- **Fallback:** Was passiert mit Themen, bei denen keine Ticketnummer gesprochen wird? Default-Ticket, manuelle Triage, oder verwerfen?

### 2. Ostermann Dokumentation
⏱️ 5 min

Die AI-gestützte Dokumentation für das Ostermann-Projekt ([#866](https://github.com/DaveX2001/deliverable-tracking/issues/866)) — Dokumente liegen mittlerweile vor.

- **Status:** Blocked seit Wochen, Dokumente jetzt verfügbar
- **Letzte Frage (Marius, 09.03):** „Sollen wir das schließen und später wieder aufmachen, oder jetzt finalisieren?"
- **Offener Punkt:** Klare Entscheidung — jetzt fertigstellen oder bewusst verschieben

### 3. Anthropic Account + 18€ Email
⏱️ 10 min

Ulrich hat keinen Zugriff auf sein Anthropic Account ([#1063](https://github.com/DaveX2001/deliverable-tracking/issues/1063)). Wiederholt 18€-Emails erhalten, Problem nicht selbst lösbar.

- **Vorgehen:** Gemeinsam im Meeting lösen — Screenshare, Account-Recovery durchgehen
- **Optionen:** Passwort zurücksetzen oder bei Marius nachfragen

### 4. VoIP Migration — Placetel-Empfehlung
⏱️ 10 min

WISO IP wird abgeschaltet. Aktuelle Situation: Dialpad wurde im Februar als Provider bestätigt ([#784](https://github.com/DaveX2001/deliverable-tracking/issues/784)). Neue Recherche (11. März) zeigt: **Placetel Voice Flat** ist deutlich günstiger und schneller einsatzbereit.

**Kostenvergleich (10 User):**

| | Dialpad | Placetel | Sipgate |
|---|---------|----------|---------|
| Kosten/Monat | ~435 € | ~106 € | ~240 € |
| Pro User | ~43,50 € | 9,90 € | ~24 € |
| Ersparnis vs. Dialpad | — | **75%** | 45% |

**Feature-Vergleich:**

| Feature | Dialpad | Placetel |
|---------|---------|----------|
| iPhone App | Dialpad App | Webex App |
| IVR/Ansagen | ✅ | ✅ (alle Tarife) |
| Aufnahme | Add-on | 6,90 €/Monat flat |
| API/Webhooks | ✅ | ✅ (HMAC-signiert) |
| Nummernportierung | 1-3 Monate | 3-4 Wochen (§59 TKG) |
| 2-stellige Durchwahlen | Min. 3 Stellen | ✅ |

**⚠️ Placetel Promo endet 31. März 2026** — sichert den Preis 9,90 €/User dauerhaft, auch für zukünftige User.

- **Empfehlung:** Placetel Voice Flat — 75% günstiger, schnellere Portierung, alle Features inklusive
- **Runner-up:** Sipgate — bessere CLINQ App (Phone-only UI), Deutschland-only Server, aber 2x teurer als Placetel
- **Portierung 06461 von NFON:** Kostenlos, 3-4 Wochen, §59 TKG Rechtsanspruch

## Meeting Format

- **Typ:** Design-Entscheidung + Live-Fix
- **Dauer:** 40 min
- **Ergebnis:** TANSS-Routing-Regeln definiert, Ostermann-Entscheidung, Anthropic-Account repariert, VoIP-Provider-Entscheidung

## Related

- **Retainer Epic:** [#650](https://github.com/DaveX2001/deliverable-tracking/issues/650)
- **Fireflies Auto-Email:** [#779](https://github.com/DaveX2001/deliverable-tracking/issues/779)
- **Ostermann Doku:** [#866](https://github.com/DaveX2001/deliverable-tracking/issues/866)
- **Anthropic Account:** [#1063](https://github.com/DaveX2001/deliverable-tracking/issues/1063)
- **VoIP Migration:** [#784](https://github.com/DaveX2001/deliverable-tracking/issues/784)
