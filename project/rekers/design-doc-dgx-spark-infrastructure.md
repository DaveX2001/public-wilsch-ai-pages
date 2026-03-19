---
publish: true
---

# DGX Spark Infrastructure Platform
[[client-08_rekers]]

Design doc for setting up two DGX Spark units as a model-agnostic inference platform serving REKERS benchmarks and AVO MiniMax workloads.

---

## Problem Statement

Two DGX Spark units (GB10 Blackwell, 128 GB unified memory each) need to become a model-agnostic inference platform serving multiple client workloads — REKERS extraction benchmarks (qwen3-vl:8b, FSO seminar deadline March 27) and AVO MiniMax M2.5 deployment (future, undefined scope). The platform must support any model via a single serving container, scale from single-node to dual-node when interconnect hardware arrives, and remain accessible from any network.

This is NOT about the extraction pipeline logic, model architecture selection, or client-specific application code. This is infrastructure: how the Sparks serve models, how they're reached, and how they scale.

**Preconditions:**
- Two DGX Spark units acquired (NVIDIA reference design, GB10 Blackwell, 128 GB LPDDR5x each)
- SSH access established via Tailscale (IP 100.85.150.22, persistent across networks)
- CUDA 13.0, Docker 29.1.3, Ollama 0.18.0 pre-installed on Spark 1
- Spark 2 in partial setup state (OS installed, not fully configured)
- QSFP112 inter-node cable not yet purchased (seller identified)
- Memory bandwidth (273 GB/s LPDDR5x) is the performance ceiling — not compute or VRAM

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | Any model loads and serves an OpenAI-compatible API on the DGX Spark, reachable from any network, with a clear path from single-node to dual-node scaling. |
| **Success** | (1) Benchmark comparison table produced — same model, same pipeline, DGX Spark vs Power 10. (2) A second, unrelated model runs on the same infrastructure without reconfiguration. (3) Dual-node expansion path documented and hardware identified. |
| **Done test** | Can I swap a model path in one command and serve a different model to a remote client? If YES → infrastructure is complete. |

---

## Approach

### Part 1 — Hardware & Connectivity

**Spark 1 (gx10-017d) — verified via SSH 2026-03-19:**

| Component | Spec |
|-----------|------|
| Hostname | gx10-017d |
| GPU | NVIDIA GB10 (Blackwell SM121, Device 2e12) |
| Memory | 128 GB unified LPDDR5x (119 GB usable), 273 GB/s bandwidth |
| Disk | 1.8 TB NVMe |
| CPU | 20 cores: 10× Cortex-X925 + 10× Cortex-A725 (ARM big.LITTLE, aarch64), 1 thread/core |
| CUDA | 13.0 (`/usr/local/cuda-13.0`), Driver 580.126.09 |
| OS | Ubuntu 24.04.4 LTS (Noble Numbat), Kernel 6.17.0-1008-nvidia |
| Docker | 29.1.3 |
| Ollama | 0.18.0 |
| Tailscale | 1.96.2 |
| NICs | 2× Mellanox ConnectX-7 (4 ports total across 2 PCIe buses: 0001 + 0002) |

**Remote access:** Tailscale (IP `100.85.150.22`, `ssh DGX-SPARK`). Persistent across network changes. No VPN or local network dependency. Fallback: TP-Link Ethernet bridge at `192.168.1.2`.

**Network interfaces:**
- `enP7s7` — Ethernet (TP-Link fallback)
- `enp1s0f0np0` / `enp1s0f1np1` — ConnectX-7 bus 0001 (inter-node QSFP, future)
- `enP2p1s0f0np0` / `enP2p1s0f1np1` — ConnectX-7 bus 0002 (second NIC, 2 additional ports)
- `wlP9s9` — WiFi (primary internet)

**Inter-node (future):** QSFP56 200G DAC or QSFP112 400G — both work on ConnectX-7. Direct point-to-point, no switch needed for 2 nodes. Creates 256 GB unified pool. 4-node supported since March 2026 (requires RoCE switch).

