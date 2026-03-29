---
publish: true
---

# KI-Entwicklung mit Datensouveränität
[[client-wilsch-group]]

Aktuelle Bewertung der Möglichkeiten für KI-gestützte Softwareentwicklung — von Cloud-Lösungen mit vertraglichem Datenschutz bis hin zu vollständig lokaler Verarbeitung auf eigener Hardware.

*Stand: Februar 2026*

---

## Was Sie aus diesem Dokument mitnehmen

- **Drei Optionen** für KI-Entwicklung — je nach Datenanforderung Ihres Unternehmens
- **Für jede Option:** Hardware, Modell und rechtlicher Rahmen — konkret und nachvollziehbar
- **Einordnung:** Wo Cloud-KI sicher einsetzbar ist, wo Grenzen liegen und wann lokale Verarbeitung die bessere Wahl ist

---

## Ausgangslage

Künstliche Intelligenz beschleunigt Softwareentwicklung grundlegend — vom Lösungsdesign bis zur Implementierung. Gleichzeitig stehen Unternehmen vor einer zentralen Frage: *Was passiert mit unseren Daten, wenn KI-Systeme damit arbeiten?*

Für viele Organisationen ist diese Frage nicht theoretisch. Datensouveränität, regulatorische Anforderungen (DSGVO, BaFin, KRITIS) oder interne Sicherheitsrichtlinien können den Einsatz externer Cloud-Dienste grundsätzlich ausschließen. Wer hier keine klare Antwort hat, riskiert entweder den Auftrag — oder die Compliance.

**Ein wichtiger Punkt:** Anonymisierte Daten (echte Schemastruktur, synthetische Werte) sind für KI-gestützte Entwicklung genauso effektiv wie Echtdaten. Die KI versteht die Datenstruktur, ohne echte Datensätze zu sehen. Das eigentliche Problem entsteht erst, wenn ein Unternehmen nicht einmal anonymisierte Strukturen in die Cloud senden kann — dann verliert die KI jeglichen Kontext.

**Was heute möglich ist:**
- Open-Source-Modelle erreichen die Qualität führender kommerzieller KI-Systeme für Softwareentwicklungsaufgaben
- Einzelne Desktop-Geräte (512 GB Arbeitsspeicher) können diese Modelle in voller Qualität betreiben — für unter 10.000 €
- Damit existiert erstmals eine glaubwürdige lokale Alternative für datensensible Projekte

---

## Datenschutz — Worauf es ankommt

Nicht jede KI ist gleich. Es gibt einen grundlegenden Unterschied zwischen kommerziellen KI-Produkten (mit Datenverarbeitungsvereinbarung) und Verbraucherprodukten (ohne vertraglichen Schutz):

| | Geschäftsprodukt (API) | Verbraucherprodukt (Chatbot) |
|--|------------------------|------------------------------|
| **Datenverarbeitung** | Auftragsverarbeitung — Ihre Daten bleiben Ihre Daten | Anbieter ist selbst Datenverantwortlicher |
| **Training** | Vertraglich ausgeschlossen | Standardmäßig aktiv |
| **Löschfrist** | 30 Tage, automatisch | Solange Konto aktiv |
| **DPA** | Ja, automatisch im Vertrag | Nein |

### Wo auch das Geschäftsprodukt an Grenzen stößt

Auch mit einer gültigen DPA bleiben Punkte, die für regulierte Unternehmen problematisch sein können:

- **US CLOUD Act** — Standardvertragsklauseln schützen nicht vor US-Behördenzugriff auf den Anbieter als US-Muttergesellschaft
- **Keine garantierte EU-Datenverarbeitung** — die Standard-Schnittstelle routet global; EU-Verarbeitung erfordert Sonderkonfiguration
- **Sicherheitsüberprüfung** — wenn der Anbieter Inhalte als potenziell schädlich einstuft, können Daten bis zu zwei Jahre gespeichert werden

