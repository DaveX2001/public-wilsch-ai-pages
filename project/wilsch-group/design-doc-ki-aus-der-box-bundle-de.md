---
publish: true
---

# KI aus der Box — DGX Spark Bundle (Deutsch)
[[client-wilsch-group]]

Design Doc für den DGX Spark Bundle-Abschnitt auf der FSO-Seminar-Webseite — Hardware, Software-Expertise und Einrichtungsdienstleistungen als verpacktes kommerzielles Angebot.

---

## Problemstellung

Die FSO-Seminar-Webseite (KI-Performance-Seite) endet mit einem generischen CTA — „Vergleichstest anfragen" — der kein konkretes Produkt, keinen Preis und keinen Handlungspfad bietet. Seminarteilnehmer sehen Benchmark-Ergebnisse (DGX Spark 200x schneller als Power 10), haben aber keinen umsetzbaren nächsten Schritt außer „kontaktieren Sie uns."

Dieses Design Doc definiert das DGX Spark Bundle — „KI aus der Box" — als verpacktes Produkt mit Hardware, Software-Expertise und Einrichtungsdienstleistungen zu einem konkreten Preis. Der Bundle-Abschnitt ersetzt oder ergänzt den aktuellen „Nächster Schritt"-Footer auf der KI-Performance-Seite.

Es geht NICHT um den Inhalt der KI-Performance-Seite (Benchmarks, Hardwarevergleich, Analogien) — der existiert bereits. Es geht um das kommerzielle Angebot am Ende: Was der Kunde kauft, was es kostet und was er bekommt.

**Voraussetzungen:**
- FSO-Seminar ist am 27.03.2026 — der Bundle-Abschnitt muss vor der Veranstaltung live sein
- DGX Spark Hardware ist verfügbar für ~€5.000 (Richtpreis, volatil — IBM hat die Spyre-Komponentenpreise im März um 55% angehoben)
- Zwei DGX Sparks sind mit vLLM betriebsbereit, Benchmarks abgeschlossen (Rekers-Pipeline: 1.319 Seiten/Std.)
- KI-Performance-Seite existiert unter /ki-performance mit Sektionen: Hero → ComparisonTable → Benchmark → WarumSoSchnell → Kaufentscheidung → PageFooter
- Bestehende CTA-Infrastruktur: „Jetzt anrufen" + „Gespräch vereinbaren" auf der Startseite, „Vergleichstest anfragen" auf der KI-Performance-Seite

---

## Erfolgsdefinition

| Element | Definition |
|---------|-----------|
| **Ziel** | Ein konkretes, bepreistes Produktangebot auf der FSO-Webseite, das Seminarteilnehmern einen klaren nächsten Schritt gibt — von „interessante Benchmarks" zu „das will ich" |
| **Erfolg** | Teilnehmer sehen das Bundle, verstehen was sie bekommen (Hardware + Software + Einrichtung), sehen den Preis (~€10K) und greifen zum Hörer oder buchen ein Gespräch. Ulrich kann im Seminar darauf zeigen und sagen: „KI aus der Box — 5.000 Euro Hardware, 5 Tage Einrichtung, läuft." |
| **Fertig-Test** | Kann Ulrich einem Geschäftsführer den Bundle-Abschnitt in 30 Sekunden erklären und ein „erzählen Sie mehr" bekommen? Wenn JA → der Abschnitt funktioniert. |

---

## Ansatz

### Teil 1 — Bundle-Struktur

Das Bundle folgt Ulrichs 40-jährigem IBM-Vertriebsmuster: Hardware + Betriebssoftware + Dienstleistung. Drei Schichten, ein Preis.

