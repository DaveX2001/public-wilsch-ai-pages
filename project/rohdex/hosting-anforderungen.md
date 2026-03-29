---
publish: true
---

# Hosting-Anforderungen — ROHDEX Dokumentenverarbeitung
[[client-rohdex]]

Technische Anforderungen und Hosting-Optionen für die Migration des ROHDEX-Dokumentenverarbeitungssystems. Zielgruppe: IT-Dienstleister zur Machbarkeitsprüfung.

---

## Problem Statement

Das ROHDEX-Dokumentenverarbeitungssystem läuft aktuell auf der Infrastruktur von Wilsch AI Services (Hetzner VPS). Die Migration auf Rohdex-eigene Infrastruktur erfordert Koordination zwischen drei Parteien:

- **Rohdex (Konstantin Fitermann)** — Auftraggeber. Geschäftsbeziehung direkt mit Wilsch AI Services.
- **Gmelch IT-Systeme (Sikander Wenzel)** — IT-Dienstleister von Rohdex. Stellt Server-Infrastruktur bereit (VM, VPN, Backups, Netzwerk).
- **Wilsch AI Services (Marius Wilsch)** — Applikationsentwickler. Führt Migration durch, wartet das System gemäß SLA v4.

Gmelch IT stellt die Infrastruktur bereit und betreibt sie. Wilsch AI migriert die Applikation und wartet sie. Rohdex ist der Auftraggeber beider Leistungen.

**Preconditions:**
- Geschäftsbeziehung direkt zwischen Marius und Rohdex (kein Intermediär)
- SLA v4 countersigned (26.02.2026) — 90 EUR/h, keine Pauschale, 30-Minuten-Takte
- System produktiv auf WILSCH-AI-SERVER, verarbeitet aktiv Versanddokumente
- On-Premise-Entscheidung getroffen: Migration auf Rohdex-VM (RDX-APP-01)
- IT-Dienstleister: Gmelch IT-Systeme (Sikander Wenzel, sw@gmelch-itsysteme.de)
- VM bereitgestellt: 4 V-Cores, 16 GB RAM, 100 GB VHD (60 GB nutzbar), Docker installiert

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | System migriert auf RDX-APP-01, bekannte Bugs gefixt, alle Dokumenttypen funktional — Wartung läuft unter SLA v4 |
| **Success** | System produktiv auf Rohdex-Infrastruktur, kein Traffic mehr über Wilsch AI Server |
| **Done test** | E-Mails werden auf RDX-APP-01 verarbeitet und Dokumente korrekt zurückgesendet? Wenn ja → Migration abgeschlossen |

---

## Approach

### Part 1: Systemarchitektur

Das System ist ein einzelner Docker-Container (FastAPI, Python 3.11+) ohne Datenbank. Zustandslose Verarbeitung — keine persistenten Daten auf dem Server.

**Verarbeitungspipeline:**
1. IMAP-Polling holt E-Mails mit Excel-Anhängen (.xlsx, .csv) ab
2. Datenextraktion: Skriptbasierte Verarbeitung (openpyxl/csv — deterministisch)
3. Berechnungen: Python-basierte Kalkulationslogik
4. Dokumentgenerierung: 8 Excel-Vorlagentypen (Mail-Avis, VGM, Packliste, etc.)
5. SMTP-Versand: Fertige Dokumente per E-Mail zurück an Absender

**Externe Abhängigkeiten:**
- E-Mail-Konto mit IMAP- und SMTP-Zugang (einzige Pflicht-Abhängigkeit)
- GlitchTip/Sentry für Fehlermonitoring (optional)
- Keine KI-API-Aufrufe im laufenden Betrieb (validiert anhand Produktionslogs)

**Systemverhalten:**
- Polling-Intervall: ~10 Sekunden (konfigurierbar)
- Geringe CPU/RAM-Last — reine Dateiverarbeitung
- Kein ausgehender Internet-Traffic außer E-Mail und optionalem Monitoring

### Part 2: On-Premise-Infrastruktur (Rohdex / Gmelch IT)

**Bereitgestellte Infrastruktur (Gmelch IT-Systeme, Stand 26.02.2026):**

