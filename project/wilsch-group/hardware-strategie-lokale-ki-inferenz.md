---
publish: true
---

# Hardware-Strategie: Lokale KI-Inferenz — Kaufentscheidung
[[client-wilsch-group]]

Kaufentscheidung für Hardware-Infrastruktur zur lokalen KI-Inferenz. Zwei Phasen: DGX Spark (sofort) und Mac Studio (strategisch). Zielgruppe: Thomas, Marius, Ulrich.

---

## Problem Statement

Wilsch AI hat derzeit keine Hardware für lokale KI-Inferenz. Die einzige verfügbare Maschine ist ein MacBook Air M2 mit 16 GB RAM — ungeeignet für jegliche Modellausführung.

Dadurch sind zwei aktive Kundenprojekte (AVO, REKERS) blockiert: KI-Entwicklung und lokales Deployment erfordern Modelle, die lokal auf leistungsfähiger Hardware laufen. Gleichzeitig muss für die FSO am 27. März eine funktionsfähige GPU-Infrastruktur demonstriert werden.

**Zwei Einsatzzwecke:**
1. **Entwicklung:** Agentic Coding mit großen Modellen (MiniMax M2.5, 230B Parameter) — ersetzt Cloud-basierte Tools wie Claude Code
2. **Deployment/Proof:** GPU-Infrastruktur für lokale Modellausführung — kleine Modelle (Qwen 3.5 4B/9B, Qwen 3 8B VL) als Benchmark gegen IBM Power 10 laufen lassen und auf der FSO demonstrieren

**Voraussetzungen:**
- MacBook Air M2 16GB kann keine sinnvolle KI-Inferenz ausführen
- Kundenprojekte erfordern lokale Modellausführung (Datensouveränität)
- UWI hat Budget für Hardware-Kauf freigegeben
- DGX Spark über Shopee SG/MY mit Next-Day Delivery verfügbar

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Hardware-Infrastruktur beschaffen, die lokale KI-Entwicklung (Agentic Coding) und Deployment (Kundenworkloads) ermöglicht |
| **Success** | Thomas und Ulrich genehmigen Phase 1 (DGX Spark Kauf). Funktionsfähige GPU-Demo auf FSO am 27. März. |
| **Done test** | „Können wir lokal ein Modell starten und eine REKERS-Extraktion durchführen?" → Wenn JA → Kaufentscheidung war richtig |

## Approach

### Glossar

**Prefill** — Das Modell liest die gesamte Eingabe in einem einzigen parallelen Schritt. Alle Tokens werden gleichzeitig verarbeitet — je mehr Rechenleistung (TFLOPS), desto schneller. Ergebnis: der interne Zustand (KV-Cache) ist aufgebaut, das Modell ist bereit zu antworten. Gemessen als **TTFT** (Time to First Token).

**Decode** — Das Modell generiert die Antwort Token für Token, sequentiell. Jedes neue Wort hängt von allen vorherigen ab (autoregressive Generierung). Pro Token wird das gesamte Modell einmal aus dem Speicher gelesen — deshalb zählt hier **Speicherbandbreite** (GB/s), nicht Rechenleistung. Gemessen als **TPS** (Tokens per Second).

**Warum diese Unterscheidung wichtig ist:** DGX Spark hat hohe Rechenleistung (100 TFLOPS) aber niedrige Bandbreite (273 GB/s) — ideal für Prefill. Mac Studio hat niedrige Rechenleistung aber hohe Bandbreite (M3 Ultra: 819 GB/s, M5 Ultra projiziert: ~1,1–1,2 TB/s) — ideal für Decode. Kombiniert: jede Maschine macht, was sie am besten kann.

### Phase 1: 2× NVIDIA DGX Spark (Sofortkauf)

**Hardware:**

| Eigenschaft | Pro Gerät | 2× Gesamt |
|-------------|-----------|-----------|
| Chip | GB10 (Grace Blackwell) | — |
| RAM | 128 GB | 256 GB |
| Rechenleistung | ~100 TFLOPS (FP16) | ~200 TFLOPS |
| Speicherbandbreite | 273 GB/s | — |
| Preis | ~€3.300–3.600 | **~€6.600–7.200** |
| Verfügbarkeit | Shopee SG/MY, Next-Day Delivery | — |