| Schicht | Was der Kunde bekommt | Preis |
|---------|----------------------|-------|
| **1 — Hardware** | NVIDIA DGX Spark (GB10 Blackwell, 128 GB RAM, 2 TB NVMe). Führt Modelle bis zu 200B Parameter lokal aus. Passt auf den Schreibtisch (150×150×50mm, 1,2 kg). | ~€5.000 (Richtpreis) |
| **2 — Software-Expertise** | Produktions-Serving-Engine (vLLM), Chat-Interface (Open WebUI / LibreChat), Datenkonnektoren (MCP, Vektor-DB, DB-Toolkits), Authentifizierung — konfiguriert für die Kundenumgebung. | Im Setup enthalten |
| **3 — Einrichtung** | 5 Manntage: Infrastruktur-Setup, Plattform + Konnektoren, Validierung mit echten Daten, Anwenderschulung + Übergabe. | ~€5.000 |

**Bundle-Gesamtpreis: ~€10.000** — gegenüber €250.000+ für IBM Spyre + Power 11.

**Positionierung:** Nicht „eine GPU-Box" — sondern Ihre lokale Plattform, um Daten zugänglich zu machen. Der DGX Spark ist die souveräne Engine; MCP + der Software-Stack verbindet ihn mit Ihrem SQL, Confluence, PIM und Ihren Fileservern. Cloud-Option verfügbar — erwähnt, aber nicht im Vordergrund.

**Produktname:** „KI aus der Box" — dreifache Bedeutung: physisch eine Box, funktioniert sofort (out of the box), und der Überraschungsfaktor („DAS ist die KI-Maschine?").

### Teil 2 — Software-Expertise (Schicht 2)

Der Verkaufspunkt ist nicht das spezifische Tool — sondern dass wir es bereits in 4+ Kundenprojekten herausgefunden haben. „Sie profitieren davon, dass Sie sich nicht selbständig mit diesen Neuerungen herumplagen."

**Serving Engine:** vLLM über eugr/spark-vllm-docker. Produktionsreif, modellagnostisch — jedes Modell mit einem Befehl austauschbar. Nächtliche Builds, vor Veröffentlichung getestet. Zwei Vorteile gegenüber dem vorinstallierten Ollama: (1) 1,4x schneller pro Anfrage bei einem 7,5x größeren Modell, und (2) Continuous Batching — 8 Benutzer können gleichzeitig anfragen, während Ollama sie einzeln abarbeitet. Gleiche Hardware, der Unterschied ist Software-Expertise. Adressiert die Veralterungsangst: neues Modell kommt morgen raus, ein Befehl zum Aktualisieren.

**Clustering (Zusatzoption):** „Was ist, wenn das Modell nicht auf eine Box passt?" — zwei DGX Sparks über ConnectX-7 verbunden → 256 GB einheitlicher Speicher, führt Modelle bis zu 397B Parameter aus (Qwen3.5-397B bestätigt). +€5K für die zweite Einheit. Wir wissen, wie man es einrichtet — launch-cluster.sh übernimmt die Orchestrierung.

**Chat-Interface:** Beide führenden Open-Source ChatGPT-ähnlichen Chat-Interfaces sind enthalten:

| Funktionalität | Enthalten |
|---------------|----------|
| ChatGPT-ähnliche Oberfläche | ✓ |
| SSO / OIDC | ✓ |
| Rollenbasierter Zugriff (Admin/User/Gruppen) | ✓ |
| Modellberechtigungen pro Gruppe | ✓ |
| Sprache-zu-Text / Text-zu-Sprache | ✓ |
| Datei-Upload + RAG | ✓ |
| MCP Tool Calling | ✓ |
| Code-Interpreter | ✓ |
| Agenten-Marktplatz | ✓ |
| MIT-lizenzierbar (umbrandbar) | ✓ |

Wir kennen beide Plattformen und wählen die richtige für die Kundenumgebung. Bereits in 4+ Kundenprojekten bewährt.

**Datenkonnektoren — die Dolmetscher-Schicht:** Vier Konnektortypen, jeder für ein anderes Datenproblem:

<img src="ki-aus-der-box-telefonzentrale.png" alt="Telefonzentrale — KI routet Anfragen an die richtige Datenquelle" width="100%">

