---
publish: true
---

# KI-Performance: Hardware-Vergleich für lokale Inferenz
[[client-wilsch-group]]

Präsentationsmaterial für das FSO-Seminar am 27. März 2026. Erklärt die leistungsbestimmenden Faktoren für KI und vergleicht vier Testsysteme anhand gemessener und geschätzter Werte.

---

## Folie 1: Performance-bestimmende Faktoren für KI

> **Was Sie von Datenbanken kennen:** Mehr RAM = mehr Daten im Speicher. Mehr Kerne = mehr parallele Abfragen. Mehr IOPS = schnellere Zugriffe. Bei KI gelten andere Regeln.
>
> KI-Modelle haben **zwei Arbeitsphasen** mit unterschiedlichen Engpässen. Mehr RAM allein hilft nicht — die entscheidende Frage ist: **Wie schnell kann die Hardware rechnen (Prefill) und wie schnell kann sie Daten lesen (Decode)?** Diese Unterscheidung bestimmt jede Hardware-Entscheidung.

### Zwei Verarbeitungsarten

| | Prefill (Einlesen) | Decode (Auslesen) |
|--|--|--|
| **Was passiert** | Daten werden in das Modell eingelesen | Modell generiert Wörter / Ergebnisse |
| **Analogie** | Ein ganzes Buch lesen | Ein neues Kapitel schreiben |
| **Hardware-Anforderung** | Rechenleistung (TFLOPS) | Speicherbandbreite (GB/s) |
| **Messzahl** | TTFT — Time to First Token | TPS — Tokens per Second |

> **Erste Frage vor jeder Hardware-Entscheidung:** Habe ich mehr Einlese- oder mehr Auslese-Aufgaben?
>
> - **Dokumentenverarbeitung** (z.B. PDF-Analyse, Bildverarbeitung): 95% Prefill → Rechenleistung entscheidend
> - **Chatbot / Dialogsystem:** 80% Decode → Speicherbandbreite entscheidend

### Hardware-Kennzahlen

| Kennzahl | Bedeutung | Einheit |
|----------|-----------|---------|
| **Zahl der Kerne** | Wie viele Recheneinheiten parallel arbeiten | CPU-Kerne oder GPU-Kerne |
| **CPU vs GPU Kerne** | CPU = wenige, vielseitige Kerne; GPU = tausende, spezialisierte Kerne | — |
| **Rechenleistung** | Wie schnell Berechnungen durchgeführt werden (Prefill) | TFLOPS (Tera Floating Point Operations per Second) |
| **RAM Größe** | Wie viel Speicher verfügbar ist — bestimmt, welche Modelle geladen werden können | GB |
| **RAM Bandbreite** | Wie schnell Daten aus dem Speicher gelesen werden (Decode) | GB/s |

### Präzision über Quantifizierung

Jeder Parameter eines Modells ist eine Zahl (z.B. `0.0472`). Diese Zahl wird mit einer bestimmten Anzahl von **Bits** gespeichert — je mehr Bits, desto genauer die Darstellung, aber auch desto mehr Speicher wird benötigt.

| Präzision | Bits pro Zahl | Speicher pro 8B-Modell | Genauigkeit |
|-----------|--------------|----------------------|-------------|
| **FP32** | 32 Bit (4 Bytes) | 32 GB | Höchste Präzision |
| **FP16** | 16 Bit (2 Bytes) | 16 GB | Standardpräzision |
| **FP08** | 8 Bit (1 Byte) | 8 GB | Leicht gerundet |
| **FP04** | 4 Bit (0,5 Byte) | 4 GB | Gerundet — für die meisten Anwendungen ausreichend |

