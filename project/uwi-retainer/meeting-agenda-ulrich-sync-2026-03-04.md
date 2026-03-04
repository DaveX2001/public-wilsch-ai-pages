---
publish: true
---

# Meeting-Agenda: Ulrich Sync — 4. März 2026
[[client-wilsch-group]]

## Meeting Goal

Retainer-Housekeeping und zwei Entscheidungspunkte. Gemeinsamer Durchgang des UWI-Retainer-Backlogs (21 Sub-Issues) — Abgeschlossene bestätigen, offene priorisieren oder streichen. Danach: IITR-Vertragsverlängerung zur Unterschrift freigeben und Geschäftsregel für Versicherungsrechnungen ohne eigene Rechnungsnummer festlegen.

1. **Retainer-Backlog bereinigen** — Disposition aller 21 Sub-Issues (11 abgeschlossen, 10 offen)
2. **IITR-Vertrag → Unterschrift** — Platzhalter aufgelöst, bereit für nächsten Schritt
3. **Versicherungsrechnungen** — Geschäftsregel für Allianz-Style Abrechnungen im System
4. **BrokerBin Listing** — Scope und Preisvorstellungen für IBM-Komponentenverkauf
5. **Private AI Workspace** — Weiterführen, umlenken oder schließen

## Pre-Read

