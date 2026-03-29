---
publish: true
---

# IITR Vertragsentwürfe — Besprechung Kraska (4. März 2026)

[[client-iitr]]

## Meetingziel

Den offenen Punkt aus dem Wartungsvertrag-Entwurf klären: welche Infrastrukturebenen der
Vertrag abdeckt und wo die Verantwortung an Drittanbieter (Hetzner, Primeline) übergeht.
Das Ergebnis wird als Vertragsergänzung zu §2.1 dokumentiert.

## Pre-Read

- [IITR Vertragsrahmen — Kommerzielle Rahmenbedingungen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-contract-extension-framework) — Fokus auf die offene **Undefined**-Markierung (Scope-Grenze) in Abschnitt 3
- [Wartungsvertrag](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/wartungsvertrag-iitr) — Vertragsentwurf, §2.1b ist der offene Punkt
- [Werkvertrag-Rahmenvereinbarung](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/werkvertrag-rahmenvereinbarung-iitr) — Vertragsentwurf, Festpreis-Modell

---

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Betreuungs-Scope und Haftungsgrenze
⏱️ 10 Min.

Der Wartungsvertrag sagt "dedizierte Betreuung unseres Servers" — aber es existieren vier
Infrastrukturebenen, jede mit einem anderen Verantwortlichen:

| Ebene | Aktueller Eigentümer | Marius' Rolle |
|-------|---------------------|---------------|
| **Applikation** (KI-Code, Docker, Ollama) | Marius | Baut und wartet |
| **Middleware** (OS-Dienste, Netzwerk) | Unklar | Verwaltet in der Praxis |
| **Hardware** (Staging: Hetzner Bare Metal; Produktion: Primeline) | Hetzner / Primeline | Kein Eigentum |
| **Netzwerk** (Hetzner 99% SLA; Primeline 4h Reaktion Mo–Fr) | Hetzner / Primeline | Keine Kontrolle |

- Marius' aktueller Ansatz: alles übernehmen, was er kann, eskalieren was seine Fähigkeiten übersteigt
- Primeines Produktionsgarantie deckt Hardware-Austausch ab (36 Monate, 4h Reaktionszeit) — aber nur Mo–Fr
- Wenn Primeline bei einem Hardware-Ausfall langsam reagiert, ist IITR offline. Die Vertragssprache bestimmt, ob das Marius' Haftung ist oder nicht
- Ulrich hat das im Gespräch am 27. Feb. als "riesiges Risiko" eingestuft, wenn der Vertrag keine klare Grenze zieht

**To resolve:**
- Welche Infrastrukturebenen der Wartungsvertrag abdeckt — Applikation und Middleware (Docker, OS, Nginx, GPU-Treiber, Speicher, Firewall) vs. Hardware und externes Netzwerk (Primeline, Hetzner)
- Bei Ausfällen außerhalb des Vertragsumfangs — übernimmt Marius die Fehlererkennung und Weiterleitung an den zuständigen Drittanbieter, oder koordiniert IITR die eigenen Lieferantenbeziehungen direkt?

### 2. Staging-System — Reaktion auf Stellmacher-Meldung
⏱️ 5 Min.

Am 26. Februar meldete Herr Stellmacher eine Latenz von über acht Minuten auf dem
Cloud-Staging-System. Die Ursache — ein GPU→CPU-Fallback — wurde am selben Tag identifiziert
und behoben.

- Reaktionszeit: unter 24 Stunden (innerhalb der vorgeschlagenen SLA-Grenzen)
- GPU/CPU-Switching liegt auf der Middleware-Ebene — illustriert die Scope-Frage aus Thema 1
- Dieser Vorfall zeigt die bestehende Betreuungsrealität, die der Wartungsvertrag formalisiert

### 3. Vertragsentwürfe — Rückfragen und Feedback
⏱️ 10 Min.

Beide Vertragsentwürfe (Wartungsvertrag + Werkvertrag-Rahmenvereinbarung) wurden am
2. März als Entwurf übermittelt. Dieser Block gibt Raum für Rückfragen und erstes Feedback.

- Wartungsvertrag: 300 EUR/Monat Bereitschaft + 120 EUR/h nach Aufwand
- Werkvertrag: Festpreis pro Einzelauftrag, Pflichtenheft-basiert
- Beide Verträge unabhängig voneinander kündbar
- Zahlungsbedingungen: Netto 30, Reverse-Charge-Verfahren

