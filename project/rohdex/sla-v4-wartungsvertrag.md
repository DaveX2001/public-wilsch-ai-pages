---
publish: true
---

# Wartungsvertrag — ROHDEX Dokumentenverarbeitung
[[client-rohdex]]

---

## Dienstleistungsvertrag

Dieser Dienstleistungsvertrag („Vereinbarung"), datiert auf den 24. Februar 2026 („Wirksamkeitsdatum"), wird geschlossen zwischen:

**ROHDEX GmbH & Co. KG** („Kunde")
- Handelsregisternummer: HRA 74823 (Amtsgericht München)
- Adresse: Morsestraße 8, D-85716 Unterschleißheim
- Ansprechpartner: Konstantin Fitermann, fitermann@rohdex.de

und

**Wilsch AI Services OÜ** („Dienstleister")
- Registernummer: 17341774
- Adresse: Sepapaja tn 6, 15551 Tallinn, Harju Maakond, Estland
- Ansprechpartner: Marius Wilsch, marius@wilsch-ai.com

---

## 1. Leistungsumfang

### 1.1 Einmalige Migration

Der Dienstleister migriert das bestehende Dokumentenverarbeitungssystem auf die On-Premise-Infrastruktur des Kunden.

| Leistung | Aufwand |
|----------|---------|
| Docker-Container deployen und konfigurieren | ~1,5h |
| Umgebungsvariablen setzen (E-Mail, Monitoring) | ~0,5h |
| E-Mail-Konnektivität verifizieren (IMAP + SMTP) | ~0,5h |
| DNS/SSL-Konfiguration falls erforderlich | ~0,5h |
| Funktionstest aller 9 Verarbeitungsschritte | ~2h |
| Inbetriebnahme und Übergabe | ~1h |
| Bugfix Tara-Berechnung (Nachkommastellen-Fehler) | ~1h |
| Puffer für Unvorhergesehenes | ~1h |
| **Gesamt** | **~8h** |

**Migrationskosten:** 720 EUR (8 Stunden × 90 EUR/Stunde)

### 1.2 Laufende Wartung (App-Ebene)

Der Dienstleister erbringt laufende technische Unterstützung auf der Applikationsebene:

- Fehlerbehebung und Problemlösung
- Leistungsüberwachung (GlitchTip)
- Kleinere Erweiterungen und Konfigurationsänderungen
- Template-Anpassungen
- Technische Beratung und Unterstützung

### 1.3 Abgrenzung: Infrastrukturverantwortung

Die Infrastrukturverantwortung (Server-Uptime, OS-Updates, Backups, Netzwerk, Docker-Engine) liegt beim IT-Dienstleister des Kunden. Der Dienstleister ist ausschließlich für die Applikationsebene verantwortlich.

---

## 2. Laufzeit und Dauer

Diese Vereinbarung beginnt am Wirksamkeitsdatum und läuft auf unbestimmte Zeit. Jede Partei kann mit 30 Tagen schriftlicher Kündigungsfrist kündigen.

---

## 3. Pflichten des Kunden

Der Kunde verpflichtet sich:

- SSH-Zugang zur On-Premise-Infrastruktur bereitzustellen
- Probleme per E-Mail an marius@wilsch-ai.com zu melden
- Einen primären Ansprechpartner zu benennen
- Koordination mit dem eigenen IT-Dienstleister für Infrastrukturfragen sicherzustellen

---

## 4. Preisgestaltung

### 4.1 Stundensatz

Alle Leistungen werden nach Arbeitsaufwand abgerechnet:

- **Stundensatz:** 90 EUR/Stunde (exkl. MwSt.)
- **Abrechnungseinheit:** 30-Minuten-Takte

Es gibt keine Bereitschaftspauschale und keine Infrastrukturgebühr.

### 4.2 Rechnungsstellung

- Rechnungen werden monatlich zum 1. des Folgemonats gestellt
- Jede Rechnung enthält: Datum, Dauer, Tätigkeitsbeschreibung pro Einsatz
- **Zahlungsziel:** 14 Tage nach Rechnungsdatum (NET 14)

### 4.3 Mehrwertsteuer

Für B2B-Leistungen innerhalb der EU (Reverse-Charge):
- 0% MwSt. durch den Dienstleister
- Rechnung enthält: „Reverse Charge — Steuerschuldnerschaft des Leistungsempfängers gemäß Art. 196 EU-MwSt.-Richtlinie"

### 4.4 Verzugszinsen

Gemäß EU-Zahlungsverzugsrichtlinie (2011/7/EU) behält sich der Dienstleister das Recht vor, gesetzliche Verzugszinsen (8% + EZB-Referenzzins) sowie eine Mindestpauschale von 40 EUR geltend zu machen.

---

## 5. Service-Levels

### 5.1 Servicezeiten

- Geschäftszeiten: 08:00–18:00 Uhr MEZ, Montag bis Freitag, ausgenommen deutsche Feiertage
- Außerhalb der Geschäftszeiten besteht keine Reaktionsverpflichtung

### 5.2 Reaktionszeiten

- Geschäftskritische Probleme (Systemausfall, Datenverlust): Reaktion innerhalb von 1 Werktag
- Nicht-kritische Probleme: Nach bestem Bemühen (typischerweise nächster Werktag)

### 5.3 Lösungszeit

Nach bestem Bemühen. Es wird keine garantierte Frist gegeben.

---

## 6. Haftungsbeschränkung

### 6.1 Haftungsobergrenze

Die Gesamthaftung des Dienstleisters ist auf den Gesamtbetrag beschränkt, den der Kunde in den letzten 12 Monaten im Rahmen dieser Vereinbarung gezahlt hat.

### 6.2 Folgeschäden

Keine Partei haftet für Folgeschäden, indirekte, zufällige, besondere oder Strafschäden.

### 6.3 Höhere Gewalt

Keine Partei haftet für die Nichterfüllung von Pflichten aufgrund von Umständen außerhalb ihrer angemessenen Kontrolle.

---

## 7. Vertraulichkeit

### 7.1 Vertrauliche Informationen

Beide Parteien verpflichten sich, vertrauliche Informationen der anderen Partei nur für die Zwecke dieser Vereinbarung zu verwenden und nicht an Dritte weiterzugeben.

### 7.2 Dauer

Die Vertraulichkeitspflichten bleiben für 3 Jahre nach Beendigung dieser Vereinbarung bestehen.

---

## 8. Geistiges Eigentum

### 8.1 Eigentum des Kunden

Alle speziell für den Kunden erstellten Arbeitsergebnisse gehen nach vollständiger Zahlung in das Eigentum des Kunden über.

### 8.2 Vorbestehendes IP des Dienstleisters

Der Dienstleister behält alle Rechte an vorbestehendem geistigen Eigentum, Werkzeugen und Methodologien. Der Kunde erhält eine unbefristete, nicht-exklusive Lizenz zur Nutzung integrierter Komponenten.

---

## 9. Allgemeine Bestimmungen

Diese Vereinbarung unterliegt EU-Recht. Änderungen bedürfen der Schriftform. Sollte eine Bestimmung unwirksam sein, bleiben die übrigen Bestimmungen wirksam.

---

## 10. Kündigung

Jede Partei kann mit 30 Tagen schriftlicher Kündigungsfrist kündigen. Bei Kündigung zahlt der Kunde alle erbrachten Leistungen und der Dienstleister übergibt alle Arbeitsergebnisse.

---

## 11. Unterschriften

**Für ROHDEX GmbH & Co. KG:**

Unterschrift: _________________________

Name: _________________________

Funktion: _________________________

Datum: _________________________

**Für Wilsch AI Services OÜ:**

Unterschrift: _________________________

Name: Marius Wilsch

Funktion: Board Member

Datum: _________________________

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