> **Halbe Präzision = halber Speicher.** Ob das auch doppelte Geschwindigkeit bedeutet, hängt von der Verarbeitungsart ab:
> - **Decode (Auslesen):** Ja — halbes Modell = halb so viele Daten aus dem Speicher laden = doppelte Geschwindigkeit (auf allen Systemen)
> - **Prefill (Einlesen):** Nur auf NVIDIA — dort kann die Hardware nativ mit kleineren Zahlen rechnen. Apple und IBM rechnen intern immer mit FP16, egal wie klein das Modell geladen wurde.
>
> Die Qualität der Ergebnisse bleibt bei FP04 für Textverarbeitung nahezu gleich.

### Entscheidender Unterschied: Native Berechnung

Jede Hardware kann ein FP04-Modell **laden und speichern** — das spart Speicher auf allen Systemen. Der entscheidende Unterschied liegt in der **Berechnung**:

| | Modell laden (FP04) | Berechnung | Spec (TFLOPS) | Gemessen (FP04-Modell) |
|--|--|--|--|--|
| **DGX Spark (Blackwell)** | ✅ 4 GB | FP04 via Marlin-Kernel¹ | 1.000 TFLOPS (Spec) | **63,3 tok/s Decode, 343ms TTFT** |
| **Apple M5 Ultra (proj.)** | ✅ 4 GB | ✅ Nativ via Neural Acc. | TBD | TBD (erwartet ~Mitte 2026) |
| **Mac Studio (M3 Ultra)** | ✅ 4 GB | ⬆️ Upcast zu FP16 | 65 TFLOPS | Kein FP04-Vorteil bei Berechnung |
| **IBM Power 10** | ✅ 4 GB | ⬆️ Upcast zu BF16 | 2 TFLOPS | Kein FP04-Vorteil bei Berechnung |

