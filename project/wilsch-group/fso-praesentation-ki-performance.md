---
publish: true
---

# KI-Performance: Hardware-Vergleich für lokale Inferenz
[[client-wilsch-group]]

Präsentationsmaterial für das FSO-Seminar am 27. März 2026. Erklärt die leistungsbestimmenden Faktoren für KI und vergleicht vier Testsysteme anhand gemessener und geschätzter Werte.

---

## Folie 1: Performance-bestimmende Faktoren für KI

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

| | Modell laden (FP04) | Berechnung durchführen |
|--|--|--|
| **DGX Spark (Blackwell)** | ✅ 4 GB | ✅ **Nativ bei FP04** — 1.000 TFLOPS |
| **Mac Studio (M3 Ultra)** | ✅ 4 GB | ⬆️ Umwandlung zu FP16 — 65 TFLOPS |
| **IBM Power 10** | ✅ 4 GB | ⬆️ Umwandlung zu BF16 — 2 TFLOPS |

> **Alle sparen Speicher.** Aber nur NVIDIA kann bei niedrigerer Präzision auch **schneller rechnen**. Apple und IBM müssen die Zahlen intern wieder vergrößern (Upcast), bevor sie rechnen können — kein Geschwindigkeitsvorteil bei der Berechnung.
>
> Das ist der eigentliche Vorteil der NVIDIA Tensor Cores: nicht nur weniger Speicher, sondern auch **15x schnellere Berechnung** bei FP04 im Vergleich zu FP16.
>
> **Wann ist das relevant?** Bei **Prefill** (Einlesen) — dort ist die Rechenleistung der Engpass. Bei Decode (Auslesen) ist die Speicherbandbreite der Engpass, und die Rechenleistung reicht auf allen Systemen aus. Für Anwendungen mit hohem Einlese-Anteil (z.B. Dokumentenverarbeitung, PDF-Analyse) ist der Unterschied zwischen nativer FP04-Berechnung und Upcast daher **entscheidend**.

---

## Folie 2: Performance-Werte angewendet auf unsere Testsysteme

### Hardware + OS

| | 1x IBM Power 10 | 1x IBM Power 11 | 2x NVIDIA DGX Spark | Apple Mac Studio M3 Ultra | Apple Mac Studio M5 Ultra (projiziert)¹ |
|--|--|--|--|--|--|
| **Kerne** | 5 CPU | 15 CPU | 2x 6.144 GPU | 80 GPU | 80 GPU + Neural Accelerators |
| **Kerne (Art)** | POWER10 (VSX + MMA) | POWER11 (VSX + MMA) | Blackwell (CUDA + Tensor) | Apple GPU | Apple GPU + Neural Acc. |
| **Rechenleistung (FP16)** | ~2 TFLOPS | ~8 TFLOPS | 2x 125 = **250 TFLOPS** | 65,5 TFLOPS | ~66 TFLOPS |
| **Rechenleistung (FP04)** | — | — | 2x 500 = **1.000 TFLOPS** | — (Upcast zu FP16) | ✅ Nativ (TBD TFLOPS) |
| **RAM** | 128 GB DDR4 | 1.024 GB DDR5 | 2x 128 = 256 GB LPDDR5x | 256 GB LPDDR5x | bis 512 GB LPDDR5x |
| **RAM Bandbreite (theoretisch)** | ~400 GB/s | ~1.200 GB/s | 2x 273 = 546 GB/s | 819 GB/s | ~1.228 GB/s |
| **Effektive Bandbreite** | **137 GB/s** (gemessen, ~34%) | TBD | ~460 GB/s (~85%) | ~700 GB/s (~85%) | ~1.044 GB/s (~85%) |
| **Speicher (SSD/HDD)** | 500 GB SAS | TBD | 2x 4 TB NVMe | 1–8 TB NVMe | 1–8 TB NVMe |
| **Betriebssystem** | AlmaLinux 10.0 | TBD | Ubuntu / DGX OS | macOS | macOS |
| **Architektur** | ppc64le | ppc64le | ARM (aarch64) | ARM (aarch64) | ARM (aarch64) |
| **Stückzahl** | 1 (vorhanden) | 1 (verfügbar) | 2 (geplant) | 1 (Referenz) | — (erwartet ~Mitte 2026) |
| **Preis** | ~50.000–100.000 € | ~50.000–100.000 € | 2x ~3.700 € = **~7.400 €** | ~4.000–10.000 € | ~5.000–12.000 € (geschätzt) |

