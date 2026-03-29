---
publish: true
---

# IITR Bi-Weekly Sync — March 17, 2026
[[client-03_iitr]]

## Meetingziel

DS-Kit Navigation Fortschritt präsentieren, Validierungs-Input einholen und nächsten Meilenstein abstimmen.

1. **Aktueller Stand** — 2-3 Live-Antworten mit verbessertem Ausgabeformat demonstrieren, CSV zur asynchronen Prüfung übergeben
2. **Staging-Konsolidierung** — Ein Zugang, eine Domain, drei Systeme als Auswahl
3. **Nächste Schritte** — Retrieval-Optimierung und Datenaufnahme als Weg zur Systemübergabe beim nächsten Bi-Weekly
4. **Singapur** — Kurze Rückmeldung zur Datenschutz-Marktlage, [ausführliche Analyse](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/singapore-datenschutz-market-research) bei Interesse

---

## Gesprächsthemen

*Ausgangspunkte für die Diskussion, nicht abschließend.*

### 1. Aktueller Stand — DS-Kit Navigationssystem
⏱️ 7 min

Das Navigationssystem beantwortet aktuell 13 von 29 Testfragen korrekt (v7.1 Prompt, 16 Fails — davon sind alle Fails Retrieval-Probleme, nicht Format). Seit dem letzten Termin wurde das Ausgabeformat verbessert: Antworten enthalten jetzt eine direkte Antwort, die Rechtsgrundlage und einen Kapitelhinweis zur Navigation im DS-Kit.

- 2-3 Beispielantworten werden live vorgeführt
- Eine CSV-Datei mit allen 29 Fragen und Antworten wird im Anschluss zur Prüfung bereitgestellt
- Ihre Rückmeldung (welche Antworten korrekt/falsch sind) fließt direkt in die nächste Verbesserungsrunde ein

**Live-Demo Beispiele:**

Erfolgsbeispiel — Direkte Antwort mit Navigation:
> **Frage:** Wie häufig sollte ich meine Mitarbeiterinnen und Mitarbeiter schulen?
> **Antwort:** Die Datenschutz-Schulung der Mitarbeiter sollte mindestens einmal im Jahr erfolgen. Dies ergibt sich aus der Erwartung der deutschen Datenschutz-Aufsichtsbehörden.
> 📍 Navigation: DS-Kit → Kapitel FAQ #6

Fehlerbeispiel — Ehrliche Weiterleitung:
> **Frage:** Stellen Sie auch englischsprachige Zertifikate zur Verfügung?
> **Antwort:** Zu Ihrer Frage bezüglich englischsprachiger Zertifikate habe ich im DS-Kit keine passenden Informationen gefunden. Bitte wenden Sie sich für eine fachliche Beratung direkt an dskit@iitr.de.

**Framing:** Das Ausgabeformat funktioniert jetzt — kurze Antworten, Kapitelverweise, keine langen Listen mehr. Die Herausforderung liegt jetzt bei der Datenabdeckung: Wir haben heterogene Quellen (PDF, FAQ-CSV, Web-Inhalte aus core.iitr.de), und jede Quelle hat eine andere Struktur. Wir müssen verschiedene Retrieval-Ansätze testen, um sicherzustellen, dass das System die richtige Stelle auch tatsächlich findet. Die Infrastruktur und das Format stehen — als nächstes kommen die zusätzlichen Datenquellen und die Feinabstimmung der Suche. Die CSV mit allen 29 Fragen und Antworten stellen wir zur asynchronen Validierung bereit.

**Zu klären:** Validierung der Antworten durch Herrn Stellmacher — asynchron über die CSV bis zum nächsten Bi-Weekly.

### 2. Ein System, ein Zugang — Staging-Konsolidierung
⏱️ 2 min

Aktuell laufen die drei IITR-Systeme (Navigation, Masterfragen, Urteile) auf getrennten Instanzen mit separaten Zugängen. Das hat zuletzt dazu geführt, dass bei Arbeiten am Navigationssystem Ihr Account nicht mehr erreichbar war.

- Ziel: Ein Zugang, eine Domain, drei Systeme als Auswahl
- Sie melden sich einmal an und wählen über das Menü, welches System Sie nutzen möchten
- Die Konsolidierung wird bis zum nächsten Bi-Weekly umgesetzt

**Zu klären:** Bestätigung, dass ein einheitlicher Zugang mit Systemauswahl den Anforderungen entspricht.

### 3. Nächste Schritte — Retrieval-Optimierung und Konsolidierung
⏱️ 3 min

Das Ausgabeformat steht — Antworten sind jetzt kurz, präzise und mit Kapitelnavigation versehen. Die verbleibenden 16 Fehler entstehen nicht durch falsche Antworten, sondern weil das System die richtige Stelle in den Dokumenten noch nicht zuverlässig findet. Der Grund: Die Datenquellen sind sehr unterschiedlich aufgebaut — PDF-Leitfaden, FAQ-Tabellen und Web-Inhalte aus core.iitr.de haben jeweils eine andere Struktur. Das Retrieval muss für diese Vielfalt optimiert werden.

- **Bewährte Optimierungstechniken** stehen als nächster Hebel bereit — verschiedene Suchmethoden werden getestet, damit das System über alle Quelltypen (PDF, FAQ, Web) hinweg die richtige Stelle findet
- **Datenaufnahme:** Zusätzliche Quellen (Templates, weitere Webinhalte) werden eingebunden, um die Abdeckung zu erhöhen
- **Staging-Konsolidierung:** Die drei Systeme werden in eine einzige Oberfläche zusammengeführt — ein Zugang, eine Domain, Systemauswahl per Menü
- **Ziel nächstes Bi-Weekly:** Sie testen das System selbst — eigener Zugang, eigene Fragen, ohne Entwicklerbeteiligung

**Zu klären:** Bestätigung des Fahrplans — Retrieval-Optimierung und Konsolidierung bis zum nächsten Termin, dann Systemübergabe.

### 4. Datenschutz-Marktanalyse Singapur — Status
⏱️ 1 min

Wie beim letzten Termin besprochen: Wir haben uns in Singapur umgehört und eine halbe Seite zusammengestellt — wer ist da, was fehlt, wo könnte IITR reinpassen.

- Kurzfassung: Kein Art. 27 Anbieter bedient Singapur gezielt, obwohl 1.300+ deutsche Firmen dort sind
- Bei Interesse liegt eine ausführlichere Analyse als Dokument bereit
- Nächster Schritt nur bei Bedarf — kein Aufwand ohne Ihr Go

**Zu klären:** Ist das Thema weiterhin interessant? Falls ja, stellen wir die ausführliche Analyse zur Verfügung.

## Meetingformat

- **Typ:** Fortschrittsbericht + Live-Demo
- **Dauer:** ~30 min
- **Erwartung:** Keine Vorbereitung erforderlich — Ergebnisse werden im Termin präsentiert
- **Ergebnis:** CSV zur asynchronen Validierung, Fahrplan für nächsten Meilenstein bestätigt

## Verweise

- **Issue:** [#1093 — IITR: JA Design Doc Extraction Passes](https://github.com/DaveX2001/deliverable-tracking/issues/1093)
- **Design Doc:** [DS-Kit Navigation — Production Readiness Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-design)
- **Zielkorridor:** [24-32h Kostenrahmen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/dskit-navigation-rag-estimate)
- **Singapore Research:** [Datenschutz-Marktanalyse Singapur](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/iitr/singapore-datenschutz-market-research)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

