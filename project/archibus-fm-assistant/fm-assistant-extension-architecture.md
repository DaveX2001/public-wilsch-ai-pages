---
publish: true
date: 2025-12-04
---

# FM Assistant Extension Architecture
[[client-archibus]]

Technical architecture for extending the FM Assistant POC beyond ASSETS to all Bruce Platform element groups.

## North Star: Hierarchical Routing

The core pattern for scaling FM Assistant:

```
1. Classify GROUP  →  Assets, Contracts, Works, Costs...
2. Find ELEMENT    →  The specific asset/contract/work
3. Determine ACTION →  Smart Insight (read) vs FM Assistant (write)
```

- Current POC implements this implicitly for ASSETS only
- Extension = make GROUP classification explicit + apply pattern to all groups

## Element Groups (Bruce Platform)

From the [Bruce Platform elements diagram](bruce-elements-diagram.png):

| Group | Elements | Current Support |
|-------|----------|-----------------|
| **ASSETS** | Places, Equipment, Systems, PM Groups | ✓ search_assets |
| **CONTRACTS** | SLAs, Service Contracts | - |
| **WORKS** | On-Demand, Planned, Committed, Tasks, Projects | ✓ create_work_request |
| **COSTS & RESOURCES** | Workforce, Budget, Meters, Invoices | - |
| **NOTIFICATIONS** | Rules, Workflows | - |
| **DOCUMENTS** | Document repository | - |

## Action Types

| Action Type | Purpose | Example |
|-------------|---------|---------|
| **Smart Insight** | Query/read data | "Find elevators in Building A" |
| **FM Assistant** | Create/modify data | "Create work request for broken AC" |

## Orchestration Model Options

### NVIDIA Orchestrator-8B
- Base: Qwen3-8B (same 8B parameter count as current baseline)
- Purpose: Tool orchestration - coordinates multiple tools and LLMs
- Trained via GRPO for accuracy, latency, cost optimization
- Reference: [nvidia/Orchestrator-8B](https://huggingface.co/nvidia/Orchestrator-8B)

### Parlant Behavioral Guidelines (Concept)
- Explicit condition → action rules instead of hoping LLM follows prompts
- Journeys for multi-step flows (find element → confirm → execute)
- Explainability for routing decisions
- Reference: [emcie-co/parlant](https://github.com/emcie-co/parlant)

## Architecture Stack

```
┌─────────────────────────────────────────┐
│           User (LibreChat)              │
└─────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────┐
│        ORCHESTRATION LAYER              │
│  Model: Orchestrator-8B or similar      │
│  Pattern: GROUP → ELEMENT → ACTION      │
└─────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────┐
│            MCP TOOLS                    │
│  • Element retrieval (search per group) │
│  • Actions (read / write per group)     │
└─────────────────────────────────────────┘
                    │
                    ▼
┌─────────────────────────────────────────┐
│          Bruce BEM API                  │
└─────────────────────────────────────────┘
```

## Key Design Decisions

1. **Element scope**: An element belongs to exactly ONE group. Multi-group queries are sequences, not parallel operations.

2. **Tool architecture**: Start with specific tools per group (search_assets, search_contracts), expand as needed.

3. **Result filtering**: API-side filtering first. Reranking after query is a future optimization.

4. **Feature gating**: Let Bruce BEM API enforce permissions rather than duplicating auth logic in MCP layer.

## Future Optimizations

| Optimization | When to Consider |
|--------------|------------------|
| **Rerank after API query** | When relevance is poor despite filtering |
| **Programmatic Tool Calling** | When result sets are large (filter in code) |
| **Tool Search Tool** | When tool count exceeds 10+ |

## References

- [Anthropic Advanced Tool Use](https://www.anthropic.com/engineering/advanced-tool-use) - Tool Search, PTC, Examples
- [Bruce Platform](https://bruceplatform.com/) - e-Collaboration platform overview
- [BruceBEM](https://brucebem.com/) - Built Environment Manager application

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

