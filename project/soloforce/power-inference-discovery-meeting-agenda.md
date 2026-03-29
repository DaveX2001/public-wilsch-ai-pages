---
publish: true
---

# IBM Power AI Inference - Discovery Meeting

[[wilsch-ai-internal]]

**Attendees:** Marius Wilsch, Nwyin

**Format:** ~60 minutes, informal discussion

**Context:** First meeting to explore collaboration on IBM Power AI inference optimization

---

## The Opportunity

Nobody is optimizing AI inference for IBM Power. The market is 100% focused on Nvidia GPUs and x86. This is a greenfield architecture — a chance to make a name on a processor architecture that's hungry for AI.

We have two clients who want AI deployed on their Power 10 infrastructure, a datacenter with hardware available for development, and AI applications in active development (initial results in 2-3 weeks, multi-month engagements). What we need is fast inference on Power.

**What we've measured so far** (not optimized — just what we've gotten to):

- ~55 prompt eval tok/s on Power 10 (5 CPUs)
- TTFT and TPS both need improvement
- For reference: x86 does 100-300 tok/s, Apple Silicon 200-400+ for comparable models

**The challenge:** Find out what the most we can get out of Power 10 with the most CPUs available (TBD with Thomas). Then 10x that in one month.

---

## The Objective

1. **Baseline**: Install and benchmark existing tools on our Power 10. Measure TTFT + TPS for **Qwen3 8B**.

2. **10x**: Achieve 10x improvement over that baseline within 30 days.

**Target**: Qwen3 8B on Power 10. Power 10 is what we have right now — we start there.

**Why Power 10 first**: If we prove results on what's available today, it becomes much easier to convince the datacenter to invest in more hardware (Power 11, Spyre accelerators, more CPUs). Results on Power 10 → stronger case for upgrades → even better results. Self-reinforcing loop.

**Measured in**: TTFT (Time to First Token) and TPS (Tokens Per Second).

---

## Inspiration — What Already Exists on Other Architectures

These aren't a roadmap. They're proof that fast inference on non-GPU hardware is possible. If it works on x86/ARM, we should be able to make it work on Power.

| Technology | What It Does | What It Proves |
|-----------|-------------|----------------|
| [BitNet.cpp](https://github.com/microsoft/BitNet) | 1-bit quantization, CPU-optimized. 2-6x speedup on x86/ARM | Fast LLM inference on CPUs is solved elsewhere |
| [vllm-spyre](https://github.com/vllm-project/vllm-spyre) | vLLM plugin for IBM Spyre accelerators | IBM is investing in AI acceleration for Power |
| [MIT Recursive LM](https://arxiv.org/html/2512.24601v1) | Processes 10M+ tokens via selective context access | Massive data volumes are solvable at the architecture level |
| [Ollama on ppc64le](https://www.linkedin.com/pulse/compiling-ollama-openwebui-ibm-power-ppc64le-andre-lutz) | Already compiled for Power (Andre Lutz, SVA) | The basics already run — now we need to make them fast |

---

## The Team

This is an exploratory collaboration, not a job description. Here's who's involved:

**Nwyin** — Inference optimization. You own how AI runs fast on Power. The benchmark is your first move. Where you take it from there is yours to define — that's the point. Make a name for yourself on this architecture.

**Marius** — Application + data architecture. I build what the clients use (RAG, similarity search, text-to-SQL). I handle the client relationships and delivery.

**Thomas Erhard** — Infrastructure. Power hardware, LPAR setup, datacenter access. He defines what CPUs and resources are available.

How we collaborate day-to-day: TBD — figure that out in the meeting.

---

## Client Context

Two clients, both on IBM Power 10, both with massive structured datasets:

**Client A** — Food manufacturer. ~8,000 recipes in AS/400 database. AI finds duplicate and similar recipes based on ingredient composition, percentages, and resource categories. POC starts with 100 recipes, then scales. Production hardware: 8 CPU cores, 512GB RAM.

**Client B** — Construction / precast concrete. 30 years of project history on IBM i. AI matches new customer requests to historical projects for cost estimation. Multiple input formats (PDFs, CAD files, structured data). Similarity matching with 3-10% variance tolerance.

**Current process**: Both clients work in **days** for these tasks. If we deliver in minutes or hours, we're already faster. Interactive speed (seconds) is a bonus, not the minimum bar.

**Database + AI**: Both have decades of structured data. Making AI confident over SQL is future scope — context for now, not immediate.

---

## Discussion Topics

1. **Your take** — What's your initial read on Power's inference limitations? Where would you start?
2. **The benchmark** — Here's what I propose: you own the baseline. Install, measure, report on our Power 10. Then we 10x it. What do you think?
3. **30-day plan** — What would you need to make this happen? Hardware access, documentation, support from Thomas?
4. **Compensation** — How do you want to get paid? Upfront per deliverable? Monthly retainer? Revenue share from client deployments? Which do you prefer and why?
5. **Working style** — How do you want to collaborate day-to-day?

---

## Outcome

Walk out of this meeting with:

1. A shared direction we're both committed to
2. A concrete 30-day plan with the first action defined
3. A fair compensation structure
4. Next action — who does what by when

---

## Source

- Seminar transcript: [AI4I Seminar Feb 6, 2026](https://app.fireflies.ai/view/AI4I-Seminar-06-02-um-13UHR::01KGS021VPV39W9J7XTC5A6PM9)
- Andre Lutz Power 11 benchmark: [LinkedIn Post](https://www.linkedin.com/pulse/compiling-ollama-openwebui-ibm-power-ppc64le-andre-lutz)
- BitNet: [github.com/microsoft/BitNet](https://github.com/microsoft/BitNet)
- vllm-spyre: [github.com/vllm-project/vllm-spyre](https://github.com/vllm-project/vllm-spyre)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