**To resolve:** Offene Fragen oder Anpassungswünsche des Auftraggebers zu den
Vertragsentwürfen identifizieren.

### 4. Hetzner-Konto — Zugangsbereinigung
⏱️ 3 Min.

Das Hetzner-Konto (Serververwaltung über Web-Konsole) ist durch
Zwei-Faktor-Authentifizierung gesichert und liegt in IITRs Verantwortung
als Kontoinhaber. Unabhängig vom VPN-Status ist zu klären, ob Roman
Zugang zur Hetzner-Konsole hatte.

- Repository- und Staging-Zugänge sind bereits gesperrt (3 Repos entzogen,
  Staging SSH key-only, veraltete Nutzer entfernt)
- Hetzner-Kontoverwaltung liegt bei IITR — Passwortrotation dort ist eine IITR-Aufgabe
- Hetzner-Zugang ist vom VPN unabhängig (Web-Interface, kein SSH)

**To resolve:** Ob Roman Zugang zur Hetzner-Konsole hatte und ob IITR
die Zugangsdaten unabhängig prüft und rotiert.

### 5. SSL-VPN-Migration zu IPsec — Pilotnutzer-Phase abschließen
⏱️ 5 Min.

Im Januar 2026 kündigte SpaceNet die Ablösung der SSL-VPN durch IPsec DialIn an
(Ticket #202601080215). Roman hat gegenüber SpaceNet bestätigt, dass VPN weiterhin
benötigt wird — die Pilotnutzer-Phase blieb jedoch bei Schritt 1 stehen. Aktuell
ist das Produktionssystem für niemanden extern erreichbar: Verbindungsversuche über
die bestehende SSL-VPN-Konfiguration laufen ins Timeout.

- Roman war im E-Mail-Verkehr als VPN-Nutzer gelistet (CC auf SpaceNet-Korrespondenz)
- SpaceNet benötigt einen benannten Pilotnutzer für die neue IPsec-Einwahl
- Nach Romans Ausscheiden sollte er kein neues VPN-Profil erhalten

**To resolve:** Wer kontaktiert SpaceNet zur Wiederaufnahme der IPsec-Migration und
stellt sicher, dass das neue Profil nur an autorisierte Nutzer verteilt wird.

## Meeting-Format

- **Art:** Vertragsbesprechung (Marius × Sebastian Kraska, ggf. Eckehard Kraska + Stellmacher)
- **Dauer:** 38 Min.
- **Zoom:** https://us06web.zoom.us/j/9440418944
- **Erwartung:** Kraska hat die beiden Vertragsentwürfe (Wartungsvertrag + Werkvertrag) vom 2. März gesichtet
- **Ergebnis:** Scope-Grenze definiert (Vertragsergänzung §2.1), offene Fragen geklärt, nächste Schritte zur Unterzeichnung

## Verknüpfungen

- **Issue:** [#954 — Prepare extension contract for signing](https://github.com/DaveX2001/deliverable-tracking/issues/954)
- **Issue:** [#963 — Revoke Roman's system access + password rotation](https://github.com/DaveX2001/deliverable-tracking/issues/963)
- **Email:** [SpaceNet VPN-Migration — SSL→IPsec (Jan 8–20)](https://mail.google.com/mail/u/0/#all/19b9e05c4ff33b11)
- **Design Doc:** [IITR Contract Extension — Commercial Framework](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/iitr-contract-extension-framework)
- **Transkript:** [Marius × Ulrich — Contract Extension Pricing Strategy (27. Feb.)](https://app.fireflies.ai/view/01KJEVQ0Q6XWZNZP9P1FTPB2VW)
- **Email:** [Sebastian Kraska — u.a. Staging System, Vergütung (Mar 3)](https://mail.google.com/mail/u/0/#all/19cb5178aea60dd3)
- **Email:** [Sebastian Kraska — Austausch KI-System (Mar 3)](https://mail.google.com/mail/u/0/#all/19cb5170512d9850)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/9f14b021-2a4f-4da9-8925-b30c9eb7d426.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/2c6e2b5f-7d4a-493a-8bc7-d441c20c6090.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/ef894185-0251-4ebf-8d42-5e08e3b9961c.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

