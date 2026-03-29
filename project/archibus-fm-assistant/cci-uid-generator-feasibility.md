---
publish: true
---

# CCI UID Generator - Feasibility Assessment
[[client-archibus]]

## Context

Kaia asked if AI could automatically generate unique IDs for building assets based on CCI (Construction Classification International) rules. Currently this is done manually by looking up classification tables.

## What is CCI?

CCI-EE is Estonia's construction classification system based on ISO standards. It provides a unified way to label buildings, systems, and components across the construction lifecycle.

**Code Structure:**
```
CO-GPB01%GPB03(DA01;5;30;75)
│  │     │     └── Properties (flowrate, pressure, efficiency)
│  │     └── Type code (specific variant)
│  └── Class code (pump category)
└── Table prefix (CO = Component)
```

**Classification Tables:**

| Code | What it classifies |
| ---- | ------------------ |
| CO | Components (pumps, doors, equipment) |
| CS | Spaces (rooms, corridors) |
| CE | Buildings |
| CF/CT | Systems (functional/technical) |

## Feasibility Verdict

**YES - Automation is feasible.**

### Why it's deterministic

1. **Table lookup:** "Liquid velocity pump" → GPB (defined mapping)
2. **802 component codes** in CO table - finite, well-defined mappings
3. **Hierarchical structure:** Category → Subcategory → Specific (3 levels)
4. **ISO-based rules:** Clear standards, not ad-hoc decisions

### Why AI could do this

Given any input (structured or natural language), AI can:
1. Parse and extract the asset type
2. Select correct table (CO for components)
3. Map asset name to class code via lookup
4. Add type/variant and properties if provided

## How It Works

**Input:** Any description of the asset
**Output:** CCI code

### Example Mapping

| Input | → | CCI Code |
| ----- | - | -------- |
| "liquid velocity pump" | → | CO-GPB03 |
| "there's a pump in room 201" | → | CO-GPB (+ location) |
| "liquid velocity pump, 5 l/s" | → | CO-GPB03(DA01;5) |

### The Lookup Path

```
"liquid velocity pump"
      ↓
It's a physical component → CO table
      ↓
It's a pump → GP category
      ↓
It's centrifugal (velocity-based) → GPB
      ↓
Specific instance → 03
      ↓
OUTPUT: CO-GPB03
```

## What I Need

1. **Example input data** - Excel, screenshot, whatever format you have

2. **Input type** - Structured, unstructured, or both?

3. **What success looks like** - Expected output for that input

## Resources

- [CCI-EE Excel (2023.08.0.1)](https://ehituskeskus.ee/wp-content/uploads/2023/08/CCI-EE-2023.08.0.1.xlsx) - Source data with all classification tables
- [CCI-EE Guidance PDF](https://ehituskeskus.ee/wp-content/uploads/2022/03/CCI-EE-guidance-material_14.02.2022_70-p_En.pdf) (English, 70 pages)
- [CCI-EE Tables Portal](https://ehituskeskus.ee/cci/ee-tabelid/) (Estonian)
- [FlowBIM CCI Introduction](https://flowbim.ee/cci-ee-context/introduction-cci-ee/)

---

© 2026 Wilsch AI Services OÜ. All rights reserved. Licensed under [CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/).

