---
publish: true
---

# AWS GPU Elimination Summary

[[client-archibus]]

## Decision

**AWS is not a good fit for early-stage Archibus deployment.**

- Traffic pattern mismatch: AWS pricing optimized for consistent workloads, not sporadic chatbot usage
- Bundle requirements: High-end GPUs (A100, H100, H200) only available as 8x bundles
- Cost inefficiency: 37-85% premium over alternatives for equivalent serverless capacity

## Why Not Now

**Sporadic Traffic Reality**

- POC/early production = unpredictable, low-volume requests
- AWS scale-to-zero exists but pricing assumes eventual steady-state usage
- Commitment discounts (30-50%) require 1-3 year terms we can't justify

**GPU Access Limitations**

- Single A100/H100/H200 unavailable on AWS for real-time inference
- Minimum 8x GPU bundles = $32-63/hr baseline cost
- Alternative providers offer single-GPU access at $2-6/hr

**Enterprise Fit Mismatch**

- AWS designed for enterprise workloads with predictable scaling
- Current state: validation phase, not production scale
- Re-evaluate when: multiple active clients with measurable traffic patterns

## When AWS Makes Sense

- High-volume consistent traffic (hundreds of daily active users)
- Existing AWS infrastructure investment requiring integration
- Compliance requirements mandating specific cloud providers
- Multi-year commitment justified by predictable growth

## Detailed Analysis

Full pricing comparison, GPU availability matrix, and source references:
[AWS vs RunPod GPU Comparison](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/aws-vs-runpod-comparison)

---

*Decision documented: 2025-12-03*
*Related Issue: DaveX2001/deliverable-tracking#9*

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

