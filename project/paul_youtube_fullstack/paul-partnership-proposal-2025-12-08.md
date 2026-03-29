---
publish: true
---

# Paul Partnership Proposal
[[client-paul]]

## 1. Current State

Paul's system today:
- 3 frontends, 2 backends, single-tenant architecture
- Quality inconsistent, codebase messy
- Works for Paul's channels, not scalable for external clients

## 2. Target State

**Phase 1 - Baseline Validation**
- Deploy Paul's original tar on my GPU box
- Paul tests (prove his code works on different infra)

**Phase 2 - TTS Integration**
- Replace TTS + Whisper with parallel TTS
- Paul tests again (validate cost, availability, reliability)
- Eric onboard as first client

**Phase 3 - Scale & Multi-tenant (Dec 15-31)**
- Parallelize entire pipeline (image prompts, image gen, video gen)
- Consolidate to 1 frontend, 1 backend
- Proper database with multi-tenancy
- Authentication and data separation
- Containerization for staging/prod/dev per client

## 3. Architecture

| Layer | Runs on | Scales by |
|-------|---------|-----------|
| Orchestration | CPU box | Adding CPU boxes (cheap) |
| GPU workloads | RunPod serverless | Workers per endpoint |

## 4. Team

| Person | Role |
|--------|------|
| Marius | Technical lead - infra, scaling, architecture |
| Roland | Client-facing - sales, onboarding, support |
| Tech support | Ramping up |

## 5. Proposal Terms

**60/40 split** on net profit
- I pay infrastructure costs upfront
- Deduct infra from gross revenue, split remainder 60/40
- Example: $1000 revenue - $200 infra = $800 → Paul $480, Marius $320

**Quarterly reevaluation**
- Mutual protection clause
- Revisit split if contributions change (workload, sales, codebase ownership)
- Neither side locked into terms that no longer reflect reality

**Lifetime platform access**
- Enables running our own YouTube channels without subscription fees

**Roland mentorship from Paul**
- Calls to transfer YouTube strategy knowledge to my team
- Evaluated agenda quarterly (continue/reduce based on progress)
- My team takes client-facing work off Paul's team in exchange

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