**Wenn einer oder mehrere dieser Punkte für Ihr Unternehmen relevant sind, kommt eine rein lokale Lösung in Betracht.** Im nächsten Abschnitt sehen Sie, welche drei Optionen dafür existieren.

---

## Drei Stufen der KI-Entwicklung

Unser Angebot gliedert sich in drei Stufen, je nach Datenanforderung Ihres Unternehmens:

| Stufe | Ihre Situation | Wie wir arbeiten | Lokale Hardware nötig? |
|-------|---------------|------------------|----------------------|
| **Stufe 1: Cloud (DPA)** | Sie unterzeichnen eine DPA und akzeptieren die Bedingungen des KI-Anbieters | Cloud-KI übernimmt Design und Implementierung | Nein |
| **Stufe 2: Hybrid** | Anonymisierte Schemata sind akzeptabel, echte Daten müssen lokal bleiben | Cloud-KI designt mit anonymisierten Daten, lokales Modell implementiert mit echten Daten | Ja |
| **Stufe 3: Voll-Lokal** | Keine Daten dürfen in die Cloud | Lokales Modell übernimmt Design und Implementierung | Ja |

**Wie das in der Praxis funktioniert:**

- **Phase 1 — Design:** Wir entwerfen die technische Lösung mit führender Cloud-KI. Bei Stufe 2 sieht die KI nur anonymisierte Datenstrukturen (echte Tabellennamen und Spalten, synthetische Werte). Bei Stufe 3 arbeitet die KI ohne Kundendaten.
- **Phase 2 — Implementierung:** Ein lokales KI-Modell auf unserer eigenen Hardware implementiert den Code gegen Ihre echten Daten. Keine Daten verlassen unsere Maschine.

*Wir designen mit führender Cloud-KI. Wir implementieren mit Ihren Daten auf unserer lokalen Infrastruktur.*

Die Datenverarbeitungsvereinbarung (DPA) bestimmt, welche Stufe Ihr Unternehmen benötigt. Eine akzeptierte DPA ermöglicht Stufe 1 — die einfachste und qualitativ beste Variante. Lokale Hardware ist die Alternative für Unternehmen, die Stufe 1 nicht akzeptieren können. Für lokale Verarbeitung schließen wir eine individuelle DPA ab.

### Modellauswahl

Wir setzen auf Open-Source-Modelle, die in voller Qualität auf einer einzelnen Maschine mit 512 GB Arbeitsspeicher laufen. Zwei Modelle erfüllen diese Anforderung aktuell — eines optimiert für Softwareentwicklung, eines als Allzweckmodell.