**Spark 2:** OS installed, partially configured. Same setup procedure: Ethernet to TP-Link, install Tailscale, authenticate, add SSH config.

### Part 2 — Serving Engine Strategy

The GB10's bottleneck is memory bandwidth (273 GB/s), not compute (1 PFLOP FP4 theoretical). Model weights must flow from memory to GPU cores every inference step. The serving engine choice determines how efficiently this happens — and whether the FP4 tensor cores get used at all.

**Engine ladder (simplest → maximum utilization):**

| Engine | When to use | Quantization | Docker image |
|--------|------------|--------------|-------------|
| **Ollama** | Quick validation, apples-to-apples benchmarks vs Power 10 | Q4_K_M, Q8_0, MXFP4 (MoE only) | Pre-installed, no container needed |
| **eugr/spark-vllm-docker** | Primary platform — any model, single or dual node, vision support | FP8, AWQ, GGUF | `eugr/spark-vllm-docker` (updated daily, Spark-optimized) |
| **SGLang** | Maximum single-node speed — NVFP4 + EAGLE3 speculative decoding | NVFP4, MXFP4, FP8 | `lmsysorg/sglang:spark` or `scitrera/dgx-spark-sglang:0.5.9-t5` |
| **TensorRT-LLM** | Production — NVIDIA's own kernel optimizations, pre-quantized NVFP4 models | NVFP4, FP8 | `nvcr.io/nvidia/tensorrt-llm/release:1.2.0rc6` |

**eugr/spark-vllm-docker** is the recommended platform container because:
1. Model-agnostic — swap model path in one command (`vllm serve <model>`)
2. DGX Spark-specific optimizations (fastsafetensors, unified memory management)
3. Dual-node ready via Ray (when cable arrives)
4. Vision models tested (Qwen3-VL is the primary README demo)
5. Updated daily, 618 stars, active community

**Quantization pairing:**

| Model type | Best quantization | Why |
|-----------|------------------|-----|
| Dense ≤32B (qwen3-vl:8b, qwen3.5:9b) | Q4_K_M (GGUF) | MXFP4/NVFP4 designed for MoE expert weights, actually degrades dense attention layers |
| MoE (MiniMax M2.5, Qwen3.5-397B, gpt-oss) | NVFP4 via SGLang/TRT-LLM | 90%+ of MoE parameters are expert weights — FP4 reduces bandwidth pressure 3.5× |

### Part 3 — Phased Deployment

**Phase 1 — FSO Benchmark (deadline: March 27, 2026)**

Run the same extraction pipeline from #1132 on the DGX Spark via Ollama. Produce the benchmark comparison table: Power 10 vs DGX Spark for qwen3-vl:8b at 72 DPI, 240 pages. Key metric: vision encoder time (40s/page on Power 10 CPU → expected <1s on DGX Spark GPU).