| Eigenschaft | Wert |
|------------|------|
| Servername | RDX-APP-01 |
| Interne IP | 192.168.44.11 |
| CPU | 4 V-Cores |
| RAM | 16 GB |
| Festplatte | 100 GB VHD (60 GB nutzbar nach Ubuntu-Installation) |
| Betriebssystem | Ubuntu Server |
| Docker | Neueste Version installiert |
| Backups | Täglich ab 22:00 (VM-Level, durch Gmelch) |
| Ausgehend | Sicherheitskritische Anwendungen + Länder (RU, CN etc.) gesperrt — IONOS-Mailserver (DE) nicht betroffen |

#### Zugang einrichten (Developer)

**Schritt 1: OpenVPN installieren (macOS)**

```bash
brew install openvpn
```

Pfad nach Installation: `/opt/homebrew/opt/openvpn/sbin/openvpn` (Apple Silicon) bzw. `/usr/local/opt/openvpn/sbin/openvpn` (Intel).

**Schritt 2: VPN-Konfiguration**

Die OpenVPN-Konfigurationsdatei `rohdex-vpn.ovpn` wird nicht im Repository gespeichert (gitignored). Datei von bestehendem Entwickler anfordern — enthält Serverzertifikate und Cipher-Konfiguration.

VPN-Server: `217.92.100.123:443` (OpenVPN, Gmelch IT — WatchGuard-Appliance mit OpenVPN-Protokoll)
Credentials: `.env`-Datei, Variablen `WATCH_GUARD_VPN_USERNAME` und `WATCH_GUARD_VPN_PASSWORD`.

**OpenVPN 2.7 Cipher-Konfiguration:** Die `.ovpn`-Datei enthält folgende Cipher-Einstellungen für OpenVPN 2.7 Kompatibilität:

```
cipher AES-256-CBC
data-ciphers AES-256-GCM:AES-128-GCM:AES-256-CBC
auth SHA256
```

`data-ciphers` ersetzt das veraltete `ncp-ciphers` ab OpenVPN 2.7. Ohne diese Zeile schlägt die Cipher-Negotiation fehl. Die `.ovpn`-Datei enthält auch `verify-x509-name` mit WatchGuard-Zertifikat — nicht ändern.

```bash
sudo /opt/homebrew/opt/openvpn/sbin/openvpn --config rohdex-vpn.ovpn
```

Verbindung steht wenn `Initialization Sequence Completed` erscheint. Terminal offen lassen — VPN läuft im Vordergrund.

**Schritt 3: SSH-Zugang**

SSH-Config-Eintrag erstellen (`~/.ssh/config`):

```
Host RDX-APP-01
    HostName 192.168.44.11
    User wilsch
```

**Passwort-Authentifizierung (Fallback):** Passwort in `.env` als `UBUNTU_PW`. Funktioniert sofort nach VPN-Verbindung.

**SSH-Key-Authentifizierung (empfohlen):** Nach erstem Passwort-Login SSH-Key hinterlegen:

```bash
ssh-copy-id RDX-APP-01
```

Danach passwortloser Zugang. VPN muss immer aktiv sein — `192.168.44.11` ist nur über den VPN-Tunnel erreichbar.

**Support:** Projektbezogen → Sikander Wenzel (sw@gmelch-itsysteme.de) direkt. Supportanfragen → support@gmelch-itsysteme.de

**Verantwortungsteilung (geklärt durch SLA v4 §1.3):**
- **Gmelch IT:** Infrastruktur — Server-Uptime, OS-Updates, Backups, Netzwerk, Docker-Engine
- **Marius:** Applikationsebene — Deployment, Wartung, Monitoring, Updates, Bugfixes

### Part 3: Migration

**Migrationsaufwand:** 1 Arbeitstag (8 Stunden, 720 EUR) — gemäß SLA v4 §1.1

