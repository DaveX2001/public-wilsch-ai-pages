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
> - **Rekers (Dokumentenverarbeitung):** 95% Prefill → Rechenleistung entscheidend
> - **Chatbot (Dialogsystem):** 80% Decode → Speicherbandbreite entscheidend

### Hardware-Kennzahlen

| Kennzahl | Bedeutung | Einheit |
|----------|-----------|---------|
| **Zahl der Kerne** | Wie viele Recheneinheiten parallel arbeiten | CPU-Kerne oder GPU-Kerne |
| **CPU vs GPU Kerne** | CPU = wenige, vielseitige Kerne; GPU = tausende, spezialisierte Kerne | — |
| **Rechenleistung** | Wie schnell Berechnungen durchgeführt werden (Prefill) | TFLOPS (Tera Floating Point Operations per Second) |
| **RAM Größe** | Wie viel Speicher verfügbar ist — bestimmt, welche Modelle geladen werden können | GB |
| **RAM Bandbreite** | Wie schnell Daten aus dem Speicher gelesen werden (Decode) | GB/s |

### Präzision über Quantifizierung

Modelle bestehen aus Milliarden von Zahlen mit Nachkommastellen. Je mehr Nachkommastellen, umso genauer — aber auch umso mehr Speicher wird benötigt.

| Präzision | Nachkommastellen | Speicher pro 8B-Modell | Genauigkeit |
|-----------|-----------------|----------------------|-------------|
| **FP32** | 32 Bit | 32 GB | Höchste Präzision |
| **FP16** | 16 Bit | 16 GB | Standardpräzision |
| **FP08** | 8 Bit | 8 GB | Leicht gerundet |
| **FP04** | 4 Bit | 4 GB | Stark gerundet — für die meisten Anwendungen ausreichend |

> **Halbe Präzision = halber Speicher = doppelte Geschwindigkeit.**
> Die Qualität der Ergebnisse bleibt bei FP04 für Textverarbeitung nahezu gleich.

---

## Folie 2: Performance-Werte angewendet auf unsere Testsysteme

### Hardware + OS

| | 1x IBM Power 10 | 1x IBM Power 11 | 2x NVIDIA DGX Spark | Apple Mac Studio M3 Ultra |
|--|--|--|--|--|
| **Kerne** | 5 CPU | 15 CPU | 2x 6.144 GPU | 80 GPU |
| **Kerne (Art)** | POWER10 (VSX + MMA) | POWER11 (VSX + MMA) | Blackwell (CUDA + Tensor) | Apple GPU |
| **Rechenleistung (FP16)** | ~2 TFLOPS | ~8 TFLOPS | 2x 125 = **250 TFLOPS** | 65,5 TFLOPS |
| **RAM** | 128 GB DDR4 | 1.024 GB DDR5 | 2x 128 = 256 GB LPDDR5x | 128 GB LPDDR5x |
| **RAM Bandbreite** | 137 GB/s (gemessen) | ~1.200 GB/s (Herstellerangabe) | 2x 273 = 546 GB/s | 819 GB/s |
| **Speicher (SSD/HDD)** | 500 GB SAS | TBD | 2x 4 TB NVMe | 1–8 TB NVMe |
| **Betriebssystem** | AlmaLinux 10.0 | TBD | Ubuntu / DGX OS | macOS |
| **Architektur** | ppc64le | ppc64le | ARM (aarch64) | ARM (aarch64) |
| **Stückzahl** | 1 (vorhanden) | 1 (verfügbar) | 2 (geplant) | 1 (Referenz) |
| **Preis** | ~50.000–100.000 € (Enterprise-Lease) | ~50.000–100.000 € | 2x ~3.700 € = **~7.400 €** | ~4.000–10.000 € |

### Benchmark-Ergebnisse (REKERS-Projekt, Qwen 3-VL 8B)

| | IBM Power 10 | IBM Power 11 | 2x DGX Spark | Mac Studio M3 Ultra |
|--|--|--|--|--|
| **TTFT (Prefill)** | ~90 s/Seite | TBD | TBD | TBD |
| **TPS (Decode)** | 12,4 tok/s | TBD | TBD | TBD |
| **240 Seiten** | ~6 Stunden | TBD | TBD | TBD |
| **Kriterien gefunden** | 9/9 (100%) | TBD | TBD | TBD |

> **Hinweis:** Power 10 Werte sind gemessen (Projekt 35764, 240 Seiten, Qwen 3-VL 8B). Werte für andere Systeme stehen aus — Benchmark wird nach Inbetriebnahme der DGX Spark nachgeholt.

### Warum der Geschwindigkeitsunterschied?

| Faktor | IBM Power 10 | DGX Spark / Mac Studio |
|--------|-------------|----------------------|
| **GPU vorhanden?** | Nein — reine CPU-Verarbeitung | Ja — dedizierte KI-Hardware |
| **Bandbreite nutzbar** | ~20–30% (CPU-Architektur) | ~85% (GPU-Architektur) |
| **Analogie** | LKW auf einer Landstraße | Rennwagen auf der Autobahn |

> Die IBM Power 10 ist eine Enterprise-Datenbank-Maschine, die KI als Nebenaufgabe ausführt. Die DGX Spark und Mac Studio wurden für KI-Aufgaben gebaut.

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

**Aktuell:** Qwen 3.5 9B (10 GB) ist so intelligent wie GPT-OS 120B (vor 12 Monaten)

> **Gleiche Intelligenz, viel weniger Hardware.**
> Das bedeutet: Die Hardware-Anforderungen für den Betrieb sinken kontinuierlich.

### Auswirkungen auf Hardware

| System | Kleine Modelle (< 9B) | Große Modelle (> 9B) |
|--------|----------------------|---------------------|
| IBM Power 10 (128 GB) | ✅ Möglich | ⚠️ Möglich, aber sehr langsam |
| IBM Power 11 (1.024 GB) | ✅ Möglich | ✅ Möglich (z.B. MiniMax 230B bei FP08) |
| 2x DGX Spark (256 GB) | ✅ Möglich | ✅ Möglich (z.B. MiniMax 230B bei FP04) |
| Mac Studio M3 Ultra (128 GB) | ✅ Möglich | ⚠️ Nur bis ~70B bei FP16 |

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
