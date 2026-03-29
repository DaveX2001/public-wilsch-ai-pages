---
publish: true
---

# IITR Zielkorridor — Meeting-Agenda

[[client-iitr]]

## Meetingziel

Die direkte Zusammenarbeit zwischen Marius Wilsch und IITR Datenschutz GmbH definieren — Taktung, Umfang und gegenseitige Erwartungen.

1. **Zweiwöchentliche Taktung vereinbaren** — Festpreis-Meilensteinlieferung mit regelmäßigen zweiwöchentlichen Berührungspunkten als Zusammenarbeitsmodell vorschlagen
2. **DS-Kit Lieferstatus** — aktuelle Testergebnisse und Evaluierungsbericht vorstellen, Lieferzeitplan bestätigen
3. **Portfolio-Abgleich** — alle aktiven Systeme (RAG-V1, RAG-V2, DS-Kit, Avatar) durchgehen und Prioritäten für nächste Meilensteine abstimmen
4. **Serveradministration** — formelle Übernahme der Infrastrukturverantwortung

## Pre-Read

- [DS-Kit Evaluierungsbericht](https://docs.google.com/spreadsheets/d/16xGQ_SQwVmCGgEUhMpnIYDMbX8Lp8UVeq224j2gzHAY/edit?gid=1671636194#gid=1671636194) — aktueller Genauigkeits- und Lückenstatus
- [DS-Kit Festpreisangebot](dskit-navigation-rag-estimate.md) — ursprünglicher Umfang und Preisgestaltung
- [Kraska-E-Mail (17. Feb)](https://mail.google.com/mail/u/0/#all/19c6ae56b934ed26) — ursprüngliche Anfrage zu Zielkorridoren

---

## Diskussionsthemen

*Ausgangspunkte für die Diskussion, nicht darauf beschränkt.*

### 1. Verzögerte Antworten und Verantwortungsübernahme
⏱️ 5 Min.

Mehrere E-Mails sind unbeantwortet, darunter die SpaceNet VPN-Migrations-Rückfrage (20. Feb). Die Verzögerung hängt mit einer laufenden rechtlichen Auseinandersetzung mit dem bisherigen Vermittler zusammen — Transparenz darüber, ohne um Unterstützung zu bitten.

- SpaceNet wartet auf Rückmeldung zum VPN/SSL-Migrationsstatus ([E-Mail](https://mail.google.com/mail/u/0/#all/19c79f0c8b9388a8))
- Hintergrund: Rechtliche Situation mit Rolnik — Vertragsverhältnis beendet, Kommunikation nicht mehr möglich. Marius hat das Projekt eigenständig weitergeführt
- Ab sofort: Marius ist der einzige Ansprechpartner für alle technischen Themen — keine Vermittlerverzögerungen mehr
- Dieses Meeting etabliert die Arbeitsbeziehung, die diese Zusage umsetzbar macht
- Falls IITR bereit wäre, ihre Erfahrungen mit der bisherigen Kommunikationsstruktur schriftlich zu bestätigen — willkommen, aber keine Voraussetzung

**Zu klären:** Verzögerung anerkennen, kurzer Kontext zur rechtlichen Situation, direkte Verantwortungsübernahme zusagen.

### 2. Vorgeschlagenes Arbeitsmodell: Zweiwöchentliche Festpreis-Taktung
⏱️ 10 Min.

Ein strukturiertes Zusammenarbeitsmodell ersetzt das bisherige Ad-hoc-Kommunikationsmuster. Jeder Zweiwochenzyklus hat einen klaren Rhythmus: Fortschritt des letzten Zyklus zeigen, Umfang für den nächsten definieren.

- **Taktung:** Zweiwöchentliche Videotermine — fester Zeitplan, nicht „wenn etwas fertig ist"
- **Preisgestaltung:** Festpreis pro Liefergegenstand, in jedem Meeting abgestimmt und vereinbart. Abrechnung bei Lieferung
- **Zyklusstruktur:** Erledigtes besprechen → nächsten Meilenstein vereinbaren → vor dem nächsten Meeting liefern
- **Ad-hoc:** Bei Klärungsbedarf zwischen den Zyklen kurze Ad-hoc-Gespräche möglich — primärer Kontakt über Stellmacher
- **Beispielumfang pro Zyklus:** Langfuse-Monitoring-Verbesserungen, DS-Kit-Iterationen nach Feedback, Avatar-Fortschritt
- **Erster Zyklus:** Start nächste Woche Dienstag. DS-Kit-Lieferung als erster Meilenstein. CSV-Vorabversion heute als Feedbackgrundlage

💡 **Verbal erwähnen (nicht im Vorschlag):** Sobald die Zusammenarbeit eingespielt ist, besteht perspektivisch die Möglichkeit eines Retainer-Modells, das alle Projekte und Wartung bündelt — als Teaser, nicht als Vorschlag.

**Zu klären:** Einigung auf die zweiwöchentliche Taktung als Zusammenarbeitsrahmen und Startdatum des ersten Zyklus.

### 3. DS-Kit Navigation: Aktueller Stand und Evaluierungsbericht
⏱️ 10 Min.

Fortschrittsbericht über das DS-Kit-Navigationssystem. Ein strukturierter Evaluierungsbericht (CSV) zeigt den aktuellen Stand: 29 Testfragen mit vollständigen RAG-Antworten und Quellenattribution. Dieser Bericht ist ein Beispiel für das Lieferformat im zweiwöchentlichen Rhythmus (→ Thema 2).

- **Live-System:** [dskit.iitr-cloud.de](https://dskit.iitr-cloud.de/)
- **Evaluierungsbericht:** Testfrage, erwartete Antwort, vollständige RAG-Antwort, erwartete vs. tatsächliche Quellen (Top-3)
- Bericht dient als Feedbackgrundlage — IITR kann Antwortqualität bewerten und Prioritäten für nächsten Zyklus setzen
- Bekannte Einschränkungen sind identifiziert und werden im nächsten Zyklus adressiert

**Demo-Fragen (3 Beispiele live zeigen):**

1. *Kann ich Sie als Datenschutzbeauftragter auf meiner Webseite angeben?* — zeigt korrekte Kontaktdaten + Kapitelreferenz
2. *Wir haben eine Datenschutzverletzung, bei der ein Kunde versehentlich eine Buchungsbestätigung mit Daten aus einem anderen Vorgang erhalten hat. Was müssen wir tun?* — zeigt Notfallprozess + Dokumentenverweis (Kapitel 10)
3. *Im Rahmen unseres anstehenden TISAX Audits müssen wir genau die Inhalte unserer Schulungen aus dem Datenschutz Kit angeben. Haben Sie eine solche Übersicht für mich?* — zeigt tiefe Navigation (Dokument 11-03, 11-04)

💡 **Nach dem Meeting:** CSV-Evaluierungsbericht an Stellmacher schicken für Feedback

**Zu klären:** Feedback zum Evaluierungsbericht, Priorisierung der offenen Punkte für nächsten Zweiwochenzyklus.

### 4. IITR-Systemportfolio: Status und Prioritäten
⏱️ 10 Min.

Vier Systeme existieren im IITR-Portfolio. Jedes befindet sich in einem anderen Stadium — die Prioritäten von Kraska bestimmen, wohin der Aufwand als nächstes fließt.

- **RAG-V1** (DSGVO FAQ): Produktion, 85% Automatisierung, 97% Genauigkeit. Wartungsmodus. Langfuse-Monitoring vorhanden — Verbesserungen als laufender Umfang verfügbar
- **RAG-V2** (Rechtsprechungssuche): Gebaut, 2.567 Gerichtsurteile indiziert. Zitierextraktion und semantische Suche funktionsfähig
- **DS-Kit Navigation**: Kurz vor Fertigstellung (siehe Thema 3)
- **Avatar**: Konvergenzpunkt — einheitliche Oberfläche über alle drei RAG-Systeme. Aktueller Stand und IITRs Vision dafür müssen abgestimmt werden

**Zu klären:** Kraskas Prioritäten über das Portfolio verstehen und wo die Avatar-Konvergenz aus ihrer Sicht steht.

### 5. Serveradministration und Wartungsvereinbarung
⏱️ 10 Min.

Die Serverinfrastruktur wird bereits von Marius betrieben — die formelle Eigentumsübernahme von Rolnik ist der logische nächste Schritt. Die SpaceNet VPN-Migration (20. Feb) ist ein sofortiger Handlungspunkt.

- Serveradministration derzeit unter Rolniks Namen, von Marius betrieben
- SpaceNet VPN/SSL-Migration wartet auf Antwort — Marius übernimmt die Verantwortung sofort
- Wartungs-SLA: separate Vereinbarung für Systemverfügbarkeit, Updates und Monitoring aller Produktivsysteme (RAG-V1, RAG-V2, DS-Kit nach Lieferung)
- SLA ist unabhängig von Projektarbeit — deckt laufende Systemgesundheit ab

**Zu klären:** Formelle Übertragung der Serveradministration und Abstimmung über den Umfang eines Wartungs-SLAs.

## Meeting-Format

- **Art:** Videocall (Zoom)
- **Datum:** Dienstag, 24. Februar 2026, 14:30 Uhr
- **Dauer:** 30–45 Minuten
- **Teilnehmer:** Eckehard Kraska, Sebastian Kraska, Marvin Stellmacher, Marius Wilsch
- **Zoom:** Meeting-ID 852 0098 1719, Kenncode 242060
- **Erwartung:** Marius stellt DS-Kit-Demo und Zielkorridor-Vorschlag vor; Kraskas geben Prioritäten und Feedback zum Arbeitsmodell
- **Ergebnis:** Einigung auf zweiwöchentliche Taktung, Klarheit über Portfolio-Prioritäten, Serveradministrations-Übergabe eingeleitet

## Verknüpfungen

- **Issue:** [#766 — Client Communication Transition](https://github.com/DaveX2001/deliverable-tracking/issues/766)
- **Kraska-E-Mail:** [Terminanfrage (17. Feb)](https://mail.google.com/mail/u/0/#all/19c6ae56b934ed26)
- **SpaceNet-E-Mail:** [VPN-Migration (20. Feb)](https://mail.google.com/mail/u/0/#all/19c79f0c8b9388a8)
- **Vertragsstrategie:** [#847 Design Doc](https://github.com/DaveX2001/deliverable-tracking/issues/847)
- **DS-Kit-Angebot:** [Festpreisangebot](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-rag-estimate)

---

*Quelle: Extraktionspass vom 24. Februar 2026. Session: /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-00-WILSCH-AI-INTERNAL--soloforce/a79da5f4-1884-450e-8e7d-bc430a975b6f.jsonl*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

