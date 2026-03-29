---
publish: true
---

# Meeting-Agenda: UWI Daily Sync — 27. Februar 2026
[[client-wilsch-group]]

## Meeting Goal

Zwei Ziele: (1) Fortschrittsbericht über UWI-Milestone [UWI] Mar 4 — was ist passiert seit letztem Sync am 25. Feb. (2) IITR-Vertragsverlängerung vorbereiten — Ulrich bekommt die Hardware-/Software-Konfiguration und offenen Fragen, damit er das Full Service Paket entwerfen kann.

1. **UWI Status klar** — Ulrich kennt den Stand aller Milestone-Items + offene Entscheidungspunkte
2. **IITR-Vertrag vorbereitet** — Konfigurationsliste, Warranty-Info und Service-Scope übergeben. Nächste Schritte (Telco + München-Termin) vereinbart

## Pre-Read

- [Meeting-Agenda: Daily Sync 25. Feb](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-daily-sync-2026-02-25)
- [Milestone: [UWI] Week March 3](https://github.com/DaveX2001/deliverable-tracking/milestone/10)
- [Mac Studio Beschaffung — Recherche & Handlungsoptionen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/mac-studio-beschaffung) *(Thomas-Anruf-Reihenfolge)*

---

## Discussion Topics

*Starting points for discussion, not limited to these.*

### 1. Rekers Workshop 2 Rechnung
⏱️ 5 min

Workshop 2 fand am 27.-28. Januar statt (2 Tage vor Ort, ~9,5 Stunden). Angebot V006 über WPH, Pricing "TBD by Uli". Team: Marius (Prozessanalyse), Thomas (Technische Analyse), Ulrich (Vertrieb).

- Angebot V006 listet Gesamtbetrag als "x.500 EUR" — finale Zahl von Ulrich gesetzt
- Rechnungsempfänger unklar: WPH oder Ulrich persönlich?
- Workshop liegt einen Monat zurück

**To resolve:** Marius' Rechnungsbetrag und Rechnungsempfänger klären.

### 2. Fireflies Auto-Email — Empfängerliste und Priorität
⏱️ 5 min

Automatische 5-Punkte-Zusammenfassung nach Kundenmeetings. Spec-Redesign läuft (David). Zwei offene Punkte brauchen Ulrichs Input.

- Empfängerliste: Wer bekommt Zusammenfassungen? Kunden dürfen auf keinen Fall einbezogen werden
- Priorität: Aktuell hinter Dialpad (#784) und David-Training (#895) eingeordnet
- Redesign in Arbeit: konfigurierbares Empfänger-System + Datenschutz-Absicherung

**To resolve:** Empfängerliste festlegen und Priorität relativ zu Dialpad und David-Training bestätigen.

### 3. Ostermann Dokumentation — Noch relevant?
⏱️ 3 min

Ulrich bat um AI-generierte Verbesserung dünner Projektdokumentation für Ostermann. Rohmaterial sollte von Ulrich kommen (Passwörter vorher redigieren). Status unklar — weder Marius noch David wissen, ob das noch aktuell ist.

- [Email vom 20. Feb](https://mail.google.com/mail/u/0/#all/19c79c6ccd04d4ec) — möglicherweise die angekündigten Rohdokumente
- Weder in Grooming noch im letzten Sync thematisiert

**To resolve:** Bestätigung ob Ostermann-Dokumentation noch gewünscht ist und was genau geliefert werden soll.

### 4. Mac Studio Beschaffung — Status und Compute-as-a-Service Angebot
⏱️ 5 min

Anbieter-Recherche abgeschlossen, Handlungsdokument veröffentlicht, E-Mail an Thomas gesendet. Antwort steht aus. Zusätzlich: die gleiche Hardware könnte als Pay-as-You-Go Compute an externe Kunden (Nuca Systems) angeboten werden.

- [Mac Studio Beschaffung — Recherche & Handlungsoptionen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/mac-studio-beschaffung)
- Erste Priorität: INKI, Flex IT Rent — 6- und 12-Monatslaufzeiten
- Compute-as-a-Service (#988): AI-Frontier-Team bei Nuca Systems als erster Zielkunde
- Überschneidung mit IITR (#954): Ulrich braucht Apple-Mietpreis für Vertragspaket

**To resolve:** Thomas-Antwort abwarten und Compute-as-a-Service als zusätzliche Einnahmequelle bewerten.

### 5. Fortschrittsbericht — Milestone [UWI] Mar 4
⏱️ 5 min

8 offene Items im Milestone, 5 Tage bis Fälligkeit. Kein Diskussionsbedarf — reiner Statusüberblick.

| # | Item | Status |
|---|------|--------|
| #784 | Dialpad VoIP Migration | In Grooming mit David, Redesign läuft |
| #894 | Google Meet Doku für Ulrich | Approved, David erstellt PDF |
| #918 | Vertragskonsolidierung | In Arbeit (Marius) |
| #895 | David AI Training Sessions | In Grooming-Planung heute |
| #893 | DNS Subdomain Setup | Spec-Design durch David, Grooming heute |
| #779 | Fireflies Auto-Email | Redesign läuft (siehe Topic 2) |
| #866 | Ostermann Dokumentation | Unklar (siehe Topic 3) |
| #917 | Mac Studio AI Dev Environment | Hardware-Entscheidung ausstehend (siehe Topic 4) |

**Abgeschlossen seit 25. Feb:** #928 (WPH INTEVA Angebot kondensiert), #836 (Time-Tracking Strategie), #724 (FSO Marketing Follow-up)

**To resolve:** Statusbestätigung — keine Diskussion.

### 6. IITR Vertragsverlängerung — Datenvalidierung und nächste Schritte (#959)
⏱️ 15 min

Stellmacher hat am 26. Feb schriftliche Rahmenbedingungen angefragt ([Email](https://mail.google.com/mail/u/0/#all/19c9a411906d76ee)). Hier ist die gesammelte Konfiguration beider Maschinen — Ulrich prüft, ob alle Daten für das Full Service Paket vorliegen.

**Staging (Hetzner GEX44 — €184/Monat):**
- Intel i5-13500, 64 GB DDR4, NVIDIA RTX 4000 SFF (20 GB vRAM)
- 2x 1.7 TB NVMe RAID-1, Ubuntu 24.04.2 LTS
- Warranty: Hetzner Datacenter, 99.9% Netzwerk-SLA, kein RTO-Commitment
- ⚠️ Kein Backup vorhanden
- ⚠️ Chunkr/Trieve Lizenzen: noch aktiv? Kosten prüfen

**Produktion (primeLine BTO — €27.605 Kaufpreis, Sept 2023):**
- 2x AMD EPYC 9124 (32C/64T), 768 GB DDR5 ECC, 4x RTX 4090 (96 GB vRAM)
- 4x 7.68 TB Kioxia RAID-10 + 2x 1TB RAID-1 (OS)
- Warranty: primeLine 36 Monate, Vorabaustausch, 4h Reaktionszeit, Mo-Fr 8-17 Uhr
- VEEAM Backup Agent installiert
- Sicherheitsaudit Staging abgeschlossen, Produktion ausstehend (VPN-Zugang nötig)

**Service-Scope (was Marius leistet):**
- OS-Wartung (automatische Security-Patches + manuelle Kernel-Updates)
- Docker Container Management (~70 Container auf Staging)
- RAG-Pipeline Entwicklung + Konfiguration
- Monitoring: SigNoz, Langfuse, OpenLIT
- Alle Tools Open-Source — keine Lizenzkosten

**Offene Punkte aus dem Transkript vom 25. Feb:**
- [ ] Telco mit IITR/Stellmacher als Zwischenschritt — wann?
- [ ] München-Termin Mitte März mit Ulrich — Datum festlegen
- [ ] Apple Mac Mietpreis — Thomas-Antwort abwarten (#929)
- [ ] Prod-Sicherheitsaudit — VPN-Zugang organisieren (#963)

**Quellen:**
- Staging: [Hetzner GEX44 Produktseite](https://www.hetzner.com/dedicated-rootserver/dedicated-rootserver/gex44/)
- Produktion: Hardware Specifications PDF + Server Configuration Report (primeLine, Sept 2023)
- Stellmacher Follow-up: [Email 26. Feb — Rahmenbedingungen](https://mail.google.com/mail/u/0/#all/19c9a411906d76ee)

**To resolve:** Validierung ob diese Daten ausreichen für den Vertragsentwurf, und Festlegung der nächsten Schritte (Telco-Termin, München-Datum, fehlende Informationen).

## Meeting Format

- **Type:** Daily Sync (zweigeteilt: Fortschrittsbericht + Arbeitssitzung)
- **Erwartung:** Ulrich prüft IITR-Konfigurationsdaten vorab
- **Outcome:** UWI-Status bestätigt, IITR-Datenlücken identifiziert, nächste Schritte (Telco + München-Termin) vereinbart

## Related

- **Epic:** [#650 UWI — Retainer](https://github.com/DaveX2001/deliverable-tracking/issues/650)
- **Epic:** [#959 IITR — Contract Extension](https://github.com/DaveX2001/deliverable-tracking/issues/959)
- **Milestone:** [[UWI] 2026-03-04 — Ulrich sync](https://github.com/DaveX2001/deliverable-tracking/milestone/10)
- **Milestone:** [[IITR] 2026-03-02 — Contract ready](https://github.com/DaveX2001/deliverable-tracking/milestone/16)
- **Letzte Agenda:** [Daily Sync 25. Feb](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/uwi-retainer/meeting-agenda-daily-sync-2026-02-25)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