| Typ | Wann einsetzen | Beispiel |
|-----|---------------|---------|
| MCP | Strukturierte Daten mit APIs | SQL, Confluence, SharePoint, E-Mail |
| Vektor-DB | Unstrukturierte semantische Suche | PDFs, Fileserver, Produktdokumentation |
| DB-Toolkits (MindsDB) | Legacy-Datenbanken, viele Konnektoren out-of-the-box | AS/400, Access über ODBC |
| Skills | Firmenwissen als strukturierte Anweisungen | Produktkatalog-Logik, Namenskonventionen, interne Prozesse — die KI kennt Ihr Unternehmen |

**Analogie — Dolmetscher:**

Ulrichs Einzeiler: *„Ihre Daten sprechen sechs Sprachen — wir liefern den Dolmetscher, der alle versteht."*

Erweitert: SQL spricht Deutsch, Confluence spricht Englisch, Ihre PDFs sprechen Bildsprache, die AS/400 spricht Plattdeutsch. Bisher mussten Sie selbst übersetzen — in jedem System einzeln suchen. Die KI ist der Dolmetscher. Wir liefern die Wörterbücher mit — und wissen, welches wo hingehört.

**Berechtigungskonzept:** Drei Authentifizierungsschichten, alle auf Muster abgebildet, die Unternehmen bereits kennen:
1. Chat-UI-Gruppen — Open WebUI RBAC (Admin/User/Pending, gruppenbasierter Modellzugriff)
2. MCP-DB-Berechtigungen — eingeschränkter Datenbankbenutzer, dieselbe Authentifizierung, die der Kunde bereits hat
3. Vektor-Metadaten-Filterung — Dokumenten-Sensitivitäts-Tags, C-Level-Inhalte nach Rolle gefiltert

**Lizenzierung:** LibreChat = MIT (vollständig umbrandbar). Open WebUI = BSD + Branding-Einschränkung (≤50 Benutzer ausgenommen, Enterprise-Lizenz für größere Installationen).

### Teil 3 — Einrichtung (Schicht 3)

5 Manntage, konfigurierbar. Tag 1 und 5 sind fest; Tage 2-4 sind die „Baukastenzone".

| Tag | Kategorie | Ergebnis |
|-----|----------|---------|
| 1 | **Infrastruktur** | Box online, Tailscale-Fernzugriff, vLLM-Serving, erstes Modell geladen |
| 2-3 | **Plattform + Konnektoren** | Chat-UI deployt, SSO/Auth konfiguriert, Basis-Datenquellen angebunden (2-3 Konnektoren) |
| 4 | **Validierung** | Echte Kundendaten, End-to-End Use Case intern getestet, dann dem Kunden vorgeführt |
| 5 | **Einweisung + Übergabe** | Admin-Schulung (Modellverwaltung, Benutzerverwaltung), Anwender-Schulung (wie man chattet), Übergabedokumentation |

**Basis vs. Zusatzoptionen:**
- **Basis-Bundle:** DGX Spark + vLLM + Chat-UI + 2 Manntage (Infrastruktur + Einweisung) = „Private ChatGPT, lokal"
- **Empfohlen:** Volle 5 Manntage mit Konnektoren + Auth + Validierung = ~€10K gesamt
- **Zusatzoptionen:** Weitere Konnektoren über die Basis hinaus (extra Manntage pro Quelle), zweiter DGX Spark für Clustering (+€5K), laufender Betreuungsvertrag (separat)

**Laufende Wartung:** „Permanente Aktualisierung verfügbar" — Modell-Updates, neue Konnektoren, Performance-Tuning. Wird pro Kunde im Workshop 1 (kostenlos) geschätzt. Nicht auf der Webseite bepreist — signalisiert Verfügbarkeit, adressiert die Veralterungsangst.

**Referenzfall:** Ein Kunde aus der Fertigungsindustrie — 294 Seiten Dokumentenverarbeitung: 5 Tage auf Power 10, 13 Minuten auf DGX Spark. Faktor 200.

### Teil 4 — Webseiten-Integration

**Position:** Ersetzt oder ergänzt den aktuellen „Nächster Schritt"-Abschnitt (PageFooter-Komponente) auf /ki-performance. Der Bundle-Abschnitt steht nach Kaufentscheidung — die natürliche Folgefrage nach „welche Hardware?" ist „was kostet der Einstieg?"