**Warum DGX Spark:** Der GB10-Chip (Grace Blackwell) liefert ~100 TFLOPS Rechenleistung — ideal für Prefill, die rechenintensivste Phase der KI-Inferenz. Beim Agentic Coding (KI-gestützte Softwareentwicklung) dominiert Prefill die Arbeitsschleife (~60/40 gegenüber Decode), da das Modell bei jedem Schritt den gesamten Projektkontext neu liest. 256 GB Gesamt-RAM ermöglicht auch große Entwicklungsmodelle (230B Parameter).

Technische Details zu Quantisierung, Serving Engines und SM121-Kompatibilität: siehe [#929 Research Comment](https://github.com/DaveX2001/deliverable-tracking/issues/929#issuecomment-4053944363).

### Phase 2: + Mac Studio — Disaggregated Inference (Strategisch)

**Das Prinzip:** Prefill und Decode auf getrennten Maschinen ausführen. DGX Spark übernimmt Prefill (100 TFLOPS), Mac Studio übernimmt Decode (819 GB/s). Der KV-Cache wird Layer-für-Layer zwischen den Geräten gestreamt.

**Benchmark (Exo Labs — Llama-3.1 8B, 8K Kontext):**

| Konfiguration | Prefill | Decode | Gesamt | Speedup |
|---------------|---------|--------|--------|---------|
| DGX Spark allein | 1,47s | 2,87s | 4,34s | 1,9× |
| M3 Ultra allein | 5,57s | 0,85s | 6,42s | 1,0× (Baseline) |
| **DGX Spark + M3 Ultra** | **1,47s** | **0,85s** | **2,32s** | **2,8×** |

Quelle: [Exo Labs Blog](https://blog.exolabs.net/nvidia-dgx-spark/)

**Spyre-Vergleich — Warum diese Kombination IBM Spyre herausfordert:**

| Eigenschaft | 8× IBM Spyre | M5 Ultra (projiziert) | Apple-Vorteil |
|-------------|-------------|----------------------|---------------|
| Speicher | 1 TB | ~192–512 GB | — |
| Bandbreite | 1,6 TB/s | ~1,1–1,2 TB/s | ~75% der Spyre-Bandbreite |
| Leistungsaufnahme | 600W | ~190W | **3× weniger Strom** |
| Bandbreite/Watt | 2,7 GB/s/W | ~6 GB/s/W | **2,2× effizienter** |
| **Systempreis** | **€100.000–150.000** | **~€15.000–20.000** | **~7–10× günstiger** |

**Undefined:** IBM Spyre-Spezifikationen müssen aus IBM-Primärquelle verifiziert werden. → Meeting Agenda: Spyre-Datenverifikation

**Undefined:** Welcher Mac (M3 Ultra jetzt, M5 Max, oder auf M5 Ultra warten) — Entscheidung abhängig von Verfügbarkeit und Preis. → Meeting Agenda: Mac-Auswahl

### Verbindung zu REKERS (#1079)

Diese Hardware-Infrastruktur ermöglicht Workloads wie die REKERS-Kriterienextraktion (#1079): kleine Modelle (Qwen 4B–9B) lokal auf DGX Spark und IBM Power 10 ausführen, bei Qualitätsparität mit Frontier-Modellen. Der DGX Spark dient dabei als Benchmark-Plattform neben Power 10 — gleicher Workload, verschiedene Hardware, messbare Vergleiche.

---

## Source

- **Issue:** [#929 — Rent Mac Studio Ultra 256/512GB RAM](https://github.com/DaveX2001/deliverable-tracking/issues/929)
- **Bestehende Recherche:** [Mac Studio Beschaffung — Handlungsoptionen](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/wilsch-group/mac-studio-beschaffung-recherche-2026-02)
- **REKERS Feasibility:** [Part 7 — POC Validation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/rekers/design-doc-feasibility-report)
- **Exo Labs:** [DGX Spark + Mac Studio Blog](https://blog.exolabs.net/nvidia-dgx-spark/)
- **Spark Arena:** [Benchmark Leaderboard](https://spark-arena.com/leaderboard)
- **NVIDIA Precision:** [TensorRT-LLM DeepSeek-R1 Blog](https://nvidia.github.io/TensorRT-LLM/blogs/tech_blog/blog1_Pushing_Latency_Boundaries_Optimizing_DeepSeek-R1_Performance_on_NVIDIA_B200_GPUs.html)
- **Session:** 69f54bd9-6bd4-40dd-9cca-07326fa49ce8
