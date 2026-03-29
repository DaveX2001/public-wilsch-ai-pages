---
publish: true
---

# Case Study Web Template

Reusable JSON schema for Wilsch AI website case studies. This template defines the 8-section structure used at [wilsch-ai-site.vercel.app](https://wilsch-ai-site.vercel.app).

---

## Quick Reference

| Section | JSON Key | Required Content |
|---------|----------|------------------|
| 1. Hero | `hero` | headline + summary |
| 2. Motivation | `motivation` | why + expectation |
| 3. Overview | `overview` | 3 stats |
| 4. Challenge | `challenge` | 3 pain points |
| 5. Solution | `solution` | 3 steps + tech stack |
| 6. Results | `results` | before/after metrics + quote |
| 7. Timeline | `timeline` | project phases |
| 8. CTA | `cta` | contact |

**Differentiator:** The Timeline section is unique to Wilsch AI - competitors don't show project journey.

---

## Full JSON Schema

```json
{
  "id": "case-id",

  "client": {
    "name": "Company Name",
    "industry": "Industry",
    "logo": "/path/to/logo.png",
    "description": "1-sentence description"
  },

  "hero": {
    "headline": "KI-gestützte X (5-7 words)",
    "summary": "Problem + solution in 2 sentences"
  },

  "motivation": {
    "title": "Warum Wilsch AI?",
    "why": "Why they came to us (1-2 sentences)",
    "expectation": "What they hoped to achieve (1-2 sentences)"
  },

  "overview": {
    "stats": [
      { "value": "8.000+", "label": "Metric description" },
      { "value": "1.300+", "label": "Metric description" },
      { "value": "100%", "label": "Metric description" }
    ]
  },

  "challenge": {
    "title": "Die Herausforderung",
    "intro": "1-sentence problem framing",
    "painPoints": [
      { "title": "Problem 1", "description": "1-2 sentences" },
      { "title": "Problem 2", "description": "1-2 sentences" },
      { "title": "Problem 3", "description": "1-2 sentences" }
    ]
  },

  "solution": {
    "title": "Die Lösung",
    "intro": "1-sentence solution summary",
    "steps": [
      { "title": "Step 1", "description": "1-2 sentences" },
      { "title": "Step 2", "description": "1-2 sentences" },
      { "title": "Step 3", "description": "1-2 sentences" }
    ],
    "techStack": ["Tech 1", "Tech 2", "Tech 3"]
  },

  "results": {
    "title": "Die Ergebnisse",
    "intro": "1-sentence results summary",
    "metrics": [
      {
        "before": "Previous state",
        "after": "New state",
        "label": "What changed",
        "context": "Why it matters"
      }
    ],
    "quote": {
      "text": "Customer quote",
      "attribution": "Company Name"
    }
  },

  "timeline": {
    "title": "Projektverlauf",
    "phases": [
      {
        "id": "phase-id",
        "title": "Phase Name",
        "date": "Date or Q1 2026",
        "status": "completed | in-progress | coming-soon",
        "summary": "1-sentence phase summary",
        "highlights": ["Bullet 1", "Bullet 2", "Bullet 3"]
      }
    ]
  },

  "cta": {
    "headline": "Interesse geweckt?",
    "subtext": "Lassen Sie uns unverbindlich über Ihre Möglichkeiten sprechen.",
    "buttonText": "Gespräch vereinbaren",
    "buttonLink": "/contact"
  },

  "sources": {
    "transcripts": [
      { "name": "Transcript name", "date": "Date", "driveId": "Google Drive ID" }
    ],
    "documents": [
      { "name": "Document name", "date": "Date", "driveId": "Google Drive ID" }
    ]
  }
}
```

---

## Content Checklist

Before building a case study, gather:

| Content | Source | Required? |
|---------|--------|-----------|
| Client name + industry | Known | Yes |
| Why they came to us | Workshop transcript | Yes |
| What they expected | Workshop transcript | Yes |
| 3 key metrics | Workshop/POC data | Yes |
| 3 pain points | Workshop transcripts | Yes |
| 3 solution steps | Technical approach | Yes |
| 3 before/after results | POC outcomes | Yes |
| Tech stack | Implementation | Yes |
| Timeline phases | Project history | Yes |
| Customer quote | Optional (anonymize if needed) | No |
| Logo | Client approval required | No |

---

## Files Per Case Study

```
src/
├── data/case-studies/{id}.json     ← All content (this schema)
├── pages/{Name}House.jsx           ← Page component + unique color palette
main.jsx                            ← Route (BEFORE :id route!)
App.jsx                             ← Card in caseStudies array
```

**Important:** Each case study needs its own color palette. Don't reuse AVO's black/white scheme.

---

## Reference Links

| Resource | Link |
|----------|------|
| **Live Example (AVO)** | https://wilsch-ai-site.vercel.app/case-studies/avo |
| **AVO JSON Source** | [avo.json](https://github.com/DaveX2001/wilsch-ai-site/blob/main/src/data/case-studies/avo.json) |
| **Code Template Guide** | [CASE-STUDY-TEMPLATE.md](https://github.com/DaveX2001/wilsch-ai-site/blob/main/docs/CASE-STUDY-TEMPLATE.md) |
| **Schema Research** | [Issue #374 Comment](https://github.com/DaveX2001/deliverable-tracking/issues/374#issuecomment-3699913158) |
| **Content Requirements** | [Issue #374 Comment](https://github.com/DaveX2001/deliverable-tracking/issues/374#issuecomment-3699920638) |
| **Issue #374** | [Case Study Research & AVO Timeline Template](https://github.com/DaveX2001/deliverable-tracking/issues/374) |

---

## Timeline Phase Status Values

| Status | Display | Use When |
|--------|---------|----------|
| `completed` | Green checkmark | Phase is done |
| `in-progress` | Blue indicator | Currently working |
| `coming-soon` | Amber with "Demnächst" | Future phase |

---

## Three-Act Narrative Structure

The 8 sections follow a storytelling arc:

| Act | Sections | Purpose |
|-----|----------|---------|
| **Setup** | Hero, Motivation, Overview | Who is the customer, why did they need help, what's the scale |
| **Conflict** | Challenge | What made this hard |
| **Resolution** | Solution, Results, Timeline | How it was solved, what changed, how we got there |
| **Action** | CTA | What the reader should do next |

---

## Workflow

```
1. Gather source content (transcripts, docs)
2. Fill JSON using this schema
3. Create JSX page with unique color palette
4. Add route in main.jsx (BEFORE :id route)
5. Add card in App.jsx caseStudies array
6. Review + iterate
```

---

## Origin

Template structure finalized 2026-01-14 based on:
- McKinsey case study standards
- Ulrich feedback (Motivation section added)
- Thomas feedback (design iterations)
- AVO as reference implementation

See [[case-study-slide-template]] for presentation slide version.

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

