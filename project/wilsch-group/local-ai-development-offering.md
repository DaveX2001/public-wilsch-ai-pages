---
publish: true
---

# Lokale KI-Entwicklung — Design Doc
[[client-wilsch-group]]

Design Doc für eine lokale KI-Entwicklungskapazität: Hardware, Modelle, rechtlicher Rahmen und kommerzielles Angebot für datensensible Kunden.

---

## Problemstellung

Wir nutzen Cloud-basierte KI (Claude von Anthropic) für unsere gesamte Kundenentwicklung — sowohl für das Lösungsdesign als auch für die Code-Implementierung. Das funktioniert für die meisten Kunden. Einige Kunden können jedoch nicht akzeptieren, dass ihre Daten von externen Cloud-Diensten verarbeitet werden — sei es aufgrund von Datensouveränität, regulatorischen Anforderungen (DSGVO, BaFin, KRITIS) oder internen Sicherheitsrichtlinien, die das Senden von Daten an US-Anbieter grundsätzlich untersagen.

Wichtig: Anonymisierte Daten (echte Schemastruktur, synthetische Werte) sind für die KI genauso effektiv wie Echtdaten. Claude versteht die Datenstruktur, ohne echte Kundendatensätze zu sehen. Das eigentliche Problem entsteht erst, wenn ein Kunde nicht einmal anonymisierte Schemata in die Cloud senden will — dann verliert die KI jeglichen Kontext über die Datenstruktur und kann nicht effektiv arbeiten.

Heute fehlt uns eine glaubwürdige lokale Alternative für genau diese Kunden. Ohne eine solche Alternative verlieren wir Aufträge, bei denen Datensensibilität nicht verhandelbar ist.

**Voraussetzungen:**
- Open-Source-Frontier-Modelle (Qwen3.5-397B, GLM-5, Kimi K2.5) erreichen mittlerweile Claude Code 4.5 (Sonnet/Opus)-Qualität für Code-Implementierungsaufgaben
- Consumer-Hardware (Mac Studio 512GB) kann diese Modelle in produktionsreifer Präzision (FP8) für unter 10.000€ betreiben
- Mindestens ein aktiver Kundenprospekt hat Datensensibilitätsanforderungen, die Anthropics Standard-DPA nicht vollständig abdecken kann

---

## Erfolgsdefinition

| Element | Definition |
|---------|-----------|
| **Ziel** | Ein Drei-Stufen-Angebot, das jeden Kunden bedienen kann — unabhängig von dessen Datenanforderungen |
| **Erfolg** | Thomas und Ulrich können in Kundengesprächen die Frage „Wie gehen Sie mit unseren Daten um?" konkret und glaubwürdig beantworten |
| **Done-Test** | Können wir für alle drei Stufen (Cloud, Hybrid, Voll-Lokal) Hardware, Modell und rechtlichen Rahmen benennen? → Wenn ja → technische Grundlage steht |

---

## Ansatz

### 1. Drei-Stufen-Modell

Unser Angebot gliedert sich in drei Stufen, je nach Datenanforderung des Kunden:

| Stufe | Kundensituation | Wie wir arbeiten | Lokale Hardware nötig? |
|-------|----------------|------------------|----------------------|
| **Stufe 1: Cloud (DPA)** | Kunde unterzeichnet DPA, akzeptiert Anthropics Bedingungen | Claude übernimmt alles — Design und Implementierung | Nein |
| **Stufe 2: Hybrid** | Anonymisierte Schemata OK, echte Daten müssen lokal bleiben | Claude designt mit anonymisierten Daten, lokales Modell implementiert mit echten Daten | Ja |
| **Stufe 3: Voll-Lokal** | Keine Daten dürfen in die Cloud | Lokales Modell übernimmt Design und Implementierung | Ja |

**Wie das in der Praxis funktioniert:**