- Engine: Ollama (pre-installed, same as Power 10)
- Model: qwen3-vl:8b (already pulled)
- Risk: thermal throttling on extended 240-page run (monitor `nvidia-smi` temps)
- Output: fills TBD values in FSO presentation (#1081)

**Phase 2 — Optimized Baseline**

Install eugr/spark-vllm-docker as the permanent serving platform. Validate Qwen3-VL via vLLM, test EAGLE3 speculative decoding via SGLang. This produces "optimized" numbers alongside the Ollama baseline.

- Engine: eugr/spark-vllm-docker (single-node `--solo` mode)
- Test: same extraction, then a second unrelated model → validates "swap model path" capability
- Explore: SGLang + EAGLE3 for ~2× decode speed
- Dependency: none (can start immediately after Phase 1)

**Phase 3 — AVO MiniMax & Dual-Node**

Deploy MiniMax M2.5 for AVO workload (scope TBD). Start single-node with FP8+INT4-AWQ (~92.5 GB), expand to dual-node for larger quantizations or longer context.

- Engine: eugr/spark-vllm-docker (same container, `TP=2` via Ray for dual-node)
- Reference: [wshobson/minimax-dgx-spark](https://github.com/wshobson/minimax-dgx-spark) — dedicated MiniMax inference server
- Dependency: AVO scope definition, QSFP cable purchase (for dual-node)
- Known issue: vLLM TP=2 + Qwen3-VL-30B has engine init failure (active NVIDIA forum thread)

**Undefined:**
- **NSC Datacenter:** Timeline, power/cooling requirements, persistent networking at NSC. Tailscale handles connectivity regardless — placement logistics TBD.
- **Docker Workflow:** Container management, model storage persistence across reboots, restart policies.
- **Monitoring:** `ateska/dgx-spark-prometheus` for GPU temp, power, throughput metrics. Relevant for sustained benchmark runs and NSC production.

### Part 4 — Ecosystem Intelligence

**NVIDIA Official:**
- [build.nvidia.com/spark](https://build.nvidia.com/spark) — 39 playbooks (inference, fine-tuning, clustering, tools)
- [NVIDIA/dgx-spark-playbooks](https://github.com/NVIDIA/dgx-spark-playbooks) — GitHub repo with step-by-step guides (594 stars)
- [DGX Spark Performance FAQ](https://forums.developer.nvidia.com/t/dgx-spark-performance-faq/359456) — expected tok/s and config gotchas

**Community Containers:**
- [eugr/spark-vllm-docker](https://github.com/eugr/spark-vllm-docker) — vLLM for Spark, dual-node, updated daily (618 stars)
- [scitrera/sparkrun](https://github.com/scitrera/sparkrun) — launch/manage inference workloads (46 stars)
- [wshobson/minimax-dgx-spark](https://github.com/wshobson/minimax-dgx-spark) — MiniMax inference server (10 stars)

**Benchmarks & Optimization:**
- [Ollama Spark benchmarks](https://ollama.com/blog/nvidia-spark-performance) — official Ollama perf numbers
- [LMSYS In-Depth Review](https://lmsys.org/blog/2025-10-13-nvidia-dgx-spark/) — SGLang benchmarks, EAGLE3, thermal analysis
- [PrimitiveContext/blackwell](https://github.com/PrimitiveContext/blackwell) — 49 benchmarked models, custom CUTLASS kernels
- [CUDA 13.2 impact thread](https://forums.developer.nvidia.com/t/cuda-13-2-dgx-spark-impact/363182) — up to 3× improvement for NVFP4/MXFP8

**Guides:**
- [Level1Techs Deep Dive (YouTube)](https://www.youtube.com/watch?v=Lqd2EuJwOuw) — most comprehensive hands-on video
- [Multi-node clustering via 200G RoCE](https://medium.com/@dorangao/connecting-two-dgx-spark-systems-via-200gb-s-roce-network-for-multi-node-gpu-training-50d67d3630a5)
- [Choosing an Inference Engine on DGX Spark](https://medium.com/sparktastic/choosing-an-inference-engine-on-dgx-spark-8a312dfcaac6)

**Known Issues:**
- Thermal throttling on NVIDIA reference design during extended runs — Dell variant (280W, better cooling) preferred
- CES Jan 2026 software update = 2.5× faster than launch — pre-update benchmarks are obsolete
- Total memory allocation should stay under ~115 GB to avoid fragmentation crashes
- vLLM SM121 aarch64 bugs: no prebuilt wheel, Marlin MXFP4 garbage output, FlashInfer MoE crash

---

## Source

- **Session:** `e489c708-db82-48a3-821f-36faae983e8c`
- **Issue:** [#1080 — DGX Spark benchmark](https://github.com/DaveX2001/deliverable-tracking/issues/1080)
- **Issue comment (preliminary research):** 2026-03-19 by MariusWilsch — MiniMax M2.5 deployment paths, key resources
- **CLAUDE.md:** Updated with DGX-SPARK SSH config, hardware specs, Tailscale setup