> ¹ **M5 Ultra (projiziert):** Basierend auf 2x M5 Max Spezifikationen — gleiches Prinzip wie M3 Ultra = 2x M3 Max. Apple M5-Generation hat mit den „GPU Neural Accelerators" erstmals native FP04-Berechnung in Hardware. Kombination aus nativer FP04-Berechnung + ~1.228 GB/s Bandbreite könnte den DGX Spark in beiden Dimensionen (Prefill und Decode) herausfordern. Erwartet ~Mitte 2026 (WWDC).

### Benchmark-Ergebnisse (PDF-Dokumentenverarbeitung, Qwen 3-VL 8B)

| | IBM Power 10 | IBM Power 11 | 2x DGX Spark | Mac Studio M3 Ultra |
|--|--|--|--|--|
| **TTFT (Prefill)** | ~90 s/Seite | TBD | **<1 s/Seite** | TBD |
| **TPS (Decode)** | 12,4 tok/s | TBD | **40,2 tok/s** | TBD |
| **240 Seiten** | ~7 Stunden | TBD | **~21 Minuten** | TBD |
| **Qualität** | **9/9** Kriterien | TBD | **9/9** Kriterien | TBD |

> **Hinweis:** Power 10 und DGX Spark Werte sind gemessen (Qwen 3-VL 8B, Projekt 35764, 72 DPI). Power 10: 240 Seiten. DGX Spark: 294 Seiten (vollständiger Manifest), gemessen auf 1x Spark. Qualität = 9/9 auf beiden Plattformen. TTFT inkl. Vision Encoder — auf GPU <1s vs. CPU ~90s (~100×). Werte für Power 11 und Mac Studio stehen aus.

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

| Phase | Was passiert | Power 10 (CPU) | DGX Spark (GPU) | Speedup |
|-------|-------------|----------------|-----------------|---------|
| **Bilderkennung (Vision Encoder)** | Modell „sieht" die Seite — wandelt Pixel in Zahlen um | ~40 Sekunden | **~0,4 Sekunden** | ~91× |
| **Prefill** | Sprachmodell liest die umgewandelten Daten ein | ~36 Sekunden | **~0,3 Sekunden** | ~120× |
| **Decode** | Modell generiert strukturierte Antwort | ~14 Sekunden | **~4,6 Sekunden** | ~3× |
| **Gesamt** | | **~90 Sekunden** | **~5,4 Sekunden** | **~17×** |

> **Kernproblem gelöst:** Die Bilderkennung (Vision Encoder) war auf der CPU mit ~40 Sekunden der Engpass (~45% der Gesamtzeit). Auf der DGX Spark GPU: **0,4 Sekunden** — eine Beschleunigung um den Faktor 91. Zusätzlich ist auch der Prefill ~120× schneller. Nur Decode zeigt moderaten Speedup (~3×), da dort die Speicherbandbreite der Engpass ist, nicht die Rechenleistung.

### Hochrechnung: 14 Projekte

| | IBM Power 10 | 2x DGX Spark |
|--|--|--|
| **1 Projekt (240 Seiten)** | ~7 Stunden | **~21 Minuten** |
| **14 Projekte** | ~4 Tage | **~2,5 Stunden** |

> **Hinweis:** Die 14 Projekte haben unterschiedliche Seitenanzahlen. Die Hochrechnung basiert auf gemessenen 672 Seiten/Stunde (1x DGX Spark, Qwen 3-VL 8B). Bei 2x DGX Spark: jeweils 7 Projekte pro Spark (trivial parallelisierbar). Power 10: ~33 Seiten/Stunde → **~20× langsamer** als DGX Spark.
>
> **Wichtig:** Diese Benchmark testet nur die Extraktion — den ersten von zwei Schritten. Der zweite Schritt (Ähnlichkeitsanalyse / Kriteriennachweis-Generierung) wird separat benchmarked, sobald die Page-Index-Infrastruktur bereit ist.

---

## Quellen

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