- **Phase 1 — Design:** Marius entwirft die technische Lösung mit Claude (Cloud-KI). Bei Stufe 2 sieht Claude nur anonymisierte Datenstrukturen (echte Tabellennamen und Spalten, synthetische Werte). Bei Stufe 3 arbeitet Claude ohne Kundendaten.
- **Phase 2 — Implementierung:** Ein lokales KI-Modell auf unserem Mac Studio implementiert den Code gegen die echten Kundendaten. Keine Daten verlassen unsere Maschine.

**USP:** *Wir designen mit der besten KI der Welt. Wir implementieren mit euren Daten auf unserer lokalen Infrastruktur.*

Die DPA (Datenverarbeitungsvereinbarung) bestimmt, welche Stufe ein Kunde benötigt. Eine gute DPA ermöglicht Stufe 1 — die einfachste und qualitativ beste Variante. Lokale Hardware ist die Rückfalloption für Kunden, die Stufe 1 nicht akzeptieren können.

### 2. Modellauswahl

**Aktueller Stand (Februar 2026):** Vier Frontier-Open-Source-Modelle wurden evaluiert:

| Modell | Parameter | Aktive Parameter | FP8-Speicherbedarf | Passt auf 512GB? | Lizenz |
|--------|-----------|-----------------|-------------------|-----------------|--------|
| **[Qwen3.5-397B](https://huggingface.co/Qwen/Qwen3.5-397B-A17B)** | 397 Mrd. (MoE) | 17 Mrd. | ~397 GB | ✅ Ja | [Apache 2.0](https://huggingface.co/Qwen/Qwen3.5-397B-A17B) — kommerziell uneingeschränkt |
| **[MiniMax M2.5](https://huggingface.co/MiniMaxAI/MiniMax-M2.5)** | 229 Mrd. (MoE) | 10 Mrd. | ~229 GB | ✅ Ja | [Modified MIT](https://github.com/MiniMax-AI/MiniMax-M2.5/blob/main/LICENSE) — kommerziell frei |
| **[GLM-5](https://huggingface.co/zai-org/GLM-5)** | 744 Mrd. (MoE) | 40 Mrd. | ~744 GB | ❌ Nein | [MIT](https://glm5.net/) — kommerziell uneingeschränkt |
| **[Kimi K2.5](https://huggingface.co/moonshotai/Kimi-K2.5)** | 1 Bio. (MoE) | 32 Mrd. | ~600+ GB | ❌ Nein | [Modified MIT](https://github.com/MoonshotAI/Kimi-K2.5/blob/master/LICENSE) — kommerziell frei unter 100M MAU |

**Ergebnis:** Qwen3.5-397B und MiniMax M2.5 sind die beiden Frontier-Modelle, die bei FP8-Präzision auf einer einzelnen Maschine (512GB) laufen. M2.5 ist auf Softwareentwicklung spezialisiert (80,2% SWE-Bench Verified), Qwen3.5 ist das stärkere Allzweckmodell. GLM-5 und Kimi K2.5 erfordern Multi-Maschinen-Clustering via [EXO](https://github.com/exo-explore/exo).

**Qualitätsstandard:**
- **FP8 ist das Minimum** für produktive Kundenarbeit (nahezu verlustfrei, >95% Qualität)
- **4-Bit-Quantisierung** (FP4, NF4, INT4) gilt als experimentell — nur für interne Tests
- Apple-Silicon-optimierte Variante: [Qwen3.5-397B MLX 4-bit](https://huggingface.co/mlx-community/Qwen3.5-397B-A17B-4bit)

**Monitoring:** Marius überwacht laufend, welche Frontier-Modelle bei FP8 auf einer einzelnen, voll ausgestatteten Maschine (512GB, <10.000€) laufen. Die Modelllandschaft verändert sich wöchentlich — Qwen3.5 wurde am 16. Februar 2026 veröffentlicht, GLM-5 fünf Tage zuvor.

### 3. Hardware-Infrastruktur

**Entscheidung:** Wir kaufen einen Mac Studio als interne Entwicklungsinfrastruktur. Keine Kunden-Hardware — unsere Maschine, unsere Kontrolle. „Voll lokal" bedeutet: Das Modell läuft auf diesem Mac Studio, Kundendaten verlassen diese Maschine nicht.

**Kaufplan:**
1. **Sofort:** Mac Studio M3 Ultra (512GB, ~9.500€) — verfügbar, deckt aktiven Bedarf
2. **Frühjahr 2026:** Upgrade auf Mac Studio M5 Ultra (512GB, ~1.100 GB/s Bandbreite)

**Hardware-Vergleich:**

| System | GPU-Speicher | Bandbreite | Preis | Qwen3.5 FP8? | Formfaktor |
|--------|-------------|-----------|-------|--------------|------------|
| **[Mac Studio M3 Ultra](https://www.apple.com/mac-studio/specs/)** | 512GB unified | 819 GB/s | ~9.500€ | ✅ | Desktop |
| **[Mac Studio M5 Ultra](https://www.macworld.com/article/2973459/2026-mac-studio-what-we-know-about-the-upcoming-m5-update.html)** *(erwartet)* | 512GB unified | ~1.100 GB/s | ~9.000€ | ✅ | Desktop |
| **[Tinybox Red v2](https://tinycorp.myshopify.com/products/tinybox-red-v2)** | 144GB diskret (6× 7900XTX) | ~1 TB/s | ~12.000€ | ✅ | 12U Rack |
| **[NVIDIA DGX Spark](https://www.nvidia.com/en-us/products/workstations/dgx-spark/)** | 128GB unified | 273 GB/s | ~4.000€ | ✅ | Desktop |

**Warum Mac Studio:** 512GB Unified Memory (3,5× mehr als Tinybox), Desktop-Formfaktor, ~100W Stromverbrauch (vs. 3.200W bei Tinybox), Betriebssystemintegration für Entwicklungsworkflows. Höchste Speicherkapazität im Budget unter 10.000€.

**Skalierungsoption:** [EXO](https://github.com/exo-explore/exo) (kostenlos, Open Source) ermöglicht es, mehrere Maschinen zu einem Cluster zu verbinden — für zukünftige Modelle, die >512GB bei FP8 benötigen. Thunderbolt 5 am Mac Studio ist eine Voraussetzung für effizientes Clustering. Aktuell nicht benötigt — eine Einzelmaschine reicht für Qwen3.5-397B. Für GLM-5 (744B) oder Kimi K2.5 (1T) wäre EXO-Clustering mit einer zweiten Maschine erforderlich.

### 4. Rechtlicher Rahmen

Anthropic (der Hersteller von Claude) bietet zwei verschiedene Produkte mit **grundlegend unterschiedlichen Datenschutzbedingungen**. Für die Bewertung ist es entscheidend, diese Unterscheidung zu verstehen.

> **Legende:**
>
> 🏢 **Commercial** = Anthropic API — das Produkt für Unternehmen. Wir nutzen dies für Kundenarbeit. Anthropic ist hier Auftragsverarbeiter, der Kunde bleibt Datenverantwortlicher. Anthropic stellt eine eigene [DPA (Datenverarbeitungsvereinbarung)](https://www.anthropic.com/legal/data-processing-addendum) bereit, die automatisch gilt — keine separate Verhandlung nötig.
>
> 👤 **Consumer** = claude.ai — das Produkt für Endverbraucher. Anthropic ist hier selbst Datenverantwortlicher. Keine DPA, andere Regeln. **Darf nicht für Kundenarbeit verwendet werden.**

#### Was Anthropic zusichert (Stand Februar 2026)

**Training auf Daten:**
- 🏢 Commercial: Kein Training auf Kundendaten — vertraglich verboten ([Commercial Terms §B](https://www.anthropic.com/legal/commercial-terms))
- 👤 Consumer: Training standardmäßig aktiv, Opt-out möglich ([Privacy Policy](https://www.anthropic.com/legal/privacy))

**Datenspeicherung:**
- 🏢 Commercial: 30 Tage, dann automatisch gelöscht ([Retention Policy](https://privacy.anthropic.com/en/articles/7996866-how-long-do-you-store-personal-data))
- 👤 Consumer: Solange Konto aktiv, Löschung erst auf Anfrage ([Consumer Retention](https://privacy.anthropic.com/en/articles/10023548-how-long-do-you-store-personal-data))

**DPA (Datenverarbeitungsvereinbarung):**
- 🏢 Commercial: Anthropic stellt eigene DPA bereit, automatisch im Vertrag enthalten ([Anthropic DPA](https://www.anthropic.com/legal/data-processing-addendum))
- 👤 Consumer: Keine DPA — Anthropic ist selbst Verantwortlicher

**EU-Vertragspartner:**
- 🏢 👤 Beide: Anthropic Ireland, Limited ([Regional Compliance](https://claude.com/regional-compliance))

**Zertifizierungen:**
- 🏢 👤 Beide: SOC 2 Type 2, ISO 27001, ISO 27017, ISO 27018 ([Regional Compliance](https://claude.com/regional-compliance))

#### Bekannte Einwandspunkte für regulierte Kunden

Auch mit der Commercial API und Anthropics DPA bleiben folgende Punkte, die regulierte Kunden (BaFin, KRITIS, Gesundheitswesen) bemängeln können:

**Hohes Risiko:**
- 🏢 👤 **US CLOUD Act** — SCCs (Standardvertragsklauseln) schützen nicht vor US-Behördenzugriff auf Anthropic PBC als US-Muttergesellschaft
- 🏢 **Keine EU-Datenresidenz garantiert** — die Standard-API routet global. EU-Verarbeitung nur über AWS Bedrock EU oder GCP Vertex EU möglich (erfordert separate Konfiguration)
- 🏢 👤 **Vage „harmful use"-Löschausnahme** — Anthropic darf Daten potenziell unbefristet speichern, wenn sie als schädlich eingestuft werden

**Mittleres Risiko:**
- 🏢 👤 **Safety-Review** — wenn Anthropics Classifier Inhalte flaggt, können Daten bis zu 2 Jahre gespeichert werden, unabhängig von anderen Löschfristen
- 🏢 **Sub-Processor-Liste** — nicht vertraglich fixiert, rollierend aktualisiert, nur 15 Tage Einspruchsfrist für neue Sub-Processors

**→ Kunden mit diesen Einwänden werden auf Stufe 2/3 (lokale Inferenz) geleitet.**

#### Offene Aufgaben

**Undefined:** Für Stufe 2/3 benötigen wir eine eigene Wilsch AI DPA, die regelt, wie wir Kundendaten auf unserem Mac Studio handhaben. Separates Arbeitspaket, erfordert juristische Prüfung. → [siehe Meeting-Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/local-ai-development-meeting-agenda)

**Undefined:** Preismodell für lokale Entwicklungsstufen → [siehe Meeting-Agenda](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/local-ai-development-meeting-agenda)

---

## Quellen

- **Issue:** [#834: Evaluate Local AI Development Hardware & Offering](https://github.com/DaveX2001/deliverable-tracking/issues/834)
- **Org Chart:** [Organization Chart - Wilsch AI Services](https://mariuswilsch.github.io/public-wilsch-ai-pages/global/organization-chart-wilsch-ai-services)
- **Anthropic Terms:** [Commercial Terms](https://www.anthropic.com/legal/commercial-terms) | [DPA](https://www.anthropic.com/legal/data-processing-addendum) | [Regional Compliance](https://claude.com/regional-compliance)
- **EXO:** [github.com/exo-explore/exo](https://github.com/exo-explore/exo)
- **Qwen3.5:** [HuggingFace](https://huggingface.co/Qwen/Qwen3.5-397B-A17B) | [MLX 4-bit](https://huggingface.co/mlx-community/Qwen3.5-397B-A17B-4bit)
- **MiniMax M2.5:** [HuggingFace](https://huggingface.co/MiniMaxAI/MiniMax-M2.5)
- **Session:** ce7b3c88-8c2e-47cb-b165-daeae5aa8c10.jsonl

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