- [UWI — Retainer (Epic #650)](https://github.com/DaveX2001/deliverable-tracking/issues/650) — vollständige Sub-Issue-Liste
- [IITR: Vertragsverlängerung (#954)](https://github.com/DaveX2001/deliverable-tracking/issues/954) — Vertragsstatus und letzter Commit
- [UWI: Versicherungsrechnungen ohne Rechnungsnummer (#1016)](https://github.com/DaveX2001/deliverable-tracking/issues/1016) — Untersuchungsergebnis und Allianz-Belege

---

## Discussion Topics

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Retainer-Backlog Disposition
⏱️ 15 min

Der Retainer-Epic ([#650](https://github.com/DaveX2001/deliverable-tracking/issues/650)) hat 21 Sub-Issues — 11 abgeschlossen, 10 offen. Gemeinsamer Durchgang zur Bereinigung: Abgeschlossene bestätigen, offene priorisieren oder streichen.

**Abgeschlossen (11):**

| # | Thema | Status |
|---|-------|--------|
| [#617](https://github.com/DaveX2001/deliverable-tracking/issues/617) | Cloud Caller/STK Agent Discovery | Zusammengelegt in #599 |
| [#679](https://github.com/DaveX2001/deliverable-tracking/issues/679) | Telecom Konsolidierung (iCall → Dialpad) | Zusammengelegt in #784 |
| [#636](https://github.com/DaveX2001/deliverable-tracking/issues/636) | Google Calendar RSVP → TANSS | Deployed, Cron läuft |
| [#836](https://github.com/DaveX2001/deliverable-tracking/issues/836) | Time-Tracking Strategie | Durch Retainer-Modell erledigt |
| [#724](https://github.com/DaveX2001/deliverable-tracking/issues/724) | FSO Marketing Event Follow-up | Als Daily-Item konsolidiert |
| [#928](https://github.com/DaveX2001/deliverable-tracking/issues/928) | WPH INTEVA Proposal kürzen | Erledigt |
| [#599](https://github.com/DaveX2001/deliverable-tracking/issues/599) | TANSS Data Access | Verschoben auf Sommer |
| [#640](https://github.com/DaveX2001/deliverable-tracking/issues/640) | Portal Wilsch Hosting-Analyse | Warten auf Thomas |
| [#894](https://github.com/DaveX2001/deliverable-tracking/issues/894) | Google Meet Doku für Ulrich | PDF geliefert |
| [#895](https://github.com/DaveX2001/deliverable-tracking/issues/895) | Tägliche AI-Trainings mit David | Abgeschlossen |
| [#992](https://github.com/DaveX2001/deliverable-tracking/issues/992) | Dialpad Credits England | Gelöst |

**Offen (10):**

| # | Thema | Aktuell |
|---|-------|---------|
| [#779](https://github.com/DaveX2001/deliverable-tracking/issues/779) | Fireflies Auto-Email Summaries | ACs definiert, warten auf AVO TANSS Ticket# |
| [#784](https://github.com/DaveX2001/deliverable-tracking/issues/784) | Dialpad VoIP Migration | Blockiert — JA Position Diskussion |
| [#893](https://github.com/DaveX2001/deliverable-tracking/issues/893) | DNS Domain + Subdomain Setup | ACs definiert, warten auf Subdomain-Namen |
| [#917](https://github.com/DaveX2001/deliverable-tracking/issues/917) | Mac Studio Local AI Setup | Blockiert — Hardware-Entscheidung (DGX Spark vs Mac Studio) |
| [#918](https://github.com/DaveX2001/deliverable-tracking/issues/918) | Retainer-Vertrag Konsolidierung | Laufende Planung |
| [#866](https://github.com/DaveX2001/deliverable-tracking/issues/866) | Ostermann Projekt-Doku | Warten auf Stakeholder-Antwort |
| [#995](https://github.com/DaveX2001/deliverable-tracking/issues/995) | Rechnung Januar 2026 (Datum 31.01) | Offen, keine Aktion |
| [#735](https://github.com/DaveX2001/deliverable-tracking/issues/735) | Private AI Workspace (OpenWebUI) | Offen — schließen falls nicht mehr relevant? |
| [#1016](https://github.com/DaveX2001/deliverable-tracking/issues/1016) | Versicherungsrechnungen ohne Rechnungsnummer | Eigenes Diskussionsthema (Topic 3) |
| ~~[#996](https://github.com/DaveX2001/deliverable-tracking/issues/996)~~ | ~~Allianz ESA Invoice → REEIBU~~ | Geschlossen als Duplikat von #1016 |

**To resolve:** Gemeinsame Bestätigung der Abschlüsse und Priorisierung der offenen Items — welche sind noch relevant, welche können geschlossen werden.

### 2. IITR Vertragsverlängerung — Unterschriftsreife
⏱️ 5 min

Der Verlängerungsvertrag für IITR ([#954](https://github.com/DaveX2001/deliverable-tracking/issues/954)) ist inhaltlich fertig. Die letzten drei Platzhalter wurden aufgelöst:

- **Kontoverbindung** — Wise EUR Bankdaten (IBAN, BIC) eingetragen
- **USt-IdNr** — IITR DE256675995 ergänzt
- **Denglish bereinigt** — "Change Request" → "Änderungsantrag"

Letzter Commit: [`23dcd0a`](https://github.com/veloxforce/claude-user-configs/commit/23dcd0a843b662937e795b4519cdc7e1c939df29)

**To resolve:** Freigabe und Zeitplan für die Unterzeichnung — wer unterschreibt wann, und ob noch eine finale Review-Runde nötig ist.

### 3. Versicherungsrechnungen ohne eigene Rechnungsnummer
⏱️ 10 min

Frau Klärl hat eine fehlende Allianz ESA Rechnung gemeldet ([#1016](https://github.com/DaveX2001/deliverable-tracking/issues/1016)). Die Untersuchung zeigt: das System funktioniert korrekt — aber Versicherungsrechnungen haben keine eigene Rechnungsnummer. Allianz verwendet nur die Versicherungsschein-Nummer (AS-9806441433) für alle Abrechnungen unter einer Police.

- **Zwei unterschiedliche Rechnungen, eine Nummer:** Einzeltransport EUR 780,94 (verarbeitet) + Jahresbeitrag EUR 562,28 (blockiert)
- **Systemverhalten ist korrekt:** Duplikat-Schutz verhindert die zweite Rechnung
- **Lücke:** Keine Geschäftsregel für Dokumente ohne eindeutige Rechnungsnummer
- **Bestehendes UI:** Duplikat-Erkennung existiert bereits im Transfer-Dashboard — könnte erweitert werden

**To resolve:** Geschäftsregel für Versicherungsrechnungen — manuell erfassen, zusammengesetzten Schlüssel verwenden (Police + Zeitraum), oder Allianz um eindeutige Referenzen bitten.

### 4. BrokerBin Account + IBM-Komponentenlisting
⏱️ 5 min

Ulrich hat den Verkauf von zwei IBM POWER Komponenten über BrokerBin beauftragt ([#1036](https://github.com/DaveX2001/deliverable-tracking/issues/1036)). David ist zugewiesen — vor Start braucht er bestätigten Scope und Kontodaten.

- **Zwei Komponenten:** EC5W (6.4TB NVMe) + ESJD (3.7TB SSD)
- **Kontoeröffnung:** Kostenlos, aber telefonische Firmenverifizierung nötig (1 Werktag)
- **Noch offen:** Keine DoD definiert, Preisvorstellungen und Listing-Details fehlen

**To resolve:** Bestätigung der Listing-Priorität und Preisvorstellungen für die beiden Komponenten — was soll David konkret einstellen.

### 5. Private AI Workspace — Weiterführen, Umlenken oder Schließen
⏱️ 5 min

Das Angebot für eine private AI-Arbeitsumgebung mit OpenWebUI + Ollama ([#735](https://github.com/DaveX2001/deliverable-tracking/issues/735)) steht seit Februar offen — ohne Scope, ohne DoD, ohne klare Richtung. Bevor weitere Kapazität gebunden wird, braucht es eine bewusste Entscheidung.

- **Angebot:** Lokale KI-Umgebung für UWI (Chat, Dokumentenverarbeitung, Suche)
- **Abhängigkeit:** Mac Studio Beschaffung ([#917](https://github.com/DaveX2001/deliverable-tracking/issues/917)) — Hardware-Entscheidung offen (DGX Spark vs Mac Studio)
- **Seit 5 Tagen:** Schließung angekündigt falls kein Interesse — Entscheidung noch ausstehend

**To resolve:** Klare Disposition — weiterführen mit definiertem Scope, in ein anderes Format umlenken, oder als nicht mehr relevant schließen.

## Meeting Format

- **Typ:** Review + Entscheidung
- **Dauer:** 40 min
- **Vorbereitung:** Retainer-Epic [#650](https://github.com/DaveX2001/deliverable-tracking/issues/650) durchsehen
- **Ergebnis:** Bereinigte Issue-Liste, Vertragsfreigabe IITR, Geschäftsregel Versicherungsrechnungen, BrokerBin Scope bestätigt, AI Workspace Entscheidung

## Related

- **Retainer Epic:** [#650](https://github.com/DaveX2001/deliverable-tracking/issues/650)
- **IITR Vertrag:** [#954](https://github.com/DaveX2001/deliverable-tracking/issues/954)
- **Insurance Billing:** [#1016](https://github.com/DaveX2001/deliverable-tracking/issues/1016)
- **BrokerBin:** [#1036](https://github.com/DaveX2001/deliverable-tracking/issues/1036)
- **AI Workspace:** [#735](https://github.com/DaveX2001/deliverable-tracking/issues/735)
