---
publish: true
---

# Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung
[[client-rohdex]]

Technische Anforderungen für die Migration des ROHDEX-Dokumentenverarbeitungssystems auf Rohdex-Infrastruktur.

---

## 1. Systemübersicht

Das System ist ein einzelner Docker-Container (FastAPI, Python 3.11+) ohne Datenbank. Zustandslose Verarbeitung — keine persistenten Daten auf dem Server.

**Verarbeitungspipeline:**
1. IMAP-Polling holt E-Mails mit Excel-Anhängen (.xlsx, .csv) ab
2. Datenextraktion: Skriptbasierte Verarbeitung (deterministisch)
3. Berechnungen: Python-basierte Kalkulationslogik
4. Dokumentgenerierung: 9 Excel-Vorlagentypen (Mail-Avis, VGM, Packliste, etc.)
5. SMTP-Versand: Fertige Dokumente per E-Mail zurück an Absender

**Externe Abhängigkeiten:**
- E-Mail-Konto mit IMAP- und SMTP-Zugang (einzige Pflicht-Abhängigkeit)
- Fehlermonitoring (optional)
- Keine KI-API-Aufrufe im laufenden Betrieb

**Systemverhalten:**
- Polling-Intervall: ~10 Sekunden (konfigurierbar)
- Geringe CPU/RAM-Last — reine Dateiverarbeitung

---

## 2. On-Premise-Hosting (Rohdex-Infrastruktur)

**Mindestanforderungen:**

| Anforderung | Minimum | Empfohlen |
|------------|---------|-----------|
| CPU | 2 Kerne, x86-64 | 4 Kerne |
| RAM | 4 GB dediziert | 8 GB Gesamt-Host |
| Festplatte | 40 GB (SSD empfohlen, HDD ausreichend) | 80 GB |
| Betriebssystem | Linux-basiert (Ubuntu empfohlen) | — |
| Container-Runtime | Docker Engine 24.x+ | — |
| Netzwerk ausgehend | IMAP und SMTP Ports offen | — |
| Netzwerk eingehend | SSH-Zugang für Deployment | — |
| Internet | Business-Leitung, >10 Mbit/s | — |

**Machbarkeitsbewertung (Stand Feb 2026):**
Die von Rohdex-IT angebotenen Spezifikationen (4 V-Cores, 16 GB RAM, 120 GB Speicher) übertreffen alle Mindestanforderungen — On-Premise-Hosting ist aus Hardware-Sicht machbar.

**Vorbereitung durch Rohdex-IT (vor Migrationstermin):**
1. Ausgehende Ports freigeben: IMAP (993) und SMTP (587 oder 465)
2. SSH-Zugang oder Remote-Zugriff für Wilsch AI Services einrichten

Alle weiteren Schritte (Docker-Installation, Konnektivitätstest, App-Deployment) übernimmt Wilsch AI Services nach Erhalt des Zugangs.

**Risikofaktoren:**
- 24/7-Verfügbarkeit abhängig von eigener Wartungsdisziplin
- Keine automatischen Backups ohne explizite Backup-Strategie

---

## 3. Migration

**Vorbereitung durch Rohdex-IT (vor Migrationstermin):**

1. Ausgehende Ports freigeben: IMAP (993) und SMTP (587 oder 465)
2. SSH-Zugang für Wilsch AI Services einrichten
3. Docker Engine 24.x+ auf der virtuellen Maschine installieren

Alle weiteren Schritte (Deployment, Konfiguration, Test, Inbetriebnahme) übernimmt Wilsch AI Services nach Erhalt des Zugangs.

---

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