| Schritt | Aufwand |
|---------|---------|
| Docker-Container deployen und konfigurieren | ~1,5h |
| Umgebungsvariablen setzen (E-Mail, Monitoring) | ~0,5h |
| E-Mail-Konnektivität verifizieren (IMAP + SMTP) | ~0,5h |
| DNS/SSL-Konfiguration falls erforderlich | ~0,5h |
| Funktionstest (→ Test-Rubrik unten) | ~2h |
| Inbetriebnahme und Übergabe | ~1h |
| Bugfix Tara-Berechnung (→ Part 5, #655) | ~1h |
| Puffer für Unvorhergesehenes | ~1h |

**Vorab-Prüfungen (nach VPN+SSH-Zugang, vor Migration):**

| Prüfung | Kommando | Erwartung |
|---------|----------|-----------|
| Docker-Gruppe | `groups wilsch \| grep docker` | `wilsch` ist Mitglied — kein `sudo` für Docker nötig |
| Ausgehender Git-Zugang | `git ls-remote https://github.com/MariusWilsch/rohdex-mvp.git` | Verbindung erfolgreich — GitHub nicht durch Firewall blockiert |
| Festplattenplatz | `df -h /` | Mindestens 10 GB frei für Docker-Images und Logs |

**Deployment-Methode (empfohlen): Git Clone + Deploy Key**

Repository ist privat (`MariusWilsch/rohdex-mvp`). Zugang auf RDX-APP-01 via GitHub Deploy Key einrichten:

1. SSH-Key auf Server generieren: `ssh-keygen -t ed25519 -C "rdx-app-01"`
2. Public Key als Deploy Key im Repository hinterlegen (Settings → Deploy Keys, Read-Only)
3. `git clone git@github.com:MariusWilsch/rohdex-mvp.git`
4. `.env` in Repository-Root kopieren
5. `make deploy`

Updates: `git pull && make deploy`

**Fallback: Docker Image Transfer (falls Git-Zugang scheitert)**

Lokal bauen und per SSH übertragen:

```bash
# Lokal
docker build -t rohdex-backend:latest .
docker save rohdex-backend:latest | gzip > rohdex.tar.gz
scp rohdex.tar.gz RDX-APP-01:~/

# Auf Server
docker load < rohdex.tar.gz
```

`docker-compose.yml` + `.env` separat auf den Server kopieren, dann `docker compose up -d`.

**Dead-Code-Cleanup (vor Migration):**
- AI-Pfad entfernen: `ai_data_extraction_service.py`, OpenRouter/Langfuse/Helicone Dependencies, DOCX-Handling
- GlitchTip/Sentry entfernen: `sentry-sdk[fastapi]`, kommentierter Init-Block, `/sentry-debug` Route, `SENTRY_DSN` Config
- Ungenutzte `.env`-Variablen: `OPENROUTER_API_KEY`, `HELICONE_API_KEY`, `LANGFUSE_SECRET_KEY`, `LANGFUSE_PUBLIC_KEY`, `SENTRY_DSN`

**E-Mail-Konfiguration (unverändert):**
- Konto: `export-ki@rohdex.com` (IONOS)
- IMAP: `imap.ionos.de:993` (TLS) — Polling alle ~60 Sekunden
- SMTP: `smtp.ionos.de:587` (TLS)
- Ordner: `Processed`, `Skipped` (automatisch angelegt)

#### Test-Rubrik

**Validierungskriterien:** Jeder Dokumenttyp wird auf drei Ebenen geprüft:
- **Werte:** Korrekte Zahlen, Berechnungen, Dezimalstellen
- **Formatierung:** Deutsche Zahlenformate (`11.982,30`), Zellstile, Schriftarten
- **Struktur:** Zellpositionen, Zeilenanzahl, Layout bei dynamischen Inhalten

Detaillierte Validierungsmethodik: ADR-014 (Visual Regression Testing) im Repository.

**Ground Truth:** Evaluation-Datasets (`evaluation/`) mit Input- und erwarteten Output-Dateien. Bereitgestellt von Konstantin Fitermann (Rohdex) als WAHRHEITSDATEI — verifizierte Kundendaten aus realen Aufträgen.

**Phase 1: Automatisierte Tests (~5 Min)**

`make test` auf RDX-APP-01 ausführen. Pytest validiert Zellplatzierung und Formaterhaltung für alle Dokumenttypen. Grün = Code funktioniert auf neuer Infrastruktur.

**Phase 2: End-to-End-Verifikation (~1h)**

Cutover-Strategie: Erst Gmail-Testkonto, dann IONOS-Produktion. Altes System (WILSCH-AI-SERVER) läuft während der Tests weiter — kein Produktionsrisiko.

| Schritt | Aktion | Erwartung |
|---------|--------|-----------|
| 1 | System auf RDX-APP-01 mit `WHICH_IMAP="GMAIL"` deployen | Container läuft, pollt Gmail-Konto |
| 2 | Test-Email an `rohdexautomation@gmail.com` mit Pacific-Dataset (1 Produkt) | System verarbeitet, sendet 8 Dokumente zurück |
| 3 | Output gegen Expected Output (`evaluation/Pacific/`) vergleichen | Werte + Format + Struktur stimmen überein |
| 4 | Test-Email mit Nishikawa-Dataset (2 Produkte) | System verarbeitet dynamische Produktblöcke korrekt |
| 5 | Output gegen Expected Output (`evaluation/Nishikawa/`) vergleichen | Werte + Format + Struktur stimmen überein |
| 6 | `WHICH_IMAP` auf `"IONOS"` umschalten, alten Server stoppen | System pollt `export-ki@rohdex.com` auf RDX-APP-01 |

**Dokumenttypen im Test (8 implementierte Typen):**

| # | Dokumenttyp | Tier | Testfokus |
|---|------------|------|-----------|
| 1 | Mail-Avis | Einfach | 2 Zellen (C12, E16) korrekt befüllt |
| 2 | VGM | Einfach | 6 Zellen korrekt, deutsches Zahlenformat |
| 3 | CTR AVIS | Einfach | 2 Zellen korrekt befüllt |
| 4 | DISPO | Mittel | Produktblöcke dynamisch eingefügt, Format erhalten |
| 5 | VET | Mittel | Produktblöcke wie DISPO, Veterinärzertifikat-Felder |
| 6 | Non-Wood | Mittel | Dynamische Zeileneinfügung, Holzfrei-Erklärung |
| 7 | Invoice | Mittel | Produktzeilen in Tabelle, HopLion-Variante automatisch geroutet¹ |
| 8 | Packing List | Komplex | Bundle-Rekonstruktion, Tara-Werte gerundet (→ #655) |

¹ HopLion-Variante (`InvoiceHopLionExcelStrategy`) wird automatisch erkannt wenn Zelle A5 "HOP LION FEATHER WORKS CORP." enthält. Gleicher Code-Pfad, kein separater Test nötig.

**Nicht im Test:** GW-List — geplant aber nicht implementiert (keine Strategy-Datei im Code). Falls benötigt → separates Feature-Issue.

**Pass/Fail-Kriterien:**
- `make test`: Alle Pytest-Tests grün
- E2E: Output-Dokumente stimmen mit Expected Output überein (Werte + Format + Struktur)
- Verantwortung: Developer führt Tests durch, Auftraggeber (Konstantin) bestätigt Inbetriebnahme

**Acceptance Criteria pro Dokumenttyp:**

Jeder der 8 Dokumenttypen folgt dem gleichen AC-Muster:

> **Given:** Input-Datei aus `evaluation/{Dataset}/input/` + zugehörige Log-CSV
> **When:** Entsprechende Strategy verarbeitet den Input
> **Then:** Output stimmt zellgenau mit `evaluation/{Dataset}/expected_output/{Typ}.xlsx` überein (Werte + Format + Struktur)

Beispiel — **Mail-Avis (Tier: Einfach):**
Given `MailAvis.xlsx` + `Log_37342.csv` aus Pacific-Dataset → When `MailAvisExcelStrategy` verarbeitet → Then C12 und E16 in Output = `MailAvis_2510155.xlsx`. Zwei Zellen, binärer Vergleich.

Beispiel — **Packing List (Tier: Komplex):**
Given `Vorlage Packliste BUNDLES.xlsx` + `Log_37342.csv` aus Pacific-Dataset → When `PackingListExcelStrategy` verarbeitet → Then Bundle-Rekonstruktion korrekt, Tara-Werte auf 1 Dezimalstelle gerundet (→ #655), Gesamtsumme = Log-Summe.

Developer definiert pro Dokumenttyp einen konkreten AC basierend auf diesem Muster und der Testfokus-Spalte oben. 8 ACs insgesamt — einer pro Zeile in der Tabelle.

### Part 4: SLA-Modell

**Superseded:** Retainer-Modell (525/325 EUR/Monat) durch SLA v4 ersetzt.

**Aktuelles Modell (SLA v4, signed 26.02.2026):**
- 90 EUR/h, 30-Minuten-Takte, keine Pauschale
- Infrastruktur: Rohdex-IT (Gmelch) — Server-Uptime, OS-Updates, Backups, Netzwerk, Docker
- Applikation: Marius — Wartung, Updates, Bugfixes, Template-Anpassungen
- Reaktionszeit: 1 Werktag (geschäftskritisch), Best-Effort (nicht-kritisch)

**Vollständiger Vertrag:** [SLA v4 Wartungsvertrag](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/sla-v4-wartungsvertrag)

### Part 5: Bekannte Probleme (Spec-Design durch Developer)

**#655: Tara-Berechnung — Verlustbehaftete Mittelwertbildung in Extraktion**

**Symptome:** Packing List zeigt übermäßige Nachkommastellen bei Tara-Werten (z.B. `3.2142857142857157` statt `3.2`) und die Gesamtsumme weicht von der Log-Datei ab (+0.043 kg bei 32 Ballen). Beide Symptome haben die gleiche Ursache.

**Ursache (verifiziert an Fall 2610023):** Die CSV-Extraktion in `_extract_from_partie_csv()` berechnet pro Zeile `tare_value / number_of_bags`, mittelt alle Werte zu EINEM `tare_per_bag` pro Partie, und speichert nur diesen Durchschnitt. Die ursprünglichen Tara-Werte pro Ballen gehen verloren. `BundleDetail` hat kein Feld für den Original-Tara. In `calculation_service.py` wird `tare_per_bag` zurückmultipliziert (`num_bags * tare_per_bag`), was bei heterogenen Ballen systematisch falsche Werte produziert.

**Beweis — Fall 2610023 (Log_38302M.csv):**
- 24 Ballen: 8 Säcke, Tara=3,2 → tare_per_bag=0,4
- 7 Ballen: 7 Säcke, Tara=2,8 → tare_per_bag=0,4
- 1 Ausreißer (Ballen 12): 7 Säcke, Tara=3,2 → tare_per_bag=0,457
- Gemittelt: `tare_per_bag = 0,4018` → Rückmultiplikation: 8 × 0,4018 = 3,2143 (statt 3,2), Ballen 12: 7 × 0,4018 = 2,8125 (statt 3,2 → **Fehler: −0,39 kg**)

**Systemisch:** Jede Lieferung mit unterschiedlichen Tara-pro-Sack-Verhältnissen über Ballen hinweg ist betroffen. Die Evaluierungs-Datensätze (Pacific, Nishikawa) haben zufällig uniforme Verhältnisse und maskieren den Bug.

**Test-Lücke:** Kein End-to-End-Test für die Pipeline `_extract_from_partie_csv()` → `CalculationService.perform_calculations()`. Die `conftest.py` umgeht Extraktion und Berechnung vollständig (hardcodierte `CalculatedShipmentData`). Der Bug war strukturell nicht testbar.

**Fix-Ansatz (zweiphasig):**
- **Phase 1 (~30 Min, innerhalb SLA):** Runden auf 1 Dezimalstelle in `calculation_service.py` + Excel `number_format='0.0'` in `packing_list_excel_strategy.py`. Behebt Display-Symptome, Mittelwert-Problem bleibt.
- **Phase 2 (~2–3 Std, Follow-up):** Schema-Änderung — `tare_weight`-Feld zu `BundleDetail` hinzufügen. Extraktion speichert Original-Tara pro Ballen aus CSV. `calculation_service.py` nutzt `tare_weight` direkt statt `num_bags * tare_per_bag`. Beseitigt Datenverlust.

**Datenquellen:** 📧 [Tara Problem Email (24.02.2026)](https://mail.google.com/mail/u/0/#all/19c8f8c66c05d43d) — Anhänge: `WAHRHEITSDATEI_2610023_.xlsx`, `Log_38302M.csv`, `PackingList_2610023.xlsx`, `Vorlage Packliste BUNDLES.xlsx` · 📧 [Original-Report Roxana (29.01.2026)](https://mail.google.com/mail/u/0/#all/19c09019e3c0afee)
**Code:** `app/services/data/data_extraction_service.py` (Zeilen 375–450 — Mittelwertbildung), `app/services/data/calculation_service.py` (Zeilen 87–121 — Rückmultiplikation), `app/services/document/strategies/packing_list_excel_strategy.py` (Zeilen 190–200 — Zellwerte ohne Rundung)
**Issue:** [#655](https://github.com/DaveX2001/deliverable-tracking/issues/655)

**#585: Veterinary BUSAN — Integrationsumfang definieren (abgeschlossen)**

Konstantin sandte `Veterinary BUSAN.docx` (Beispiel-Dokument, 03.12.2025) — ein Anschreiben an die Veterinärbehörde ("Sehr geehrte Frau Rieder, wir bitten Sie höflich um Ausstellung folgender Dokumente: VETERINARY CERTIFICATE"). Das Dokument ist eine Word-Datei (.docx), kein Excel-Template. "BUSAN" bezieht sich auf den Zielhafen (Busan, Südkorea) in den Lieferbedingungen (CIF BUSAN).

**VET-Template-Review (abgeschlossen):** Vergleich des `VetExcelStrategy`-Outputs mit dem Beispieldokument ergibt: Alle 7 dynamischen Felder, die das System befüllt, stimmen mit dem BUSAN-Referenzdokument überein.

| Feld | VetExcelStrategy | BUSAN.docx |
|------|-----------------|------------|
| Ballen + Einheit | ✅ B28 | "8 BUNDLES" |
| Produktbeschreibung | ✅ B29 | "UKR. GREY DUCK DOWN ABT.57%" |
| Bruttogewicht | ✅ B31 (deutsches Format) | "4,541.60KGS GROSS" |
| Nettogewicht | ✅ B32 (deutsches Format) | "4,504.40KGS NET" |
| Marks/Partienummer | ✅ C33 | "MARKS: 37101" |
| Containernummer | ✅ dynamische Suche | "KOCU 423033-2" |
| Siegelnummer | ✅ dynamische Suche | "GW342782" |

Vier weitere Felder im BUSAN-Dokument (Empfänger/Consignee, Lieferbedingungen/CIF, Ausstellungsdatum, Sterilisierungsvermerk) werden von Konstantin im Excel-Template vorausgefüllt — keine Systemverantwortung.

**Ergebnis:** Kein Codeänderungsbedarf am bestehenden VET-Excel-Output.

**Undefined:** VET-Ausgabeformat — Ob VET-Output zusätzlich als DOCX generiert werden soll (aktuell nur Excel). Konstantin sandte BUSAN.docx als Beispiel, unklar ob als gewünschtes Ausgabeformat oder nur als Referenz. Bei Bestätigung: ~4–7h Aufwand (€360–630). → [Meeting Agenda: VET DOCX + Invoice](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/vet-docx-invoice-agenda) Topic 1

**Korrektur: Rezept/Material-Datenintegration entfernt.** Die JSON-Datenstruktur (Recipe ID → Materials Array) und die 500MB+ Datenlieferung gehören zum AVO-Projekt (Empfänger: Mattis von Stevendaal, AVO). Der Eintrag wurde durch eine fehlerhafte Transcript-Zuordnung dem ROHDEX-Issue #585 zugewiesen. Verifiziert anhand der E-Mail "Abstimmung zum Aufbau der JSON-Datei" (Thomas Erhard → Mattis.vonStevendaal@avo.de). Kein Bezug zu ROHDEX Veterinärdokumenten.

**Datenquellen:** 📧 [VETERINÄR Email (03.12.2025)](https://mail.google.com/mail/u/0/#all/19ae34999468ca08) — Anhang: `Veterinary BUSAN.docx`
**Code:** `app/services/document/strategies/VetExcelStrategy` (strukturell identisch mit DISPO)
**Issue:** [#585](https://github.com/DaveX2001/deliverable-tracking/issues/585)

---

## Source

- **Transcript:** [Rohdex Call — Hosting Requirements (Feb 18, 10 min)](https://app.fireflies.ai/view/01KHRFX1ESBZ7CJ8Z4X69CS4XK) — Anforderungen und Hosting-Optionen besprochen
- **Email Thread:** [Migration Programm ROHDEX (Feb 25-26)](https://mail.google.com/mail/u/0/#all/19c9530e9b21c06b) — Konstantin → Sikander → Marius: VM-Vorbereitung, VPN-Zugang, Server-Details
- **VPN Credentials:** [Keeper Link (Feb 26)](https://mail.google.com/mail/u/0/#all/19c9a93fce2727cf) — OpenVPN SSL-VPN
- **SSH Credentials:** [Keeper Link (Feb 26)](https://mail.google.com/mail/u/0/#all/19c9a955f43ac511) — Ubuntu-Benutzer RDX-APP-01
- **SLA v4:** [Wartungsvertrag (signed 26.02.2026)](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rohdex/sla-v4-wartungsvertrag)
- **Tara Bug:** [Email (Feb 24)](https://mail.google.com/mail/u/0/#all/19c8f8c66c05d43d) — Input/Output-Daten zum Tara-Problem
- **Veterinary BUSAN:** [Email (Dec 3, 2025)](https://mail.google.com/mail/u/0/#all/19ae34999468ca08) — Beispiel-Dokument
- **Code Analysis:** Codebase at `~/Documents/projects/billable/ROHDEX/` — AI-Pfad nie getriggert (production validated)
- **Issue:** [#909 Dokumentenverarbeitung SLA & Wartung](https://github.com/DaveX2001/deliverable-tracking/issues/909) (Epic)
- **Sub-Issues:** [#961 Migration](https://github.com/DaveX2001/deliverable-tracking/issues/961), [#655 Tara Fix](https://github.com/DaveX2001/deliverable-tracking/issues/655), [#585 BUSAN](https://github.com/DaveX2001/deliverable-tracking/issues/585)
- **Session (Design Doc Creation):** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/eb15ce95-2336-43e6-baa8-a92e2e6b1adf.jsonl
- **Session (Extraction Pass 1 — Test-Rubrik):** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/4eacfe98-8591-469f-9930-2e91a9468c40.jsonl
- **Codebase:** [MariusWilsch/rohdex-mvp](https://github.com/MariusWilsch/rohdex-mvp) — Strategy-Dateien, Evaluation-Datasets, ADR-014
- **Email Thread (VPN):** [Migration VPN Troubleshooting (Feb 26 — Mar 2)](https://mail.google.com/mail/u/0/#all/19c9530e9b21c06b) — OpenVPN setup, Keeper-Links, Geo-Blocking-Fix
- **OpenVPN Config:** [rohdex-vpn.ovpn (Google Drive)](https://drive.google.com/file/d/1g2r7GJcug9iVcybXay1ZZyJoKE7Gf4iF/view) — Cipher-Konfiguration für OpenVPN 2.7
- **Session (Extraction Pass 2 — VPN/SSH Zugang):** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/3b53456f-4ba4-41a5-a16d-207a99ba8e42.jsonl
- **Session (Extraction Pass 3 — Tara #655):** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/93486107-19c1-4502-8861-c22d37d87e60.jsonl
- **Session (Extraction Pass 4 — BUSAN #585):** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/8c4a1a6d-6f2f-4e45-9588-1f321e7027c6.jsonl
- **Session (Extraction Pass 5 — SA Feedback):** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/290d1003-c3e9-443c-99f3-bfb8b3a81bb0.jsonl
- **Session (Extraction Pass 6 — Meeting Agenda Reformat + Undefined B Correction):** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-04-ROHDEX--deliverable/3060c61b-9d4e-4f7d-8f2f-eb6a9b170fec.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