> ¹ **Technisches Detail:** Der DGX Spark (SM121-Chip) nutzt FP04 derzeit über den Marlin-Kernel — eine gewichtsbasierte FP04-Kompression, die in höherer Präzision rechnet. Das ist nicht dasselbe wie volle native FP04-Tensor-Core-Berechnung, liefert aber messbare Vorteile: **4,65× schnelleres Decode** und **1,83× schnelleres Prefill** gegenüber dem gleichen Modell in BF16 (gemessen mit llama-benchy, synthetische Benchmarks). Ein nativer FP04-Pfad (FLASHINFER_CUTLASS, PR #98) wurde ebenfalls getestet: 29% schnelleres Prefill, 7% langsameres Decode — besser für dokumentenlastige Workloads.
>
> **Spektrum statt Schwarz-Weiß:** NVIDIA führt heute bei FP04-Inferenz — auch wenn der Mechanismus (Marlin) technisch kein „natives FP04" im Chip-Sinne ist. Apple M5 Ultra wird voraussichtlich Mitte 2026 echte native FP04-Berechnung via GPU Neural Accelerators bieten. IBM Spyre (Power11) hat ebenfalls FP04-Unterstützung, benötigt aber eigene Kernel und ein neues Power11-System. Die Landschaft bewegt sich.
>
> **Wann ist das relevant?** Bei **Prefill** (Einlesen) — dort ist die Rechenleistung der Engpass. Bei Decode (Auslesen) ist die Speicherbandbreite der Engpass. Für Dokumentenverarbeitung (95% Prefill) ist der Unterschied zwischen FP04-fähiger und FP16-only Hardware **entscheidend**.

---

## Folie 2: Performance-Werte angewendet auf unsere Testsysteme

### Hardware + OS

| | 1x IBM Power 10 | 1x NVIDIA DGX Spark | Apple Mac Studio M3 Ultra | Apple M5 Ultra (proj.)¹ | 8× IBM Spyre (Power11)² | NVIDIA H100 (Datacenter)³ |
|--|--|--|--|--|--|--|
| **Kerne** | 5 CPU | 6.144 GPU | 80 GPU | 80 GPU + Neural Acc. | 32 AI-Kerne × 8 Karten | 16.896 GPU |
| **Rechenleistung (FP16)** | ~2 TFLOPS | ~100 TFLOPS | 65,5 TFLOPS | ~66 TFLOPS | TBD | ~1.979 TFLOPS |
| **Rechenleistung (FP04)** | — | 500 TFLOPS (Spec) | — (Upcast) | ✅ Nativ (TBD) | FP04/INT4 (custom Kernel) | ~3.958 TFLOPS |
| **RAM** | 128 GB DDR4 | 128 GB LPDDR5x | 256 GB LPDDR5x | bis 512 GB LPDDR5x | 1 TB LPDDR5 | 80 GB HBM3 |
| **RAM Bandbreite** | ~400 GB/s (137 gemessen) | 273 GB/s | 819 GB/s | ~1.200 GB/s | 1.600 GB/s | 3.350 GB/s |
| **Leistungsaufnahme** | ~1.000 W (System) | ~100 W | ~370 W | ~190 W | ~600 W (8 Karten) | ~700 W (GPU allein) |
| **Betriebssystem** | AlmaLinux 10.0 | Ubuntu / DGX OS | macOS | macOS | RHEL 9.6+ | Linux |
| **Software-Ökosystem** | Ollama (ppc64le) | CUDA, vLLM, Ollama | MLX, Ollama | MLX, Ollama | Custom Kernel (PyTorch) | CUDA, vLLM, TRT-LLM |
| **Eigenständiger Computer** | ✅ | ✅ | ✅ | ✅ | ❌ (PCIe-Karte für Power11) | ❌ (benötigt Host-Server) |
| **Preis** | ~50.000–100.000 € | **~3.700 €** | ~4.000–10.000 € | ~5.000–12.000 € | ~100.000–150.000 €⁴ | ~30.000–40.000 €⁵ |

> ¹ **M5 Ultra (projiziert):** Basierend auf 2× M5 Max. Erstmals native FP04-Berechnung via GPU Neural Accelerators. Erwartet ~Mitte 2026 (WWDC).
>
> ² **IBM Spyre:** 8 PCIe-Karten im ENZ0 Expansion Drawer. Benötigt ein neues **Power11-System** (S1122/S1124) — nicht kompatibel mit bestehenden Power10-Installationen. 5nm ASIC, LPDDR5, 75W pro Karte. Software: eigenes PyTorch-Backend mit Red Hat AI Inference Server (vLLM). Quellen: [IBM Newsroom](https://newsroom.ibm.com/2025-10-07-ibm-introduces-the-spyre-accelerator-for-commercial-availability), [IBM Research](https://research.ibm.com/blog/lifting-the-cover-on-the-ibm-spyre-accelerator).
>
> ³ **NVIDIA H100:** Datacenter-GPU als Goldstandard-Referenz. Theoretische Werte — nicht von uns getestet. Benötigt zusätzlich Host-Server, Netzwerk-Infrastruktur, Kühlung. Gesamtsystemkosten deutlich höher als GPU-Preis allein.
>
> ⁴ Spyre-Systempreis inkl. Power11-Server + 8 Karten + Expansion Chassis + RHEL-Subscription. Keine offizielle IBM-Preisliste veröffentlicht — Schätzung basierend auf IT Jungle Analyse.
>
> ⁵ H100-GPU-Preis ohne Host-Server. Gesamtsystem (DGX H100): ~€200.000+.

### Benchmark-Ergebnisse (PDF-Dokumentenverarbeitung)

| | IBM Power 10 | 1x DGX Spark (sequentiell) | 1x DGX Spark (parallel) |
|--|--|--|--|
| **Modell** | Qwen 3-VL 8B | Qwen 3-VL 30B-A3B NVFP4 | Qwen 3-VL 30B-A3B NVFP4 |
| **TTFT (Prefill)** | ~90 s/Seite | ~0,24 s/Seite | ~0,24 s/Seite |
| **TPS (Decode)** | 12,4 tok/s | 79,5 tok/s | 79,5 tok/s |
| **Seiten pro Stunde** | 33 | 1.319 | **6.853** |
| **Qualität** | **9/9** Kriterien | **9/9** Kriterien | **9/9** Kriterien |

> **Hinweis:** Gemessen auf Projekt 35764, 72 DPI, 294 Seiten. Power 10: Ollama, CPU-only (Parallelisierung nicht möglich — NUM_PARALLEL=2 bringt nur 1,03×). DGX Spark: vLLM mit FP04-MoE-Modell (30B Parameter, 3B aktiv), Sweet Spot bei 8 parallelen Anfragen. Bei 16 Anfragen sinkt der Durchsatz (GPU-Decode-Bandbreite gesättigt). Quelle: [#1232](https://github.com/DaveX2001/deliverable-tracking/issues/1232).

### Warum ist die effektive Bandbreite so unterschiedlich?

Die theoretische Bandbreite (GB/s) ist bei allen Systemen auf dem Papier vergleichbar. Der Unterschied liegt darin, **wie viele gleichzeitige Speicheranfragen** die Hardware erzeugen kann:

| | IBM Power 10 (CPU) | DGX Spark / Mac Studio (GPU) |
|--|--|--|
| **Kerne** | 5 CPU-Kerne | 6.144 / 80 GPU-Kerne |
| **Gleichzeitige Speicheranfragen** | ~50–100 | Tausende |
| **Effektive Nutzung** | ~34% der theoretischen Bandbreite | ~85% der theoretischen Bandbreite |
| **Analogie** | Autobahn mit 8 Spuren, aber nur 50 Autos | Autobahn mit 8 Spuren und 6.000 Autos |

> Eine CPU ist für **schnellen Zugriff auf einzelne Daten** optimiert (Datenbanken, ERP). Eine GPU ist für **massiven Datendurchsatz** optimiert (KI, Grafikverarbeitung). Dieselbe Bandbreite auf dem Papier, aber die GPU kann sie tatsächlich ausnutzen.
>
> **Gemessen auf unserer Power 10:** 137 GB/s von 400 GB/s theoretisch = 34% Nutzung. Nicht weil der Speicher langsam ist, sondern weil 5 CPU-Kerne einfach nicht genug gleichzeitige Anfragen erzeugen können, um den Speicherbus auszulasten.

---

## Folie 3: Große und kleine Sprachmodelle

### Größenordnungen

| | Kleines Modell | Großes Modell |
|--|--|--|
| **Definition** | Weniger als 9 Milliarden Parameter (< 9B) | Mehr als 9 Milliarden Parameter (> 9B) |
| **Beispiel** | Qwen 3.5 4B | MiniMax M2.5 (230B) |
| **RAM-Bedarf (FP16)** | 8 GB | 450 GB |
| **RAM-Bedarf (FP08)** | 4 GB | 220 GB |
| **Einsatz** | Betrieb / Kundenausführung | Entwicklung / Programmierung |

> **Was ist ein Parameter?** Eine einzelne Zahl (z.B. `0.0472`), die das Modell beim Training gelernt hat. Mehr Parameter = mehr Wissen = bessere Ergebnisse — aber auch mehr Speicherbedarf.

### Kernaussage: Kleine Modelle werden immer intelligenter

Alle 6–12 Monate erreichen kleine Modelle das Intelligenzniveau von großen Modellen der Vorgängergeneration — ohne die Parameteranzahl zu ändern.

**Aktuell:** Qwen 3.5 9B (2026, 10 GB) ist so intelligent wie GPT-OSS 120B (2025, 240 GB) — ein Modell mit **13x mehr Parametern**, erschienen nur ein Jahr zuvor. ([Quelle: VentureBeat](https://venturebeat.com/technology/alibabas-small-open-source-qwen3-5-9b-beats-openais-gpt-oss-120b-and-can-run))

> **Gleiche Intelligenz, viel weniger Hardware.**
> Das bedeutet: Die Hardware-Anforderungen für den Betrieb sinken kontinuierlich.

### Auswirkungen auf Hardware

| System | Kleine Modelle (< 9B) | Große Modelle (> 9B) |
|--------|----------------------|---------------------|
| IBM Power 10 (128 GB) | ✅ Möglich | ⚠️ Möglich, aber sehr langsam |
| IBM Power 11 (1.024 GB) | ✅ Möglich | ⚠️ Passt in RAM, aber CPU-only — sehr langsam |
| 2x DGX Spark (256 GB) | ✅ Möglich | ✅ Möglich (z.B. MiniMax 230B bei FP04) |
| Mac Studio M3 Ultra (256 GB) | ✅ Möglich | ✅ Möglich (z.B. MiniMax 230B bei FP08 = 220 GB) |

---

## Folie 4: Generelle und spezialisierte Sprachmodelle

### Zwei Arten von Sprachmodellen

| | Generelles Modell | Spezialisiertes Modell |
|--|--|--|
| **Kennzeichen** | Soll alles können | Auf bestimmtes Fachgebiet trainiert |
| **Abdeckung** | 99% aller Anwendungsfälle | 1% — wenn Generalisten nicht ausreichen |
| **Beispiel** | Qwen 3.5 (Textverarbeitung, Analyse) | MiniMax M2.5 (Programmierung) |
| **Herkunft** | Vom Hersteller geliefert | Fein-getuntes generelles Modell |

> **Spezialisierung ist der letzte Schritt.** Man probiert zuerst ein generelles Modell. Nur wenn das nicht ausreicht, wird ein spezialisiertes Modell eingesetzt oder ein generelles Modell fein-getuned.

### Einsatzstrategie

| Phase | Modelltyp | Warum |
|-------|----------|-------|
| **Entwicklung** | Großes Modell | Versteht Aufgaben ohne viel Vorarbeit — wie ein erfahrener Mitarbeiter |
| **Betrieb / Kunde** | Kleines Modell | Schneller, günstiger, läuft auf weniger Hardware — wie ein gut eingearbeiteter Spezialist |

### Für unsere Kunden relevante Modelle

| Modell | Typ | Parameter | Einsatz |
|--------|-----|-----------|---------|
| **Qwen 3.5** | Generell | 4B / 9B | Betrieb: Dokumentenverarbeitung, Textanalyse |
| **MiniMax M2.5** | Spezialisiert (Code) | 230B | Entwicklung: KI-Programmierung |

> **Amerikanische Hersteller** (OpenAI, Anthropic) bieten in der Regel nur Cloud-Modelle an.
> **Chinesische Hersteller** (Alibaba/Qwen, MiniMax) bieten lokale Open-Source-Modelle an.
> Für Datensouveränität bei unseren Kunden setzen wir auf lokale Modelle.

---

## Folie 5: REKERS Benchmark — Gemessene Ergebnisse

### Testaufbau

Dieselbe Aufgabe (Kriterienextraktion aus 240 PDF-Seiten, Projekt 35764) wurde mit drei verschiedenen Modellen auf IBM Power 10 durchgeführt. Alle Modelle laufen lokal — keine Cloud, keine externen Dienste.

| | Qwen 3.5 4B | Qwen 3.5 9B | Qwen 3-VL 8B |
|--|--|--|--|
| **Parameter** | 4 Milliarden | 9 Milliarden | 8 Milliarden |
| **Modellgröße** | 3,4 GB | 6,6 GB | 6,1 GB |
| **Verarbeitungszeit (240 Seiten)** | ~5,7 Std. | ~6,9 Std. | **~7,3 Std.** |
| **Seiten pro Stunde** | 42 | 35 | 33 |
| **Decode-Geschwindigkeit** | 6,5 tok/s | 5,7 tok/s | **12,4 tok/s** |
| **Qualität (von 9 Kriterien)** | 6/9 | **9/9** | **9/9** |

> **Ergebnis:** Zwei von drei Modellen finden **alle 9 Kriterien** — vollständig lokal, ohne Cloud, ohne externe Abhängigkeit.

### Warum dauert es 7 Stunden?

Die Verarbeitungszeit pro Seite besteht aus drei Phasen:

| Phase | Was passiert | Power 10 (CPU) | DGX Spark (vLLM) | Speedup |
|-------|-------------|----------------|------------------|---------|
| **Einlesen** | Modell sieht die Seite und versteht den Inhalt — Bilderkennung + Sprachverarbeitung in einem parallelen Schritt | ~76 Sekunden | **~0,24 Sekunden** | **~317×** |
| **Antworten** | Modell generiert strukturierte Antwort — ein Wort nach dem anderen (sequentiell) | ~14 Sekunden | **~2,5 Sekunden** | ~6× |
| **Gesamt** | | **~90 Sekunden** | **~2,7 Sekunden** | **~33×** |

> **Warum der extreme Unterschied beim Einlesen?** Einlesen ist eine parallele Berechnung — alle Daten der Seite werden gleichzeitig verarbeitet. Eine GPU mit 6.144 Kernen erledigt das in Millisekunden. Eine CPU mit 5 Kernen braucht über eine Minute, weil sie die gleiche Arbeit mit tausendmal weniger parallelen Recheneinheiten erledigen muss.
>
> **Warum ist Antworten nur 6× schneller?** Antworten ist sequentiell — jedes Wort hängt vom vorherigen ab. Hier zählt nicht Rechenleistung, sondern wie schnell die Hardware das Modell aus dem Speicher lesen kann (Speicherbandbreite). Die GPU ist hier nur moderat schneller als die CPU.

### Hochrechnung: 14 Projekte

| | IBM Power 10 | 1x DGX Spark |
|--|--|--|
| **1 Projekt (294 Seiten)** | ~8,9 Stunden | **~2,6 Minuten** |
| **14 Projekte** | ~5 Tage | **~36 Minuten** |

> **Hinweis:** Hochrechnung basiert auf gemessenen 6.853 Seiten/Stunde (1x DGX Spark, 8 parallele Anfragen, Qwen 3-VL 30B-A3B NVFP4 via vLLM). Power 10: ~33 Seiten/Stunde (Parallelisierung nicht möglich) → **~207× langsamer**.
>
> **Wichtig:** Diese Benchmark testet nur die Extraktion — den ersten von zwei Schritten. Der zweite Schritt (Ähnlichkeitsanalyse / Kriteriennachweis-Generierung) wird separat benchmarked, sobald die Page-Index-Infrastruktur bereit ist.

### Warum so schnell? Drei Faktoren

**1. Richtige Hardware — 6.144 statt 5 Kerne**

Eine CPU ist für schnellen Zugriff auf einzelne Daten optimiert — ideal für Datenbanken und ERP. Eine GPU ist für massiven parallelen Durchsatz optimiert — ideal für KI. Die Power 10 hat 5 CPU-Kerne. Der DGX Spark hat 6.144 GPU-Kerne. Jeder Kern verarbeitet einen Teil der KI-Berechnung gleichzeitig.

**2. Richtiges Modell — FP04 + MoE-Architektur**

Das FP04-MoE-Modell (30 Milliarden Parameter, davon nur 3 Milliarden pro Anfrage aktiv) nutzt die GPU-Kerne effizienter als herkömmliche Modelle. Weniger aktive Parameter pro Wort = weniger Rechenaufwand = höhere Geschwindigkeit. Die Qualität bleibt gleich (9/9 Kriterien).

**3. Richtige Parallelisierung — 8 Seiten gleichzeitig**

Die GPU kann 8 Seiten gleichzeitig verarbeiten — der Durchsatz steigt von 1.319 auf 6.853 Seiten pro Stunde. Ab 16 gleichzeitigen Anfragen ist die Speicherbandbreite gesättigt — der Sweet Spot liegt bei 8.

> **Warum kann die Power 10 das nicht?** Wir haben es getestet: mit 2 parallelen Anfragen steigt der Durchsatz um nur 3% (1,03×). Die 5 CPU-Kerne sind bereits mit einer einzigen KI-Anfrage voll ausgelastet. Parallelisierung erfordert Tausende von Kernen — genau das, was eine GPU bietet.

---

## Folie 6: Kaufentscheidung — Drei Wege zur lokalen KI

<img src="kaufentscheidung-infra-vergleich.png" alt="Infrastruktur-Vergleich: Kompakte KI-Appliance vs. IBM Spyre-System" width="100%">

### Kernaussage

Ihre Power 10 bleibt unverändert — Datenbanken, ERP, alles wie gehabt. Wir stellen eine eigenständige KI-Infrastruktur daneben, die sofort einsatzbereit ist.

| | Option 1: Lokale KI-Appliance ⭐ | Option 2: Disaggregierte Inferenz | Option 3: IBM Spyre Integration |
|--|--|--|--|
| **Konzept** | Eigenständiger KI-Computer neben Ihrer bestehenden Infrastruktur | Zwei spezialisierte Geräte — Einlesen und Auslesen auf jeweils optimaler Hardware | KI-Beschleunigerkarten innerhalb des IBM-Ökosystems |
| **Preis** | **~€3.700** | ~€8.000–14.000 | ~€100.000–150.000 |
| **Eigenständig** | ✅ Vollständiger Computer | ✅ Zwei eigenständige Computer | ❌ Erweiterungskarten — benötigt neues Power11-System |
| **Ihre bestehende Infrastruktur** | Unverändert | Unverändert | Neues System erforderlich |
| **Software-Ökosystem** | Offenes Ökosystem (CUDA, vLLM, PyTorch) | + Apple MLX, EXO-Cluster | Proprietäre Kernel |
| **Gemessener Speedup** | ~207× vs Power 10¹ | ~2,8× zusätzlich² | Nicht getestet |

> ⭐ **Unsere Empfehlung:** Die lokale KI-Appliance als sofortiger Einstieg. 40× schneller als Power 10 bei der Dokumentenverarbeitung — für ~€3.700 statt ~€150.000. Die disaggregierte Inferenz (Option 2) ist der strategische Upgrade-Pfad für maximale Leistung.
>
> ¹ Gemessen: 6.853 Seiten/Stunde (8 parallele Anfragen) vs 33 Seiten/Stunde (Projekt 35764, 294 Seiten, 9/9 Qualität). Details: Folie 2 + Folie 5. Quelle: [#1232](https://github.com/DaveX2001/deliverable-tracking/issues/1232).
> ² Quelle: [EXO Labs Benchmark](https://blog.exolabs.net/nvidia-dgx-spark/) — Prefill/Decode-Trennung auf separater Hardware.

---

## Quellen

### Extraction Pass — Session 2026-03-22

**Transcripts:**
- [March 14 — Ulrich + David + Marius (Hardware-Strategie, FSO)](https://app.fireflies.ai/view/01KKP083AR8GQPCYDPZ8S5HKVW)
- [March 21 — Marius + David (Benchmark-Ergebnisse, Kaufentscheidung)](https://app.fireflies.ai/view/01KM872GJ8213CZMA4ZKTJEBGA)

**Issue-Quellen:**
- [#1232 — vLLM Full Extraction Benchmark (294 Seiten, 1.319 pg/hr)](https://github.com/DaveX2001/deliverable-tracking/issues/1232)
- [#1220 — vLLM FP4 Spike (4,65× Decode, 1,83× TTFT)](https://github.com/DaveX2001/deliverable-tracking/issues/1220)
- [#1222 — Native FP4 Tensor Core Validation](https://github.com/DaveX2001/deliverable-tracking/issues/1222)
- [#1205 — DGX Spark Ollama Benchmark (3 Modelle × 294 Seiten)](https://github.com/DaveX2001/deliverable-tracking/issues/1205)
- [#1196 — FSO Seminar Webseiten (David, wilsch-ai-site)](https://github.com/DaveX2001/deliverable-tracking/issues/1196)

**Externe Quellen:**
- [EXO Labs — DGX Spark + Mac Studio Disaggregated Inference](https://blog.exolabs.net/nvidia-dgx-spark/)
- [IBM Newsroom — Spyre Accelerator Announcement](https://newsroom.ibm.com/2025-10-07-ibm-introduces-the-spyre-accelerator-for-commercial-availability)
- [IBM Research — Spyre Architecture Deep-Dive](https://research.ibm.com/blog/lifting-the-cover-on-the-ibm-spyre-accelerator)
- [Hardware-Strategie Kaufentscheidung (Drive)](https://docs.google.com/document/d/1ex9bruT-KPZHsEegVJYr0xf2mSOQ2ngmRlt4UdPGCjw/edit)

### Ursprüngliche Quellen

- [Qwen 3.5 9B schlägt GPT-OSS 120B — VentureBeat](https://venturebeat.com/technology/alibabas-small-open-source-qwen3-5-9b-beats-openais-gpt-oss-120b-and-can-run)
- [NVIDIA DGX Spark Hardware-Spezifikationen](https://docs.nvidia.com/dgx/dgx-spark/hardware.html)
- [LMSYS DGX Spark In-Depth Review](https://lmsys.org/blog/2025-10-13-nvidia-dgx-spark/)
- [Apple M3 Ultra Announcement](https://www.apple.com/newsroom/2025/03/apple-reveals-m3-ultra-taking-apple-silicon-to-a-new-extreme/)
- [Apple M5 — Native FP4 via GPU Neural Accelerators](https://www.apple.com/newsroom/2025/10/apple-unleashes-m5-the-next-big-leap-in-ai-performance-for-apple-silicon/)
- [Apple ML Research — LLMs mit MLX und M5 Neural Accelerators](https://machinelearning.apple.com/research/exploring-llms-mlx-m5)
- [Unabhängiger M5 Neural Accelerator Benchmark](https://tzakharko.github.io/apple-neural-accelerators-benchmark/)
- [Apple Silicon vs NVIDIA CUDA: AI Comparison 2025](https://scalastic.io/en/apple-silicon-vs-nvidia-cuda-ai-2025/) — bestätigt: M3 hat kein natives FP4/FP8
- [IBM POWER10 Architektur — NextPlatform](https://www.nextplatform.com/2020/09/03/the-memory-area-network-at-the-heart-of-ibms-power10/)
- [IBM Power11 Announcement — NextPlatform](https://www.nextplatform.com/compute/2025/07/16/the-worlds-most-powerful-server-embiggens-a-bit-with-power11/)
- [MLX FP4/FP8 Upcast-Verhalten — GitHub Issue #2962](https://github.com/ml-explore/mlx/issues/2962)
- [MLX nvfp4/mxfp8 Quantize/Dequantize — PR #2688](https://github.com/ml-explore/mlx/pull/2688)
- [Power 10 Decision Boundary Chart](https://github.com/MariusWilsch/REKERS__poc/blob/main/.claude/tracking/issue-1132/power10_territory.html)
- [#1132 — Power 10 Extraction Runs (3 Modelle × 240 Seiten)](https://github.com/DaveX2001/deliverable-tracking/issues/1132)
- [#929 — DGX Spark Research & Hardware-Strategie](https://github.com/DaveX2001/deliverable-tracking/issues/929)
- [#1081 — Commercial Feasibility Artifact](https://github.com/DaveX2001/deliverable-tracking/issues/1081)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