| Modell | Parameter | Aktive Parameter | Speicherbedarf (volle Qualität) | Passt auf 512 GB? | Lizenz |
|--------|-----------|-----------------|-------------------------------|-------------------|--------|
| **[Qwen3.5-397B](https://huggingface.co/Qwen/Qwen3.5-397B-A17B)** | 397 Mrd. | 17 Mrd. | ~397 GB | ✅ Ja | Apache 2.0 — kommerziell uneingeschränkt |
| **[MiniMax M2.5](https://huggingface.co/MiniMaxAI/MiniMax-M2.5)** | 229 Mrd. | 10 Mrd. | ~229 GB | ✅ Ja | Modified MIT — kommerziell frei |
| **[GLM-5](https://huggingface.co/zai-org/GLM-5)** | 744 Mrd. | 40 Mrd. | ~744 GB | ❌ Nein | MIT — kommerziell uneingeschränkt |
| **[Kimi K2.5](https://huggingface.co/moonshotai/Kimi-K2.5)** | 1 Bio. | 32 Mrd. | ~600+ GB | ❌ Nein | Modified MIT — kommerziell frei unter 100M MAU |

GLM-5 und Kimi K2.5 erfordern mehrere zusammengeschaltete Maschinen und kommen für den Einzelmaschinenbetrieb nicht in Frage.

**Qualitätsstandard:** Volle Qualität (FP8-Präzision) ist das Minimum für produktive Kundenarbeit — nahezu verlustfrei bei über 95 % der Originalleistung. Komprimierte Varianten gelten als experimentell.

Die Modelllandschaft verändert sich wöchentlich. Wir verfolgen kontinuierlich, welche Modelle in voller Qualität auf einer einzelnen Maschine unter 10.000 € betrieben werden können.

### Hardware-Infrastruktur

Wir setzen auf einen Mac Studio als eigene Entwicklungsinfrastruktur — ein Desktop-Gerät für unter 10.000 €, das in jedes Rechenzentrum passt und weniger Strom verbraucht als ein Wasserkocher. „Voll lokal" bedeutet: Das Modell läuft auf dieser Maschine. Kundendaten verlassen sie nicht.

| System | Speicher | Bandbreite | Preis | Formfaktor |
|--------|----------|-----------|-------|------------|
| **[Mac Studio M3 Ultra](https://www.apple.com/mac-studio/specs/)** | 512 GB | 819 GB/s | ~9.500 € | Desktop |
| **[Mac Studio M5 Ultra](https://www.macworld.com/article/2973459/2026-mac-studio-what-we-know-about-the-upcoming-m5-update.html)** *(erwartet)* | 512 GB | ~1.100 GB/s | ~9.000 € | Desktop |
| **[Tinybox Red v2](https://tinycorp.myshopify.com/products/tinybox-red-v2)** | 144 GB | ~1 TB/s | ~12.000 € | 12U Rack |
| **[NVIDIA DGX Spark](https://www.nvidia.com/en-us/products/workstations/dgx-spark/)** | 128 GB | 273 GB/s | ~4.000 € | Desktop |

**Stromkosten im Vergleich** (bei 0,25 €/kWh, deutscher Gewerbestrompreis):

| System | Last unter KI-Inferenz | Jährliche Stromkosten (24/7) |
|--------|----------------------|----------------------------|
| **Mac Studio M3 Ultra** | ~200 W | **~438 €** |
| Tinybox Red v2 (6× GPU) | ~3.200 W | ~7.008 € |
| NVIDIA RTX 5090 (1 Karte, 32 GB) | ~575 W | ~1.261 € |
| NVIDIA H100 SXM (1 Karte, 80 GB) | ~700 W | ~1.533 € |

Der Mac Studio spart gegenüber Rack-Lösungen rund 6.500 € Strom pro Jahr — das entspricht nahezu dem Kaufpreis des Geräts.

**Warum Mac Studio:** 512 GB Arbeitsspeicher (3,5× mehr als die nächste Alternative), Desktop-Formfaktor, ~100 W Stromverbrauch (gegenüber 3.200 W bei Rack-Lösungen). Höchste Speicherkapazität im Budget unter 10.000 €.

**Skalierungsoption:** [EXO](https://github.com/exo-explore/exo) (kostenlos, Open Source) ermöglicht es, mehrere Maschinen zu einem Verbund zusammenzuschalten — für zukünftige Modelle, die mehr als 512 GB benötigen.

### Rechtliche Grundlage

Für Cloud-basierte Arbeit (Stufe 1) nutzen wir ausschließlich das Geschäftsprodukt von Anthropic. Die wesentlichen vertraglichen Zusicherungen:

- **Kein Training** auf Kundendaten — vertraglich ausgeschlossen
- **30-Tage-Löschfrist** — automatisch, ohne Antrag
- **DPA** — automatisch im Vertrag enthalten
- **EU-Vertragspartner:** Anthropic Ireland, Limited
- **Zertifizierungen:** SOC 2 Type 2, ISO 27001, ISO 27017, ISO 27018

Für lokale Verarbeitung (Stufe 2 und 3) schließen wir eine individuelle Datenverarbeitungsvereinbarung ab, die regelt, wie Kundendaten auf unserer Hardware gehandhabt werden.

**Quellenverweise:** [Anthropic Commercial Terms](https://www.anthropic.com/legal/commercial-terms) | [DPA](https://www.anthropic.com/legal/data-processing-addendum) | [Regional Compliance](https://claude.com/regional-compliance)


---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