**Unwiderstehliches Angebot ($100M Offers Muster):**
- Preisanker mit Alternativen: IBM Spyre + Power 11 (€250K+), 300 ChatGPT-Accounts (€XX.000/Jahr), dann das Bundle (~€10K einmalig)
- Wertestapel: alle drei Schichten sichtbar (Hardware + Software + Einrichtung)
- Preis-Disclaimer: „Aktueller Richtpreis, kann sich ändern"
- Dringlichkeit: seminarspezifische Formulierung

**Verknappung:** „Nur 3 Pakete verfügbar" — oder ähnliche begrenzte Verfügbarkeit. Echte Einschränkung: Marius' Einrichtungskapazität ist endlich (5 Manntage pro Kunde).

**Einstiegspunkt:** Workshop 1 (kostenlos, 1-1,5 Stunden) ist der natürliche nächste Schritt nach dem Sehen des Bundles. Kunde ruft an → kostenloser Workshop → Bundle auf seine Umgebung zugeschnitten. Bereits der Standard-Vertriebsprozess — das Bundle gibt ihm einen konkreten Anker.

**CTA:** Verknüpft mit bestehender Infrastruktur — „Jetzt anrufen" + „Gespräch vereinbaren." Kein neuer Flow nötig.

**Texte/Überschriften:** An David delegiert — Richtung ist „KI aus der Box" als Headline, Wertestapel als Body, Preisanker als Überzeugungsschicht. Konkreter deutscher Text wird bei der Implementierung geschrieben.

**Visual:** Telefonzentrale-Illustration für den Datenkonnektoren-Abschnitt (generiert, gespeichert als ki-aus-der-box-telefonzentrale.png). Weitere Visuals nach Davids Ermessen.

**Technisch:** React-Komponente in KiPerformance.jsx, positioniert zwischen Kaufentscheidung und PageFooter (oder als Ersatz für PageFooter). Bestehendes Design-System: DM Sans Schriftart, #0A84FF blauer Akzent, Gradient-CTAs.

---

## Quellen

- **Transkript (FSO Bundle-Diskussion):** [25.03.2026](https://drive.google.com/file/d/1Ct_aOzc6px9j3TSBy7xrteoja-FPb6E0/view) — Zeilen 1780-2320, Bundle-Definition mit Ulrich
- **Transkript (Schäferbarthold WS01):** [24.03.2026 Fireflies](https://app.fireflies.ai/view/01KMGA10PQ07RZ3S7DGXPDQR26) — Poggemöller Anforderungen, MCP/Vektor-Erklärung, Cloud vs. On-Prem Debatte
- **E-Mail (Poggemöller Anforderungen):** [23.03.2026](https://mail.google.com/mail/u/0/#all/19d19a3dec2135c4) — 7-Punkte-Anforderungsliste
- **Hardware-Strategie:** [Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/hardware-strategie-lokale-ki-inferenz) — DGX Spark Spezifikationen, Preise, Phase 1/2
- **DGX Spark Infrastruktur:** [Design Doc](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-dgx-spark-infrastructure) — Serving Engine Leiter, vLLM, phasenweise Bereitstellung
- **vLLM Container:** [eugr/spark-vllm-docker](https://github.com/eugr/spark-vllm-docker) — README, Clustering, 397B Rezept
- **vLLM vs Ollama Benchmark:** hippocampus/project/rekers/vllm-vs-ollama-benchmark.md
- **FSO Analogien Meeting:** [Agenda 25.03.2026](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/meeting-agenda-fso-test-run-2026-03-25)
- **Webseiten-Repo:** /Users/verdant/Documents/projects/non-billable/WILSCH-AI-INTERNAL__wilsch-ai-site (KiPerformance.jsx:800-866)
- **Issue:** [#1280](https://github.com/DaveX2001/deliverable-tracking/issues/1280)
- **Generierte Visuals:** /tmp/analogy-{01-dolmetscher,02-reiseadapter,03-telefonzentrale}.png
- **Session:** b982f95a-c532-43a6-9f1b-1247d112a47b

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

