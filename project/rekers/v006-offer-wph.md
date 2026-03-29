---
publish: true
---

# Workshop 2 Offer V006 - WPH/Ulrich
[[client-rekers]]

**Source:** [Google Drive](https://docs.google.com/document/d/1dd1usOQ-YYu8MTOdWXSfRW-hR1IVxGBi/edit)
**Version:** V006 (SG180 – AI on IBM Power, VP: K2500142)
**Date:** November 21, 2025

---

## Angebot für REKERS Betonwerk GmbH & Co. KG

**Auftraggeber:** REKERS Betonwerk GmbH & Co. KG, Portlandstr. 15, 48480 Spelle
**WPH:** Karl-Valentin-Str. 17, D-82031 Grünwald
**Ansprechpartner:** Ulrich Wilsch, 0170/7990960, wilsch@wilsch.de

---

## Ausgangssituation

REKERS verfügt über 30 Jahre Ingenieursexpertise in mehreren tausend erfolgreich realisierten Projekten. Diese Projekthistorie mit den enthaltenen Daten stellt einen wichtigen Wettbewerbsvorteil dar. Die notwendige umfangreiche Kenntnis dieser Projekte ist jedoch in der aktuellen Form erschwert nutzbar.

Die zentrale Herausforderung besteht darin, dieses essenzielle Projektwissen durch die technischen Möglichkeiten von ETL-Prozessen (Extract, Transform and Load) und durch die Leistungsfähigkeit von KI-Systemen einem erweiterten Benutzerkreis mit einer geringen Einarbeitungszeit zur Verfügung zu stellen.

**Nachteile des aktuellen Verfahrens:**
- Lange Einarbeitungszeit durch hohes Maß an notwendiger spezifischer Kenntnis der Projekthistorie
- Expertenwissen konzentriert sich auf wenige Schlüsselpersonen
- Begrenzte Suchfunktionen auf die Historie
- Suche über unterschiedliche Datenpools erschwert manuelle Suche
- Relevante, hilfreiche Daten werden ggf. nicht gefunden
- Paralleles Arbeiten an Angeboten erschwert, nur mit zentral gesteuerter Abstimmung möglich

---

## Zielsetzung: KI-gestütztes Gedächtnis der Projekthistorie

Das Ziel ist ein KI-System mit Zugriff auf die gesamte REKERS Projekthistorie der letzten 30 Jahre.

### Systemverfügbarkeit und Wissenserhalt
- Kontinuierliche Verfügbarkeit unabhängig von Arbeitszeiten
- Vollständiger Zugriff auf alle historischen Projekte
- Zentrales „Projektgedächtnis" unabhängig von Personalwechseln
- Semantische Suchfunktion für einfache und effiziente Abfragen ohne Spezialwissen
- Breiter Wissenszugang und schneller Wissenstransfer für alle relevanten Mitarbeitergruppen

### Erwartete Prozessverbesserungen

| Bereich | Erwartung |
|---------|-----------|
| Wissenserhalt | Projektgedächtnis unabhängig von Mitarbeitererfahrung und Fluktuation |
| Einarbeitung | Reduktion der Einarbeitungszeiten von Monaten auf Tage |
| Wissenszugang | KI-gestützte Analyse unterstützt schnelleren Know-how Aufbau |
| Kalkulationszeit | Ca. 50-70% Reduktion bei Routineprojekten |
| Suchzeit | Ca. 70% Reduktion bei der Suche der relevanten Projektreferenzen |

### Prozessvergleich

| Parameter | Aktueller Prozess | Ziel-Prozess |
|-----------|-------------------|--------------|
| Suchzeit pro Kundenanfrage | Tage auf mehrere Personen geteilt | Stunden von einer Person |
| Abdeckung | Persönliches Projektgedächtnis | KI-„Gedächtnis" über die gesamte 30-Jahres-Historie |
| Verfügbarkeit | Abhängig von Personenverfügbarkeit | 24/7 systembasiert |

---

## Ziele der Voranalyse

Basierend auf Workshop 1 vom 09.10.2025 umfasst dieses Angebot die technische Machbarkeitsprüfung für ein KI-gestütztes Angebotssystem bei REKERS.

### Workshop-Schwerpunkte
- Bewertung der Datenbankkomplexität (IBM i – Struktur und historische Projektdaten)
- Definition von Metadaten (Extrakten) als Datenpool für das KI-System
- Klärung der Referenzierung zwischen strukturierten und unstrukturierten Daten
- Identifizierung der Datenzugriffsmethode zwischen IBM i – Datenbank/LPAR und dem KI-System
- Validierung des Prozessablaufs mit dem Angebotsworkflow für die Ermittlung der Automatisierungspunkte
- Analyse und Bewertung welche Informationen in welchem Dokumententyp oder Tabellen zu finden sind
- Bewertung der Hardware-Infrastruktur (Power 10 Inference-Workload-Kapazität)

### Ergebnis
- Machbarkeits- und Aufwandsbewertung mit Go/No-Go-Empfehlung
- Detaillierte technische Beschreibung zum Vorgehen und Umsetzungsplan für den Proof of Concept

---

## Vorbereitungen für den Workshop

### REKERS stellt bereit

**Workshop-Teilnehmer (erforderlich):**
- IT-Ansprechpartner (vertraut mit der Tabellenstruktur der IBM i Datenbank)
- Abteilungsleiter Kalkulation (zur Definition der Geschäftsprozessanforderungen)
- Kalkulationsingenieur (zur Demonstration des aktuellen manuellen Suchprozesses)

**Technischer Zugang:**
- Lesezugriff auf IBM i Datenbank (Materiallisten, Kalkulationsdaten, Arbeitsstunden, BLOB-Dateien)
- Zugriff auf Linux-Dateisysteme (CAD-Zeichnungen, PDFs, Statistikberechnungen, Fertigteilkonventionen)
- *Anmerkung WPH:* Dateien mit einer Größe über 100 MB sollen in Linux-Servern liegen und nicht als BLOB gespeichert sein
- Stichprobe von 10 historischen Projekten mit vollständigen Datensätzen (Kalkulation und Ausführung)

**Dokumentation (falls vorhanden):**
- Prozessdokumentation des Kalkulationsworkflows
- ERP-System Dokumentation (Schnittstellen für "gelbe Felder")
- Systemarchitektur-Diagramme (IBM i + Linux Infrastruktur)

**Rechtliches:**
- NDA-Unterzeichnung vor Datenzugriff

---

## Geplante Workshop-Struktur

**Ort:** Spelle (vor Ort)
**Dauer:** 2 Tage vor Ort, 1.5 Tage Einzelarbeit

### Agenda vor Ort (2 Tage)

#### Technische Validierung
- **IBM i Datenbankstruktur:** Tabellenübersicht für Materiallisten, Kalkulationsdaten, Arbeitsstunden und historische Projektdaten. Verständnis der Relationen der Tabellen. Ggf. Ableitung/Normalisierung in ein einfaches Star-Schema
- **Meta-Datei(en):** Klären ob und wie die IBM I DB2-Tabellen in Metadaten normalisiert werden müssen. Klärung relevanter Spalten der Metadaten und der Source für diese Spalten
- **Linux Dateisystem Struktur:** Verständnis der Organisation der unstrukturierten Daten (CAD-Zeichnungen, PDFs, Statistikberechnungen)
- **Referenzierung zur Metadatei:** Klärung der Struktur für die Referenzierung zwischen strukturierten Daten in den Metadaten und den unstrukturierten Daten
- **Datenqualitätsprüfung:** Stichprobenanalyse historischer Projektdaten (10 Projekte) auf Vollständigkeit und Nutzbarkeit

#### Prozessvalidierung
- **Kalkulationsworkflow:** Beobachtung des manuellen Suchprozesses für historische Bauteile und Anpassung von Preisdaten
- **Automatisierungspunkte:** Identifizierung der "gelben Felder" (Materialliste, Arbeitsstunden, Kosten) für KI-Integration ins ERP-System
- **Benutzer-Schnittstellen:** Abgrenzung zwischen automatischer Befüllung und manueller Ingenieurprüfung

#### Infrastruktur Validierung
- **Power 10 Inference-Kapazität:** Bewertung der Hardware-Leistung für beide KI-Anwendungsfälle (Kostenkalkulation + semantische Suche von 20-30 Jahren Projekthistorie)
- **Alternativ Hybrid-Ansatz:** Klärung Training (externe GPU) vs. Inference (lokale Power 10) und Datenexport-Anforderungen

### Einzelarbeit (Berater) (1.5 Tage)
- **Machbarkeitsanalyse:** Bewertung Datenbankkomplexität und Infrastruktureignung
- **Dokumentation:** Erstellung Machbarkeitsbericht mit Go/No-Go-Empfehlung

---

## Team

| Name | Rolle | Verantwortungsbereich |
|------|-------|----------------------|
| Marius Wilsch | Prozessanalyse | Lösungsanforderungen und Workflow-Definition |
| Thomas Erhard | Technische Analyse | Datenbankstruktur und Infrastrukturbewertung |
| Ulrich Wilsch | Vertriebsleitung | Process-Owner |

---

## Investitionsübersicht

| Komponente | Investition (EUR) |
|------------|------------------|
| Workshop vor Ort (2 Tage, 2 Experten) | x.x00 |
| Analyse und Dokumentation (1.5 Tage, 2 Experten) | x.x00 |
| Reisekosten | 1.000 |
| **Gesamt** | **x.500 EUR** |

*(Pricing TBD by Uli)*

---

## Vertragsbedingungen

- Dieses Angebot ist gültig bis zum **31. Dezember 2025**
- Es gelten die allgemeinen Vertragsbedingungen der WPH Wilsch Power Hosting GmbH
- Zahlungsziel: 14 Tage netto
- Alle genannten Preise zzgl. der gesetzlichen MwSt.
- Vereinbarte Stundensätze gelten 7 x 24
- Es werden keine Nacht-, Wochenend- und Feiertagszuschläge erhoben

---

**Kontakt:**
Telefon: 0170 7990960
E-Mail: wilsch@wilsch.de

Ulrich Wilsch

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

