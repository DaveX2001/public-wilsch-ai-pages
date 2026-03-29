---
publish: true
---

# Faceless Reset Plan - Advisor Talking Points
[[client-paul]]

## The Problem

- **Architectural sprawl**: 2 servers, 3 frontends, 2 backends
- **Reliability issues**: Pipeline breaks, quality degradation, constant firefighting
- **Root cause**: Complexity from parallelization + multi-tenant prep while serving production

## The Solution

- **Fresh start**: Deploy Paul's original code on single GPU server
- **Keep it simple**: One box, one backend, prove it works
- **Incremental approach**: Add parallel TTS + Whisper only after baseline is stable

## The Timeline

- **Dec 10th deadline**: New client wants to start
- **Paul's stance**: "Just make it work" - onboard with any path that delivers
- **Strategy**: Stable baseline first, iterate from there (no more firefighting)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

