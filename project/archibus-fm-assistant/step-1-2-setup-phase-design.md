---
publish: true
---

# Step 1+2: Setup Phase — Hierarchy Understanding & Column Mapping
[[client-archibus]]

Design of the interactive Setup Phase for the AI Bulk Data Entry pipeline. Step 1 maps the client's data columns to BEM hierarchy levels. Step 2 maps the client's data columns to BEM schema fields. Together they produce the mapping contract that Step 3 consumes.

---

## Problem Statement

**Client data arrives in the client's language, not in BEM's language.**

Every client structures their facility data differently — different column names, different hierarchy depth, different conventions. Before Step 3 can process data level-by-level, two translations must happen: which columns represent hierarchy levels (Step 1), and which columns map to which schema fields (Step 2).

Today, the implementer performs both translations manually by filling out BEM's import Excel template. The AI takes over this translation; the implementer only confirms or corrects.

**Preconditions:**
- Client has provided a data file (Excel/CSV) with their facility data
- BEM asset schema is known and stable (35 fields, 9 hierarchy levels)
- Step 3 (fill level-by-level) is operational and waiting for the mapping contract

**Value proposition:** The implementer no longer fills out the import Excel template manually. The AI reads their data and produces the mapping — the implementer's time shifts from data entry to client communication.

---

## Success Definition

| Element | Definition |
|---------|-----------|
| **Goal** | AI produces a confirmed mapping contract from any client Excel — no manual template filling required |
| **Success** | Implementer uploads a data file, reviews AI's proposed mappings in conversation, and confirms with minimal corrections |
| **Done test** | Can Step 3 consume the mapping contract and process data without asking "which column is this?" → If YES → setup is complete |

---

## Approach

### Part 1: Hierarchy Inference (Step 1)

The implementer uploads a raw client Excel. The AI reads it and proposes which client columns map to which of the 9 BEM hierarchy levels.

**Inference mechanism:** Semantic analysis — not column name matching alone. The AI combines three signals:
1. **Column names** — semantic similarity to hierarchy level names
2. **Value content** — what the actual data values suggest (e.g., "CONCOURSE C" suggests a building-scale structure)
3. **Value cardinality** — few unique values = higher level, many = lower level

Column names alone are insufficient. Client data uses domain-specific naming (e.g., "AREA", "SUB AREA OF FACILITY", "LOCATION" in airport operations) that requires semantic reasoning to map to BEM levels like Building, Floor, Room.

**Authority model:** AI proposes with its best understanding, probes with useful questions when uncertain, but the implementer owns the lock-in decision. This is a conversational interaction — the AI presents, the implementer reacts, and the loop continues until the implementer explicitly confirms.

**Scope boundary:** AI maps only what exists in the client's data. It does not suggest adding hierarchy levels that the data doesn't contain. The value proposition is translating client data into BEM's language, not restructuring the client's data model.

**Non-contiguous hierarchies:** BEM's data model supports level-skipping — a Room can be a direct child of a Building with no intermediate Floor. The AI works with what the client provides: if the data has no floor column, the hierarchy is Building → Room. No container levels, no proposals to add missing layers. *(Confirmed: Rein, Feb 27 2026)*

**Rejection flow:** If the implementer disagrees, they provide context ("AREA means campus zones, not buildings") and the AI re-proposes. The correction loop is conversational — no re-upload, no UI reassignment. Loop runs until explicit confirmation.

**Output:** Confirmed mapping of client columns → BEM hierarchy levels. Step 3 uses this mapping to generate container assets (Building, Floor, Room records that don't exist in the client's Excel but are required by BEM's parent-child structure).

#### Proposal Artifact

The AI presents its hierarchy proposal using progressive disclosure:

1. **Level names** (must-have) — which client columns map to which BEM levels
2. **Sample values + lightweight reasoning** (should-have) — evidence from the data, one line per level
3. **Expanded reasoning** (on-demand) — implementer can ask the AI to explain its inference

All levels appear in a single proposal. Uncertain levels are flagged inline — the AI does not split confident and uncertain levels into separate phases.

**Example — clean data (CAFM sample, 200 rows):**

| BEM Level | ← Client Column | Sample Values | Confidence |
|-----------|-----------------|---------------|------------|
| Building | Location | Building A, B, C, D | High |
| Floor | Floor | 1, 2, 3, G | High |
| Room | Room No. | 315, 330, 173 | High |

**Example — domain-specific data (Housekeeping Tracker, FMM Qatar):**

| BEM Level | ← Client Column | Sample Values | Confidence |
|-----------|-----------------|---------------|------------|
| Complex | AREA | CONCOURSE A, B, C | ⚠️ Medium |
| ??? | SUB AREA OF FACILITY | PTC, RETAILS | ⚠️ Uncertain |
| Room | LOCATION | DEPARTURE GATE C4 | High |

The clean data resolves in one confirmation. The domain-specific data triggers a correction loop — the AI surfaces the ambiguity with its reasoning, and the implementer provides the external context the data alone cannot supply.

#### Hierarchy Type Descriptions

BEM defines 9 hierarchy levels (Campus → Site → Complex → Property → Building → Floor → Stairwell → Suite → Room). The AI infers which levels apply by matching the client's data against **descriptions and examples per hierarchy AssetType** — not just the type name.

Without descriptions, the AI is guessing from a single word ("Complex" vs "Building"). With descriptions, the AI matches "4 concourse zones in a terminal" against a defined pattern.

**Hierarchy type descriptions (validated with Rein, Feb 27 2026):**

| BEM Type | Proposed Description | Examples |
|----------|---------------------|----------|
| Campus | Entire grounds spanning multiple buildings and sites | University campus, hospital campus |
| Site | A distinct land area within a campus | North wing, Terminal 1 |
| Complex | A group of related zones sharing infrastructure | Airport concourse, shopping mall wing |
| Property | A land parcel or named estate with its own address | Main Office Park |
| Building | A single enclosed structure | Office Tower A, Warehouse B |
| Floor | A horizontal level within a building | Floor 1, Ground Floor, Mezzanine |
| Stairwell | A vertical access shaft (sibling of Floor) | Stairwell A, Fire escape north |
| Suite | A named functional unit within a floor | Suite 204, Lab B, Retail zone |
| Room | An individual enclosed space | Room 315, Server Room, Gate C4 |

Rein confirmed these descriptions as "accurate enough" for AI inference. BEM's asset_types file provides structural codes only — these descriptions serve as the AI's reference for semantic matching between client data and BEM hierarchy levels. *(Confirmed: Rein, Feb 27 2026)*

#### Name Enrichment

After hierarchy confirmation (Step 1, Beat 2), the AI proposes a naming pattern as a separate confirmation. BEM stores all asset types in a single table — a floor named "3" is unreadable without its parent context. The AI compiles readable names by aggregating hierarchy context: "Building D Floor 3 Room 315" instead of just "315".

The enrichment rule is contextual: if the client's data already contains readable names (e.g., "CONCOURSE C"), the AI passes them through. If the data contains only codes or numbers (e.g., "3"), the AI enriches. The AI discriminates when to apply the rule — this is interpretation, not copying. The discrimination rule runs behind the scenes; the AI presents the result for confirmation.

Readability validation is an AI concern — the AI flags unreadable names (e.g., "X1", "ABC123") during the interactive setup phase. The API accepts anything; the quality gate is in the AI layer.

**Interaction placement:** Name enrichment is presented as a separate confirmation after the hierarchy is locked in (Beat 2 of Step 1). This keeps the hierarchy decision and the naming decision as separate cognitive loads — the implementer focuses on one thing at a time.

**Example — CAFM sample (clean data):**

> **AI (Beat 2):** Some of your hierarchy values are just numbers. I'd like to compile readable names:
>
> | Level | Raw Value | → Enriched Name |
> |-------|-----------|----------------|
> | Building | Building A | Building A *(already readable — pass through)* |
> | Floor | 3 | Building A Floor 3 *(enriched)* |
> | Room | 315 | Building A Floor 3 Room 315 *(enriched)* |
>
> Buildings keep their original names. Floors and rooms get parent context added. Agree?

**Example — Housekeeping Tracker (domain-specific data):**

> **AI (Beat 2):** Your hierarchy values are already readable names — no enrichment needed:
>
> | Level | Raw Value | → BEM Name |
> |-------|-----------|------------|
> | Complex | CONCOURSE C | CONCOURSE C *(pass through)* |
> | Room | DEPARTURE GATE C4 | DEPARTURE GATE C4 *(pass through)* |
>
> All names are already descriptive. I'll use them as-is.

Name enrichment boundaries are confirmed as empirical — the exact discrimination logic (when to enrich vs pass through) will be tested against real client data. Three agreed reference cases: "Building D" → pass through (already readable), "3" → enrich to "Building D Floor 3" (bare number), "PTC" → flag for implementer (ambiguous acronym). *(Confirmed: Rein + Marius, Feb 27 2026)*

---

### Part 2: Column-to-Schema Mapping (Step 2)

With the hierarchy established, the AI maps remaining client columns to BEM's 35 schema fields.

**Mapping model:** Two-layer. First, column-level matching: every client column is either **mapped** (AI is confident of the BEM field) or **flagged** (AI cannot determine the match). Second, for enum fields only, value-level matching: each client value is either a direct match or needs translation.

Mapped columns go through batch confirmation — the implementer sees all confident mappings in one table and confirms. Flagged columns get individual 1x1 resolution.

```
┌─────────────────────────────────────────────────────┐
│  Layer 1: Column-Level Matching                     │
│                                                     │
│  Client Column        →  BEM Field                  │
│  ─────────────           ─────────                  │
│  "Status"             →  status        ✅ Mapped    │
│  "Condition"          →  condition     ✅ Mapped    │
│  "Serial No."         →  serialNumber  ✅ Mapped    │
└──────────────────────────┬──────────────────────────┘
                           │
                    (enum fields only)
                           │
                           ▼
┌─────────────────────────────────────────────────────┐
│  Layer 2: Value-Level Matching                      │
│                                                     │
│  Client Value    →  BEM Enum Value                  │
│  ────────────       ──────────────                  │
│  "Active"        →  "Active"           ✅ Match     │
│  "Inactive"      →  ???                ⚠️ Suggest   │
│                     ├─ "Out of Service"              │
│                     ├─ "Not Ready"                   │
│                     └─ "In Storage"                  │
└─────────────────────────────────────────────────────┘
```

**Confirmation surface:** Chat-rendered table as the primary review artifact. The implementer sees the proposed mappings and confirms in conversation. Excel export is available as an option for deeper review.

**Training data:** The more diverse client Excels the team provides internally, the better the AI's context engineering becomes for column name recognition. Self-improving agent loops across deployments are a post-deployment architecture concern.

#### Column Taxonomy

Every client column falls into one of six categories. The category determines how the mapping contract records it and how Step 2 presents it to the implementer.

| Category | Contract Records | Step 2 Behavior |
|----------|-----------------|-----------------|
| **Hierarchy assignment** | clientColumn, bemLevel | Handled in Step 1 (Part 1) |
| **Direct passthrough** | clientColumn → bemField | Batch confirm — AI is confident |
| **Passthrough with fallback** | clientColumn → bemField + auto-fallback | Auto-resolved — no implementer flag |
| **Enum mapping** | clientColumn → bemField + value translation table | Batch confirm (column) + 1x1 (unmatched values) |
| **Flagged unmappable** | flagged with options: additional properties (key-value) / exclude | 1x1 — AI flags neutrally, implementer decides |
| **Excluded (Skip tier)** | excluded, reason recorded | Excluded — no implementer decision |

**CAFM example (15 columns → 6 categories):**
- Hierarchy: Location, Floor, Room No. (3 columns → Part 1)
- Direct passthrough: Serial No.→serialNumber, Model→modelSpecific, BrandSpecific→brandSpecific, 6 dates, 3 contacts (12 columns)
- Passthrough with fallback: Asset ID→id (row index if no client ID column)
- Enum mapping: Asset Type→assetType, Status→status, Condition→condition
- Flagged unmappable: Maintenance Frequency (no BEM import field)
- Excluded: Assigned To (assignedPortfolioEmployee is Skip tier — requires Guid lookup)

#### Enum Resolution

For enum fields (AssetType, Status, Country), column-level matching is insufficient — the client's values must also match BEM's predefined enum values. The AI resolves enums during Step 2's interactive phase:

1. AI reads the client's unique values for the column
2. AI compares against BEM's enum reference (asset_types, asset-status-enum, countries table)
3. Direct matches pass through; unmatched values get ranked suggestions from the enum
4. Implementer confirms or picks from suggestions for each unmatched value
5. The confirmed translation table enters the mapping contract

The contract's enum map must be complete — every client value that appears in the data has a confirmed BEM translation. No unresolved values pass through to Step 3. A default fallback exists per enum (e.g., "Equipment" for unknown AssetType values) to catch values that appear in future data rows Step 2 never encountered.

Condition is being added to the import API as an enum field. Classification_for_maintenance may follow. Unmappable columns now route to **additional properties** — a key-value pair array per asset stored in a separate BEM table, replacing the statusDetail overflow approach. The API schema change is in progress. *(Confirmed: Rein, Feb 27 2026)*

**Undefined:** Additional properties API schema — Rein is implementing an `additionalProperties` array on the asset import body (key-value pairs per asset). The exact JSON structure and the mapping contract's 4th element for unmapped columns depend on the updated API documentation. Rein's deliverable: updated asset schema (~week of March 3, 2026). See [Meeting Agenda: Step 1+2 Hierarchy & Interpretation](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/rein-meeting-agenda-step1-2-hierarchy-interpretation).

Rein confirmed "Out of Service" as the correct mapping for "Inactive" — the ranked-suggestion approach is validated ("very correct approach"). The pattern works: AI reads BEM's enum descriptions, ranks suggestions by semantic fit, implementer confirms. Specific mappings beyond status (e.g., condition enum values) will follow the same pattern once the condition enum is finalized. *(Confirmed: Rein, Feb 27 2026)*

---

### Part 3: Mapping Contract (Step 1+2 → Step 3 Handoff)

The combined output of Step 1 and Step 2 is the **mapping contract** — the artifact that Step 3 consumes to process data level-by-level.

**Role:** The mapping contract is the single source of truth for Step 3. Everything Step 3 needs to know about the client's data structure — which columns are hierarchy levels, which columns map to which BEM fields, which columns are excluded — lives in this contract. Step 3 never interprets client data directly; it reads through the contract.

**Visibility:** Internal JSON, invisible to the implementer. The AI internalizes BEM's schema and produces structured output from the raw client Excel. The implementer validates the mapping through the chat-rendered table (Part 2), not by inspecting the contract itself.

**Design principle — absence = omission:** Fields not present in the contract are omitted from Step 3's JSON output. The BEM API treats missing fields as null. No explicit "skip" entries needed for unmapped BEM fields — their absence IS the signal.

**Field placement:** Address fields (address, city, state, postalCode, country) are placed on **all location-based assets** — building, floor, room. The AI auto-populates children from the building, which is the authoritative address source (property may carry a more generic address). All other mapped fields are placed on equipment leaf nodes. *(Confirmed: Rein, Feb 27 2026 — "at least address line and city line are good to repeat to the children")*

#### Contract Elements

The contract contains three elements, each corresponding to a Step 1 or Step 2 output:

**Element 1 — Hierarchy Assignments** (Step 1 output):
An ordered array where position defines nesting depth. Each entry maps a client column name to a BEM hierarchy level. Name enrichment happens during Step 1 before the contract is finalized — the contract carries the mapping, not the enrichment logic.

```json
"hierarchy": [
  {"clientColumn": "Location",  "bemLevel": "Building"},
  {"clientColumn": "Floor",     "bemLevel": "Floor"},
  {"clientColumn": "Room No.",  "bemLevel": "Room"}
]
```

**Element 2 — Field Mappings** (Step 2 output):
A flat lookup — each entry maps a client column to a BEM field. The AI knows field types and lengths from its static context (AssetImportDescription); the contract carries only the mapping, not the schema. The `id` field uses an explicit fallback when the client has no Asset ID column.

```json
"fieldMappings": [
  {"clientColumn": "Asset ID",      "bemField": "id"},
  {"clientColumn": "Asset Name",    "bemField": "name"},
  {"clientColumn": "Serial No.",    "bemField": "serialNumber"},
  {"clientColumn": "Model",         "bemField": "modelSpecific"},
  {"clientColumn": "Manufacturer",  "bemField": "brandSpecific"},
  {"clientColumn": "Purchase Date", "bemField": "datePurchased"},
  {"clientColumn": "Warranty Expiry","bemField": "warrantyTo"}
]
```

When the client has no Asset ID column, the fallback is explicit:

```json
{"clientColumn": null, "bemField": "id", "fallback": "rowIndex"}
```

**Element 3 — Enum Rules** (Step 2 output):
Per-enum-field translation tables confirmed during Step 2. Every client value that appears in the data must have a confirmed BEM translation — no nulls or unresolved values. The `default` catches values that appear in future data rows Step 2 never encountered.

```json
"enumRules": {
  "assetType": {
    "map": {"Safety": "Safety Equipment", "HVAC": "HVAC equipment", "Electrical": "Other EPE"},
    "default": "Equipment"
  },
  "status": {
    "map": {"Active": "Active", "Inactive": "Out of Service", "New": "Not Ready"},
    "default": "Unknown"
  }
}
```

**Step 3 consumption:** Step 3 walks the hierarchy array top-to-bottom, building the tree. For each equipment row, it reads field mappings to populate BEM fields and applies enum rules for translation. Absent fields are omitted from the JSON. Step 3 performs no interpretation — it executes the contract.

#### Contract Design Principles

**Transformation spec, not mutation.** The contract is a set of transformation rules applied over the immutable source Excel. The original data is never modified. With all three artifacts preserved (source Excel + contract + output JSON), every transformation is traceable and diffable.

**AI/tool boundary.** The contract separates the intelligent layer (Steps 1+2: semantic inference, interactive confirmation) from the programmatic layer (Step 3: tree building, API calls). The AI produces the contract; the Step 3 tool reads and applies it without interpretation.

**Backpressure updates the contract.** When the BEM API rejects an enum value, the AI corrects the contract's enum rules — not just the individual JSON node. This fixes the root cause: all rows with that value get the corrected translation, including buildings not yet processed. Data-specific errors (e.g., name too long) are fixed per-node.

**Contract consumption interface.** The three contract elements reach Step 3 through a single-shot stateless [FastMCP](https://github.com/PrefectHQ/fastmcp) v3 tool. MCP-first architecture: the `@mcp.tool` function IS the implementation — no separate core module or wrapper layer. FastMCP v3's `generate-cli` auto-generates a typed CLI from the MCP tool schema, providing development convenience without maintaining a separate CLI codebase.

| Environment | Interface | How |
|-------------|-----------|-----|
| **Production** (BruceBEM chat) | MCP tool | AI calls `step3_execute(contract, excel, building, mode)` via LibreChat mcpServer (streamable-http) |
| **Development** (Claude Code) | MCP tool | Claude Code connects via `mcpServers` config (stdio or streamable-http) to the same FastMCP server |
| **Scripted testing** | Auto-generated CLI | `fastmcp generate-cli` produces typed CLI from MCP tool schema |

Bulk import runs as a **separate MCP server** from the existing BruceBEM work request tools — different user personas (FM implementers vs building occupants), different repos, independent deployment. LibreChat registers both servers; the AI agent accesses tools from all registered servers transparently.

**Input:** mapping contract JSON (containing the hierarchy, field mappings, and enum rules defined in this section) + source Excel path + building name + mode (validate or import). **Output:** structured API response — success with import details, or error with `{id, field, value, reason, valid_values}` for AI self-correction.

The backpressure loop lives in the AI layer, not in the tool. The tool executes one round trip: read the contract's rules → build JSON from them → send to API → return response. The AI reads the response, decides whether to correct the contract and retry, and calls the tool again if needed. The tool never modifies the contract — it reads and applies the rules on each invocation without interpretation or modification. The AI corrects the contract between invocations and passes the updated version. *(Confirmed: David, Mar 3 2026 — extraction pass 6. Updated: David, Mar 4 2026 — extraction pass 7)*

#### Model Requirements

The pipeline targets small-model compatibility for production deployment. Development uses Claude Sonnet for rapid iteration. Production targets Qwen 3.5 (9B preferred, 30B-A3B acceptable). Future: self-hosted on RunPod with FP8 quantization (standard) or FP4 (Blackwell-architecture GPUs). The design constraint is small-model compatibility (9B), not quantization specifics.

This model target constrains the design: API error responses must be structured and unambiguous (`{id, field, value, reason, valid_values}`) so a 9B model can execute the backpressure loop — reading errors, correcting the contract, and retrying — without large-model reasoning. The tool's structured output format serves both correctness (traceable errors) and small-model compatibility (no interpretation required). *(Source: Marius, Mar 4 2026 — #852 comment)*

#### Data Type Detection (Step 0)

Before column mapping begins, the AI assesses whether the uploaded data is an asset inventory. Not all client files contain equipment data — operational trackers (work orders, maintenance logs, staff lists) contain location hierarchies but no asset properties. The AI surfaces this upfront: "This appears to be a work order tracker, not an asset inventory. Location hierarchy can be extracted, but there are no equipment records."

The mapping contract is entity-specific — designed for the Assets import pipeline. Other BEM entities (employees, departments, PM procedures) would follow the same pattern with entity-specific field targets.


---

### Part 4: Interaction Flow

The implementer's journey through Steps 0-2 as a chat-based conversation inside BruceBEM's AI interface. The implementer uploads a file, the AI analyzes it, and the conversation walks through each step until the mapping contract is confirmed and Step 3 can begin.

#### Context Layers

The AI draws from three context layers during the setup phase:

| Layer | What | When Loaded |
|-------|------|-------------|
| **Static** | Four BEM reference artifacts baked into the AI agent: (1) [AssetImportDescription](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit) — 37 import fields with data types, lengths, descriptions; (2) [asset_types.xlsx](https://docs.google.com/spreadsheets/d/1Wc1BL18e5Vaxx7bAzsvxAeWojfvxRrWd/edit) — AssetType enum; (3) [asset-status-enum.csv](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc/blob/staging/.claude/tracking/issue-373/asset-status-enum.csv) — Status enum; (4) Countries table (249 entries). Google Sheets are working copies — authoritative versions live on Bruce Partners SharePoint. | Pre-loaded before any client interaction |
| **Dynamic** | The mapping contract — hierarchy assignments, field mappings, enum rules. Built progressively through Steps 1+2 confirmations | Accumulated during the interactive phase |
| **Runtime** | API backpressure — validation errors, enum lists returned on rejection, correction signals | Generated during Step 3 autonomous processing |

The static layer is the AI's prior knowledge. The dynamic layer is the output of the interaction flow described below. The runtime layer is Step 3's feedback mechanism (see [Step 3 Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)). The exact encoding of each layer is an implementation detail — the design-level decision is the three-layer separation and progressive accumulation.

#### Design Principles

Three principles govern the interaction flow across all steps:

**1. Fast lane / slow lane.** AI presents confident work as quick-scan batch tables, uncertain work as individual 1x1 engagement. This pattern applies universally — hierarchy, column mapping, enum resolution. The implementer's attention goes where it's needed, not spread across everything equally.

**2. Announce and ask for foundational decisions, announce and advance for low-stakes.** Data type detection (Step 0) is low-stakes for supported types — the AI announces and advances. Hierarchy confirmation (Step 1) is foundational — it determines the entire tree structure. The AI always asks. The weight of the confirmation gate matches the weight of the decision.

**3. Progressive trust.** First import = full explicit gates at every step. As the implementer and AI build mutual understanding, gates can condense. The AI sets expectations upfront so the implementer knows the journey. v1 uses **roadmap-first** as the opening mode. Two alternative modes exist for future empirical testing.

**Opening modes — what Message 2 looks like after file upload:**

**A) Roadmap-first (v1 default):**

> I'll analyze your file in 3 steps:
> 1. Detect data type
> 2. Map your columns to BEM's hierarchy
> 3. Map remaining columns to BEM fields
>
> Let's start. Your file has 201 rows and 16 columns. This looks like equipment asset data.
>
> **Hierarchy detected:** ...

Explains the journey before showing results. Best for first-time implementers who need orientation.

**B) Show-first:**

> `cafm-asset-upload-sample.xlsx` — 201 rows, 16 columns. Equipment asset data.
>
> **Hierarchy detected:**
>
> | BEM Level | ← Your Column | Sample Values |
> |-----------|---------------|---------------|
> | Building | Location | Building A, B, C, D |
> | Floor | Floor | 1, 2, 3, G |
> | Room | Room No. | 116, 232, 333 |
>
> Does this match your data structure?

No preamble — jumps straight to the first result. For implementers who know the process.

**C) Result-first:**

> `cafm-asset-upload-sample.xlsx` — 201 rows, 16 columns. Equipment data.
>
> **Full analysis:**
> - Hierarchy: Building → Floor → Room (3 levels)
> - Mapped: 9 columns → BEM fields
> - Enums: AssetType (4 values), Status (3 values)
> - Flagged: 2 columns need your input
>
> Everything look right, or should I walk through each section?

Shows the entire analysis at once. Closest to Miguel's "boom" demo feedback. Implementer confirms in one shot or drills into detail.

See [Anthropic Skill Best Practices — Progressive Disclosure](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) for the underlying pattern: AI recommends one option with an escape hatch, not a menu of choices.

#### Happy Case Walkthrough (CAFM Sample)

The CAFM asset upload sample (201 rows, 16 columns, 4 buildings) represents the clean-data path. 8 messages, 4 confirmation gates.

**Message 1** — Implementer uploads `cafm-asset-upload-sample.xlsx`.

**Message 2 — Step 0 + Step 1, Beat 1 (data type + hierarchy):**

> `cafm-asset-upload-sample.xlsx` — 201 rows, 16 columns. Equipment asset data.
>
> **Hierarchy detected:**
>
> | BEM Level | ← Your Column | Sample Values |
> |-----------|---------------|---------------|
> | Building | Location | Building A, B, C, D |
> | Floor | Floor | 1, 2, 3, G |
> | Room | Room No. | 116, 232, 333 |
>
> 3 levels. Does this match your data structure?

Data type announced without a gate (high confidence, supported type). Hierarchy presented with an explicit question — the implementer must confirm before advancing.

**Message 3 — Step 1, Beat 2 (name enrichment):**

After hierarchy confirmation:

> Some of your hierarchy values are just numbers. I'd like to compile readable names:
>
> | Level | Raw Value | → Enriched Name |
> |-------|-----------|----------------|
> | Building | Building A | Building A *(already readable)* |
> | Floor | 3 | Building A Floor 3 |
> | Room | 315 | Building A Floor 3 Room 315 |
>
> Buildings keep their original names. Floors and rooms get parent context added. Agree?

Separate cognitive load from hierarchy. The discrimination rule ran behind the scenes — the AI shows the result.

**Message 4 — Step 2 (column mapping batch):**

> **Column mapping — 13 remaining columns analyzed:**
>
> **Mapped (9 confident):**
>
> | Your Column | → BEM Field |
> |-------------|------------|
> | Asset Name | assetName |
> | Serial No. | serialId |
> | Manufacturer | manufacturer |
> | Model | model |
> | Purchase Date | datePurchased |
> | Warranty Expiry | warrantyExpiry |
> | Condition | condition |
> | Status | status |
> | Assigned To | assignedPortfolioEmployee |
>
> **Already used in hierarchy:** Location, Floor, Room No.
>
> **Needs your input (2):**
> 1. `Asset Type` → assetType — one enum value didn't match
> 2. `Maintenance Frequency` — no BEM field found
>
> Do the 9 mappings above look correct? If so, we'll resolve the 2 flagged items next.

Confident mappings in a batch table — still a confirmation gate. The implementer reviews the batch and may disagree with a mapping before moving on. Flagged items listed with count so the implementer knows what's coming. Hierarchy columns called back to Step 1. Internal IDs (Asset ID, ParentId) silently handled.

**Message 5 — Step 2 (enum resolution, 1x1):**

For each enum mismatch, the AI leads with its top recommendation plus alternatives:

> `Asset Type` → BEM's `assetType`. 3 of 4 values matched:
>
> | Your Value | → BEM AssetType | Match |
> |------------|----------------|-------|
> | Safety | Safety Equipment | Fuzzy |
> | IT Equipment | IT Equipment | Exact |
> | HVAC | HVAC equipment | Fuzzy |
> | Vertical Trans. | **?** | No match |
>
> For "Vertical Trans." I'd suggest **Vertical Transportation**. Other options: Other Equipment, Mechanical Equipment. [See all BEM types]
>
> Which one fits?

Then for Status:

> `Status` → BEM's `status`. 1 of 3 matched:
>
> | Your Value | → BEM Status |
> |------------|-------------|
> | Active | Active *(exact)* |
> | Inactive | **?** — I'd suggest **Out of Service** ("asset no longer used, could be out of service for a while") |
> | New | **?** — I'd suggest **Not Ready** ("asset being installed or commissioned") |
>
> Does "Inactive" mean the same as "Out of Service" in your client's context?

One mismatch at a time. AI provides BEM descriptions to help the implementer decide. Progressive disclosure: top recommendations shown, full enum list one click away.

**Message 6 — Step 2 (unmappable column):**

> `Maintenance Frequency` doesn't match any of BEM's 35 import fields.
>
> **→ Exclude** — this data won't be imported.
>
> If you'd rather keep it, I can store it in `statusDetail` (free-text field). What would you prefer?

Default recommendation with escape hatch.

**Message 7 — Summary + handoff:**

> **Mapping complete.** Summary:
>
> - **Hierarchy:** Building → Floor → Room (names enriched)
> - **Mapped:** 9 equipment fields
> - **Enums resolved:** AssetType (4 values), Status (3 values)
> - **Excluded:** Maintenance Frequency
>
> Ready to process 201 rows across 4 buildings. Proceed?

Last gate before autonomous processing. In-chat summary is the default review artifact.

The summary above is the default handoff artifact. A detailed "decision receipt" is available on demand — a chat-native expandable view showing every mapping row, every enum translation, and every exclusion with reason. Two faces, one artifact: the summary (quick scan) and the receipt (full audit). The receipt is in-chat, not Excel — it aggregates heterogeneous decision types (hierarchy, mappings, enums, exclusions) that don't fit a single spreadsheet format. Test empirically whether implementers use the receipt or just confirm the summary.

**Message 8 — Step 3 (autonomous processing):**

> Processing...
>
> **Building A:** 3 locations + 52 equipment ✓
>
> **Building B:** 3 locations + 48 equipment ✓
>
> **Building C:** 3 locations + 55 equipment ✓
>
> **Building D:** 3 locations + 46 equipment ✓
>
> **Done.** 201 equipment + 12 location assets inserted. 0 errors.
>
> [Export to Excel] [View in BruceBEM]

In-chat progress by building. Excel export available on demand for detailed review.

#### Step 0 Scenarios

| Scenario | AI Behavior | Gate? |
|----------|------------|-------|
| **0a: Recognized equipment data** | Announces "equipment asset data" and advances to hierarchy | No gate — announce and advance |
| **0b: Recognized but unsupported** (e.g., work orders) | "This looks like work order data. I can only process asset/equipment data right now." | Stop — blocked |
| **0c: Ambiguous data type** | "I'm not sure if this is equipment data or work orders. What type of data is this?" | Ask — implementer decides |
| **0d: Insufficient data** | Equipment data detected but no hierarchy columns (flat list with no location context) | Stop — "I need at least one location column to build the hierarchy" |

Data type detection confirmed: the AI distinguishes by checking non-hierarchy columns. Equipment columns (Serial No., Model, Manufacturer) = assets. Task columns (Task Description, Completion Status, Scheduled Date) = work orders. If neither equipment nor location data is present, the AI rejects. *(Confirmed: Rein, Feb 27 2026)*

#### Step 1 Scenarios

| Scenario | Description | Example |
|----------|------------|---------|
| **1a: Clean, all high confidence** | All columns map clearly to hierarchy levels | CAFM sample: Location→Building, Floor→Floor, Room No.→Room |
| **1b: Partial confidence** | Some levels confident, some uncertain | Housekeeping Tracker: AREA→Complex (medium), SUB AREA→??? (uncertain), LOCATION→Room (high) |
| **1c: No hierarchy detected** | Flat equipment list, no location columns | Inventory export with only equipment attributes (name, serial, type) |
| **1d: Too many candidate columns** | Multiple columns could be hierarchy levels | Facility export with AREA, ZONE, SUB AREA, LOCATION, SECTOR — implementer disambiguates |
| **1e: Hierarchy found, wrong data type** | Location columns exist but non-hierarchy columns suggest work orders, not equipment | Housekeeping Tracker: has AREA/LOCATION hierarchy but columns are Task Description, Completion Status, Scheduled Date |

Scenario 1e is Step 0 and Step 1 interacting — data type detection gets refined BY the hierarchy analysis.

#### Step 2 Scenarios

| Scenario | Description | Example |
|----------|------------|---------|
| **2a: Mostly confident** | Most columns match, few flagged | CAFM: 9 mapped, 2 flagged |
| **2b: Many fuzzy matches** | Cryptic column names, AI guesses | Abbreviated names: SN→serialId?, MFR→manufacturer?, EQ_NM→assetName? |
| **2c: All confident** | Clean template-based data, every column matches | Rare — all 12 columns match, no enum conflicts, no flags |

#### Future Extension: Existing-Database Scenario (Phase 2)

When the BEM database already contains location assets (buildings, floors, rooms), the implementer imports new equipment under existing parents. This is "bulk insert using existing parents" — the equipment is new, but the location hierarchy already exists. Rein describes this as "completely different scenario" from clean insert and "much more complicated."

**Identifying signal (hypothesis):** The client's data contains hierarchy codes (building code "A02", floor code "GF", room code "LRBAL") but no descriptive names or addresses. This likely indicates that parent assets already exist in BEM and the codes are references, not new records — but this needs validation with more client data examples.

**Algorithmic sketch (Rein, Feb 27 2026):**
1. Extract unique building codes from the client's data
2. API queries BEM database for buildings matching those codes → returns real UUIDs
3. For each matched building, API retrieves its floor and room children
4. AI compares the returned hierarchy JSON against the client's floor/room codes
5. If hierarchy matches: insert equipment under the matched room (using room's UUID as parent)
6. If hierarchy doesn't match: flag to implementer — "room code X not found under building Y"
7. If building code not found: ask implementer to verify codes or provide more data

**Key constraints:**
- Room codes can repeat across buildings — matching must verify the full chain (building → floor → room), not just the room code
- Large databases: cannot fetch all assets at once. Process building-by-building, same as clean insert
- Equipment already exists: if lookup finds equipment with the same code under the same room, return error (duplicate detection)
- otherCode field is the lookup key — BEM's primary key is UUID, but otherCode preserves the client's original code

**Phase ordering:** Complete Phase 1 (clean insert with unmapped properties + name enrichment) → demo → Phase 2 design begins. Phase 2 needs new API endpoints for hierarchy lookup by code.

**Applicability to other BEM tables:** The pattern generalizes. Employees have department → business unit (3 levels, simpler). Once assets work, remaining 11 tables follow the same approach with fewer levels and fewer fields. *(Source: Rein, Feb 27 2026)*

---

### Part 5: Test Rubric

Defines how to witness the Setup Phase pipeline. One evaluation surface: the **contract output** — the mapping contract JSON compared against a known-good expected contract for the CAFM sample. Same input, same BEM reference data → same contract. The output is deterministic.

**Audience:** The witness ceremony's ultimate goal is to impress Miguel (Product Owner), not just pass a technical check. Miguel cares about intelligent results — readable asset names, no process leakage, no template-feeling rigidity. The SA reviews technically first; Miguel judges whether the system is ready for client-facing demos.

#### Environment

[LibreChat](https://www.librechat.ai/) — deployed locally, connected to the Anthropic API (Sonnet). The AI runs in an isolated chat environment that doesn't know it's being tested. LibreChat's native [MCP support](https://www.librechat.ai/docs/features/mcp) connects the archibus-bulk-import FastMCP server. A LibreChat [agent](https://www.librechat.ai/docs/features/agents) provides the pre-prompted persona with MCP tools attached.

**Setup:**
1. Deploy LibreChat (Docker)
2. Connect Anthropic API key (Sonnet)
3. Add MCP server in LibreChat's yaml config → points to archibus-bulk-import FastMCP server
4. Create a LibreChat agent with the bulk import persona + MCP tools

The Dev Lead opens a chat, uploads the test fixture, and walks through Steps 0→1→2 as the implementer. Tool calls and responses are visible inline in the conversation.

#### Test Fixtures

| Fixture | Location | Purpose |
|---------|----------|---------|
| `cafm-asset-upload-sample.xlsx` | `data/` | Primary — happy case end-to-end (200 rows, 15 columns, 4 buildings) |
| `fmm-qatar-housekeeping.xlsx` | `data/` | Optional — Step 0 rejection (non-equipment detection) |
| `Asset Data (1).xlsx` | [#852 attachment](https://github.com/DaveX2001/deliverable-tracking/issues/852) | Phase 2 — 3 sheets, 2,649 rows, dual Condition+Status enums, multi-sheet test (from Mujahid) |

Primary fixture and rejection fixture are checked into the archibus-bulk-import repo. Phase 2 fixture is an issue attachment pending repo commit.

#### Expected Contract

The golden contract lives at [`data/expected-contract.json`](https://github.com/MariusWilsch/archibus-bulk-import/blob/staging/data/expected-contract.json) in the archibus-bulk-import repo. It defines the canonical mapping contract for the CAFM sample — the exact output the AI should produce.

The contract contains three elements matching Parts 1–3 of this design doc: hierarchy assignments (Element 1), field mappings (Element 2), and enum rules (Element 3).

**Undefined:** Condition enum values — the `condition` field mapping exists in Element 2 and a placeholder entry exists in Element 3's enumRules, but the BEM condition enum values are pending Rein's API schema delivery. The golden contract will be updated when the condition enum is finalized.

#### Evaluation

A run **passes** when the AI's contract output matches `data/expected-contract.json` exactly. Field-by-field comparison across all three elements.

A run **fails** when any element deviates from the expected contract. Failures are findings routed to prompt refinement — the Dev Lead never fixes the prompt directly.

**Rubric evolution:** Exact-match is the starting rubric. If enum mappings prove non-deterministic across runs (e.g., the AI reasonably maps the same value to different BEM enums), the rubric evolves to structure-exact with SA-judged enum tolerance. Start strict, loosen based on evidence.

**Test scope:** The first witness ceremony tests against ONE golden contract (`cafm-asset-upload-sample.xlsx`). When Mujahid delivers additional CAFM examples, new golden contracts are created and added to the test suite. Additive — new examples do not block the first ceremony.

#### Optional Spot-Checks

| Spot-Check | How | What to verify |
|------------|-----|---------------|
| **Non-equipment rejection** | Upload `fmm-qatar-housekeeping.xlsx` | AI announces non-equipment data and stops — does not advance to hierarchy |
| **Correction loop** | Intentionally disagree with one hierarchy mapping | AI re-proposes, accepts correction, loops until explicit confirmation |
| **Multi-sheet handling** | Upload a multi-sheet Excel file | AI defaults to active sheet, notes available sheets, offers to switch |

Spot-checks are optional — the Dev Lead picks based on judgment.

*(Source: Extraction pass 2026-03-08 — SA feedback on test rubric. Resolutions: contract output as sole evaluation surface, LibreChat as isolated test environment, golden contract as git-committed JSON file, exact-match evaluation, conversation quality deferred to fine-tuning phase.)*

#### Prompt Architecture

The Step 2 prompt (`prompts/step-2.md`) serves as the managed artifact stand-in until the real managed artifact system is implemented. It encodes both behavioral and mechanical guidance.

**Behavioral layer (PSM-derived):** The AI's persona emerges from its operating environment, not a label. The ETL agent is a character who:
- Knows BEM deeply — confident, decisive, explains rationale without being asked
- Adapts to arbitrary input — no rigidity, no template-feeling interactions
- Improves data intelligently — does not pass through raw Excel values; adds semantic understanding

These traits follow Anthropic's Persona Selection Model: the AI behaves like a competent human colleague in this environment. Designing interactions that match how you would brief a senior CAFM data engineer is effective, not naive.

**Mechanical layer (10-section recipe):** The prompt TARGET follows Anthropic's prompt engineering structure. The current `prompts/step-0-1.md` (617 lines) partially implements this — see [prompt audit](https://github.com/DaveX2001/deliverable-tracking/blob/main/.claude/tracking/issue-1075/prompt-audit.md) for gap analysis. Gaps are addressed through iterative testing per ADR 002, not upfront rewrite.

| Section | Content for Step 2 |
|---------|-------------------|
| 1. Task context | Column classification: map client Excel → BEM schema |
| 2. Tone context | PSM persona traits (above) |
| 3. Background data | BEM context (fields, types, enums) via MCP tool |
| 4. Rules | Mapping categories, exclusion rules, enum handling |
| 5. Examples | Golden contract JSON (`data/expected-contract.json`) |
| 6. Conversation history | N/A (single-turn classification) |
| 7. Immediate task | "Classify these columns" with Excel analysis output |
| 8. Think step by step | Explicit reasoning before committing mappings |
| 9. Output formatting | JSON contract structure (3 elements) |
| 10. Prefilled response | Opening JSON brace to anchor output format |

**Anti-hallucination:** The prompt instructs the AI to say "I don't know" for uncertain mappings (flag as `excluded` with reasoning), answer only when confident in the BEM field match, and think before committing — reasoning traces surface in the conversation for implementer review.

**XML structure:** All context sections use XML delimiters (`<bem_context>`, `<excel_analysis>`, `<mapping_rules>`) for clear boundaries and token efficiency.

*(Source: Extraction pass 2026-03-09 — PSM behavioral model + Anthropic prompt engineering recipe. Miguel transcript: audience expectations. Prompt audit: extraction pass 2026-03-09 session ff776065.)*

---

### Part 6: Pipeline Integration & Deployment

Parts 1–5 designed the interactive mapping phase (Steps 0→2) and the execution phase (Step 3) independently. Two working systems exist: the MCP/Skill system produces a mapping contract through conversation, and the CLI pipeline builds nested JSON and submits to the BEM API. This part connects them and defines how the complete pipeline deploys for the client demo.

#### 6.1 Bridge Tool — Contract Consumption

The interactive phase (Steps 0→2) produces a mapping contract. The execution phase (Step 3) transforms data and submits to the BEM API. Today these phases run independently — Step 3 uses hardcoded column names and enum maps instead of consuming the contract. The bridge tool closes this gap.

**What it does:** Takes the confirmed mapping contract and source data, executes the [Step 3 pipeline](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design) (filter → hierarchy → deduplicate → generate locations → build JSON → validate/insert via API) using the contract's mappings instead of hardcoded values. Returns the API result — either success with importID or a structured error for the AI to act on.

**Backpressure:** The bridge tool implements the backpressure loop from the [Step 3 design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design) — API errors feed back to the AI, which decides whether to self-correct (update the contract) or escalate to the implementer.

**Form factor:** Registered as a FastMCP tool on the existing `archibus-bulk-import-tools` MCP server, alongside `bem_context` and `excel_analysis`.

**Contract handoff:** The interactive phase (Steps 0→2) saves the confirmed contract to disk via the filesystem MCP's `write_file` tool at a session-scoped path (`/app/uploads/{session_id}/contracts/confirmed.json`). No custom `save_contract` tool needed — a generic file write with a path convention in the skill instructions replaces the purpose-built tool. The bridge tool receives a `contract_id` argument and reads from the same shared volume. The AI passes `contract_id` to the bridge tool. Path resolution (`/app/uploads/{contract_id}/contracts/confirmed.json`) is internal to the tool — the conversation carries only the identifier, not the path or object. This ensures the contract survives session drops, MCP reconnections, and phase transitions. *(Updated: Spike validation 2026-03-16 — `save_contract` is `write_file` in disguise. Marius, Mar 16 2026.)*

**Golden contract (dev testing):** The `data/expected-contract.json` file serves dual purpose: Part 5's test rubric evaluation surface AND a bridge tool input fixture that bypasses Steps 0–2. Its schema is identical to what `save_contract` produces — the bridge tool reads both via `contract_id` without code bifurcation. For dev testing, place the golden contract at `contracts/golden.json` and pass `contract_id="golden"`. *(Confirmed: Extraction pass 2026-03-15.)*

**Multi-building processing:** The bridge tool handles building iteration internally — one tool call, not one per building. Signature: `process_buildings(buildings: list[str] | "all", contract_id: str, dry_run: bool)`. Each building is processed in isolation with independent try/except. If Building A succeeds and Building B fails, A's `importID` is logged to `completed_buildings.json` and never resubmitted. Failed buildings accumulate in `failed_buildings.json` for retry or escalation. Batch size: groups of 5 to stay within MCP timeout (~60s).

**Progress reporting:** Uses FastMCP's `ctx.report_progress(current, total, "Processing {building_name}")`. Silently no-ops if the runtime client doesn't support progress tokens.

**Error format:** API errors return via FastMCP `ToolError` with structured messages containing `field`, `value`, `reason`, and `valid_values` — parseable by the AI for self-correction decisions.

**Implementation path:** The bridge tool is a thin wrapper around the existing CLI pipeline. `cli.py` lines 162–182 contain the core `_build_and_transform()` logic. This function is extracted into a shared `pipeline.py` module; the bridge tool imports and calls it with contract-derived parameters instead of CLI arguments. All existing modules (parser, hierarchy, generator, builder) are reused unchanged.

*(Source: Gap analysis extraction pass 2026-03-12. Updated: Extraction pass 2026-03-15 — escalation thresholds relocated to §6.2 (skill content concern, not bridge tool concern).)*

#### 6.2 Skills Architecture — Persona + Two Skills

The pipeline has two distinct modes: interactive (Steps 0→2, lots of back-and-forth with the implementer) and autonomous (Step 3, near-zero questions, execution + backpressure). Mixing both modes in a single prompt confuses the AI about when to ask vs when to act. The architecture separates them into layers.

**Three layers:**

| Layer | What | When loaded |
|---|---|---|
| **Persona** (system prompt) | PSM-derived behavioral identity — knows BEM deeply, adapts to arbitrary input, improves data intelligently. 5 beliefs + tone context. | Always present |
| **Skill 1: bem-setup** (interactive) | Recipe for Steps 0→2. Guides the AI through detection → hierarchy → column mapping → enum resolution → contract assembly. Conversational, gates on implementer confirmation. | Loaded when implementer uploads Excel |
| **Skill 2: bem-backpressure** (autonomous) | Recipe for Step 3's backpressure loop. Teaches the AI how to interpret API errors, decide whether to self-correct the contract or escalate to the implementer, and retry. The bridge tool executes; this skill teaches the AI what to do with the results. | Loaded when contract is confirmed |

**Design intent:** Skills follow Anthropic's progressive disclosure model — descriptions load light (~100 tokens), full content loads on invocation. This prevents the AI from being overburdened with instructions for both modes simultaneously. The persona stays constant; the active skill determines the AI's behavior.

**MCP tools remain the same regardless of which skill is active:** `bem_context` (schema reference), `excel_analysis` (file profiling), `step3_execute` (bridge tool from §6.1). Skills tell the AI WHEN and HOW to use these tools. MCP provides the kitchen; skills provide the recipes.

**Authoring format:** Skills are authored in the [agentskills.io](https://agentskills.io) open standard — a directory-based format (`SKILL.md` + optional `scripts/`, `references/`, `assets/`). This format is portable across 32+ tools including Claude Code, Cursor, Gemini CLI, and OpenHands. The agent loads only the `description` field at startup (~100 tokens), then loads the full body on invocation. The `allowed-tools` field (experimental) pre-approves MCP tools the skill can invoke.

**Skill loading mechanism:** The AI controls when skills load — not the runtime. FastMCP v3's `SkillsDirectoryProvider` serves skill directories as MCP resources. The `ResourcesAsTools` transform bridges tool-only clients (like LibreChat) by exposing `list_resources` and `read_resource` as callable tools. The AI calls `list_resources` → sees skill names with descriptions (~100 tokens). When the AI determines it needs a full recipe, it calls `read_resource("skill://bem-backpressure/SKILL.md")` → the full skill content enters the conversation as a tool response. This is progressive disclosure at the MCP layer — no custom `load_skill` tool, no runtime framework support required. *(Spike validated 2026-03-16: 3 lines of Python — `SkillsDirectoryProvider` + `ResourcesAsTools` — produce working `list_resources` and `read_resource` tools. Tool-only clients discover and load skills.)*

**Skill accumulation:** Loaded skills remain in context — no unloading. The AI can reference both `bem-setup` and `bem-backpressure` simultaneously if needed. Context window management is a future optimization, not a design constraint. The Anthropic API sends `system=` fresh with every request, so the runtime can include any combination of loaded skills per turn.

**Undefined (narrowed):** The backpressure skill's production content — Marius iterates via #1094. Minimum viable version: 4-step loop (read structured error → locate failing field via `error.id` → pick closest match from `valid_values` → update contract's enumRules → retry bridge tool). Created by David via `/skill-creator` as a DoD item for the bridge tool issue. *(Narrowed: Extraction pass 2026-03-15.)*

**Escalation thresholds** (relocated from §6.1): Escalation is a skill content concern — the backpressure skill instructs when the AI should stop retrying and ask the implementer. The bridge tool returns errors; the skill decides what to do. Starting values: 3 attempts per field / 5 per building, tuned empirically. No industry standard exists. *(Relocated: Extraction pass 2026-03-15 — escalation belongs in AI-layer skill, not code-layer tool.)*

*(Source: [Anthropic Skills Guide PDF](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) Category 3 pattern. Gap analysis extraction pass 2026-03-12. Updated: Extraction pass 2026-03-15.)*

#### 6.3 Deployment — Runtime & File Lifecycle

The complete pipeline requires capabilities across five layers. Most are commodity — the interesting question is which layer has unsolved gaps.

| Layer | What it provides | Status |
|-------|-----------------|--------|
| **Runtime** | Web UI, auth, file upload, session management, model routing, skill loading | **Gap — see below** |
| **MCP Server** | Tool calling (bem_context, excel_analysis, bridge), structured errors, progress | Exists (archibus-bulk-import-tools) |
| **System Prompt** | PSM persona — always-on behavioral identity (§6.4) | Exists (prompts/system-prompt.md) |
| **Skills** | On-demand recipes loaded by AI — bem-setup, bem-backpressure (§6.2) | Content partially written |
| **Model** | Reasoning, semantic matching, multi-turn coherence | Claude (demo), Qwen (production target) |

The runtime layer has three gap primitives — capabilities that coding-oriented tools (Claude Code, Cursor) provide natively but web-deployable runtimes do not:

1. **Bidirectional file I/O** — browser upload becomes a filesystem path the MCP tool reads; tool output becomes a downloadable file
2. **Progressive skill loading** — the AI loads/swaps instruction sets mid-conversation based on workflow state
3. **Long-running tool support** — tool calls that exceed ~60s without framework-imposed timeout

This gap is not unique to ARCHIBUS. Any team deploying MCP tools for non-developer end users hits the same three primitives — it is an unspoken market category without a packaged solution.

##### 6.3.1 File Lifecycle

The `excel_analysis` MCP tool expects a local filesystem path (`file_path` parameter). In a web-based runtime, an Excel file uploaded via the browser must become a path accessible to the MCP server process.

**LibreChat gap (confirmed March 2026):** LibreChat stores uploads to `./uploads/` (Docker volume mounted at `/app/uploads/`) but does NOT pass file paths to MCP tools. Three GitHub issues confirm the gap (#10739 closed without fix, #8060 open, PR #11094 unmerged). The `@modelcontextprotocol/server-filesystem` workaround requires the unmerged PR to expose `filepath` in LLM context — a three-point failure surface with no official documentation.

**Chainlit resolution:** Chainlit's `AskFileMessage` returns uploaded files with a `.path` attribute — the actual filesystem path. No workaround needed. Stdio MCP is natively supported.

**Path injection — "tell the AI":** The runtime injects uploaded file paths into conversation context. When the implementer uploads files, the runtime appends path metadata (filename → filesystem path) to the message. The AI uses the correct path when calling MCP tools. This works because the AI has semantic understanding of which file is what — it can distinguish the main data file from reference files by name and content.

**Multi-file uploads:** Implementers bring multiple files — the primary data file plus reference files (asset types, enum mappings). The runtime surfaces all file paths; the AI determines which file goes to which tool.

**Three deployment options for file I/O:**

| Option | How it works | Path certainty | Complexity |
|--------|-------------|----------------|-----------|
| **In-process** | Runtime + MCP server in same Python process (FastMCP in-memory transport). Shared filesystem — `.path` is directly accessible. | Exact (programmatic) | Lowest — single container |
| **Docker shared volume** | Runtime and MCP server in separate containers, mounting the same host directory (`./uploads:/app/uploads/`). Runtime injects the path into conversation context. | Exact (programmatic) | Medium — two containers, volume config |
| **Convention-based** | AI knows filenames from chat, assumes uploads land at a known directory (e.g., `/uploads/{filename}`). No path injection needed. | Inferred (fragile if runtime renames files) | Lowest code — but fragile |

**Filesystem MCP — validated approach (2026-03-16):** A standard filesystem MCP server (e.g., `@modelcontextprotocol/server-filesystem` or equivalent FastMCP tools) mounted on the shared Docker volume provides `write_file`, `read_file`, `list_directory`, and `create_directory` tools. The AI discovers uploaded files via `list_directory` rather than requiring the runtime to pass file paths — this sidesteps LibreChat's file→MCP gap entirely.

**Session-scoped directories:** Each conversation writes to `/app/uploads/{session_id}/`. The AI knows its session ID (from system prompt or runtime context) and calls `list_directory` to discover files within its scope. No cross-session leakage, no filename guessing.

```
/app/uploads/
  ├── {session_A}/
  │   ├── cafm-sample.xlsx      ← uploaded by implementer
  │   └── contracts/
  │       └── confirmed.json     ← written by AI via write_file
  ├── {session_B}/
  │   └── another-file.xlsx
```

**Impact on save_contract:** Replaced. The `save_contract` MCP tool from the original design is a `write_file` call with a path convention (`{session_id}/contracts/confirmed.json`). The path convention lives in the skill instructions, not in custom tool code. No schema validation at write time — validation happens when the bridge tool sends data to the API (backpressure).

**Impact on excel_analysis:** Fixed. The broken `excel_analysis` tool (which requires a filesystem path) works when the AI discovers the uploaded file via `list_directory` and passes the path as an argument.

**Remaining L2 validation:** Docker compose integration — does LibreChat write uploads to the shared volume path? Does the AI receive uploaded filenames in conversation context? *(Spike validated L1 only — local Python, not Docker.)*

*(Source: LibreChat issues [#10739](https://github.com/danny-avila/LibreChat/issues/10739), [#8060](https://github.com/danny-avila/LibreChat/issues/8060), [PR #11094](https://github.com/danny-avila/LibreChat/pull/11094). Rein transcript 2026-03-11: "before the demo, you need to utilize the upload option." Runtime harness research, session 802ad55e. Updated: Spike validation 2026-03-16.)*

##### 6.3.2 Skills Delivery

LiteLLM does NOT support Agent Skills — `container.skills` and `/v1/skills` are not present in LiteLLM's documentation or codebase. Option A from the prior design pass is invalid.

**System prompt baking** remains viable for the demo: embed skill instructions in the LibreChat agent's system prompt. For Claude Sonnet (demo model), context window is not a constraint. For Qwen 3.5 (production target), baking both skills may exceed the model's reliable instruction-following capacity — one agent per mode is the workaround (implementer selects the right LibreChat agent).

**LibreChat Agent Skills** (the native progressive disclosure feature) is on the Q1 2026 roadmap ([Issue #11106](https://github.com/danny-avila/LibreChat/issues/11106)) with no active PR — likely slips to Q2 2026.

**FastMCP SkillsDirectoryProvider — validated approach (2026-03-16):** FastMCP v3's `ResourcesAsTools` transform solves the skill delivery gap at the MCP layer, independent of the runtime. Skills authored as `SKILL.md` files are served as MCP resources. The transform generates `list_resources` and `read_resource` tools — any tool-capable client (including LibreChat) can discover and load skills without native resource support. Progressive disclosure works: `list_resources` returns names and descriptions only; `read_resource` loads the full recipe on demand.

**Demo vs. production path:** For the demo, system prompt baking remains viable (one skill, Claude Sonnet, large context window). For production with Qwen 3.5, the FastMCP skill provider enables progressive loading — the AI loads only the skill it needs, keeping context lean. This eliminates the need for separate LibreChat agents per mode.

**Behavioral gap (unvalidated):** Skill content delivered as a tool response (not system prompt) may have reduced persistence in very long conversations. For the backpressure loop (2-5 turns), tool response delivery is sufficient. For multi-skill sessions spanning 30+ turns, persistence needs empirical testing.

**Chainlit** handles skills via Python code — the developer programmatically loads skill instructions based on conversation state. Full control, no waiting on upstream features.

**Resolved mechanism — AI-controlled loading (Pattern B):** The system prompt includes short skill descriptions. The AI invokes a `load_skill` tool when it determines the full recipe is needed. The runtime reads the skill file and includes its content in the system prompt for subsequent API calls. The Anthropic API sends `system=` fresh with every request — the runtime can include any combination of loaded skills per turn without framework constraints. This matches Claude Code's native skill behavior: descriptions loaded at startup, full content on invocation, skills accumulate in context.

**LibreChat alternative:** System prompt baking — embed both skill instructions upfront. Viable for Claude (context window accommodates both). For Qwen 3.5 (production target), one LibreChat agent per mode (setup vs. execution) avoids instruction-following degradation. No progressive loading — all instructions present from the start.

##### 6.3.3 Runtime Candidates

Four runtimes were evaluated against the full requirements stack:

| Criterion | LibreChat + FastMCP | OpenWebUI | Chainlit | Dify |
|-----------|-------------------|-----------|----------|------|
| File → MCP tool | ⚠️ Filesystem MCP + shared volume (L1 validated) | ⚠️ Open Terminal + shared volume (needs L2 validation) | ✅ Native `.path` | ⚠️ HTTP only |
| MCP transport | ✅ stdio/SSE/HTTP | ⚠️ Streamable HTTP only — FastMCP `transport="http"` compatible | ✅ stdio/SSE/HTTP | ⚠️ HTTP only |
| Skills loading | ✅ FastMCP SkillsDirectoryProvider (L1 validated) | ✅ Native `view_skill` lazy loading — matches Anthropic best practices | ✅ Code-defined | ⚠️ Workflow nodes |
| Multi-file skills | ✅ SKILL.md + references + scripts | ❌ Single markdown per skill | ✅ Code-defined | ❌ |
| Web UI | ✅ Polished, ChatGPT-like | ✅ Polished, already deployed (IITR) | ⚠️ Framework build | ✅ Visual workflow |
| Non-developer UX | ✅ Best | ✅ Good | ⚠️ Build-dependent | ✅ Good |
| Qwen 3.5 support | ✅ OpenAI-compatible | ✅ OpenAI-compatible | ✅ Any model | ✅ Any model |
| Session isolation | ✅ Convention-based (`{session_id}/` paths) | ❌ Shared `/home/user` — per-user containers planned | ✅ Per-user | ⚠️ Unknown |
| Demo-ready | ⚠️ L2 Docker validation pending | ⚠️ L2 Docker + file chaining validation pending | ✅ Works today | ✅ Works today |

**Rejected:** Claude Agent SDK (no built-in web UI, Anthropic models only), Claude Code (not self-hostable), [PI](https://github.com/badlogic/pi-mono) (author is explicitly anti-MCP — no MCP support by design; best skill-loading of all candidates but dealbreaker for MCP-based pipeline). *(PI evaluated: Extraction pass 2026-03-15.)*

**Re-evaluated (2026-03-17):** OpenWebUI was previously rejected ("MCP via proxy, no file forwarding"). Since v0.6.31 (March 2026), OpenWebUI added native MCP support (Streamable HTTP), native skills with lazy loading (`view_skill` builtin tool), and [Open Terminal](https://docs.openwebui.com/features/extensibility/open-terminal/) (full OS access via Docker container). Re-added as candidate. Key strengths: skills progressive disclosure matches [Anthropic best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) natively. Key weaknesses: no per-session filesystem isolation (shared `/home/user`), file upload → MCP `__files__` parameter bugged on Streamable HTTP (v0.6.41). *(Sources: [OpenWebUI Skills](https://docs.openwebui.com/features/ai-knowledge/skills), [OpenWebUI MCP](https://docs.openwebui.com/features/extensibility/mcp), [Open Terminal](https://docs.openwebui.com/features/extensibility/open-terminal/). Marius, Mar 17 2026.)*

**UX paradigm — conversational, not workflow:** Miguel's demo expectation is the interactive ETL — the AI reads the implementer's data, proposes mappings, and the implementer confirms through conversation. This is free-form conversational, not deterministic workflow. Dify's guided workflow paradigm creates friction for this interaction pattern.

**FastMCP v3 — complementary layer, not runtime:** FastMCP v3 added Client (programmatic MCP client with Anthropic/OpenAI/Gemini sampling handlers), Apps (interactive UIs rendered in conversation), and Elicitation (structured user input mid-tool). These are powerful MCP-layer primitives but explicitly "building blocks for higher-level systems" — not a web UI or conversation runtime. FastMCP provides the MCP server and client; the runtime provides the conversation shell around it.

**Critical path — bridge tool first, then runtime:** The demo blocker is the bridge tool (contract → pipeline execution), not the runtime. Build order: (1) bridge tool, (2) golden example validation with CAFEM contract, (3) backpressure loop testing, (4) skills content. The runtime decision feeds into this path but does not block step 1.

**Runtime Bake-Off — Spike Plan (2026-03-17)**

Two spikes compare the leading candidates side-by-side. Each spike is a one-day issue (spec → implement → deploy). Deliverable = deployed environment the Dev Lead can log into and test. Not code snippets or screenshots — a running thing to witness. Spikes are a JA primitive — the Dev Lead witnesses the deployed spike before the design doc says "validated."

**Gold standard:** Claude Code's native skill + file behavior. Each spike proves how close the alternative gets.

| Spike | Runtime | Deploy | Port |
|-------|---------|--------|------|
| A | LibreChat + FastMCP + Filesystem MCP | Wilsch-AI VPS | `:3020` |
| B | OpenWebUI + Open Terminal + FastMCP MCP | Wilsch-AI VPS | `:3010` |

**Skills criteria — progressive disclosure ([Anthropic best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)):**
- Metadata-only pre-load (name + description, not full content)
- On-demand full content loading via tool mechanism
- Multi-file skill directories (SKILL.md + references)
- AI selects which skill to load based on context

**Filesystem criteria:**

| # | Prove this |
|---|-----------|
| 1 | AI writes files to a session-scoped path |
| 2 | AI discovers uploaded files without being told the exact path |
| 3 | AI chains: discover file → get path → pass to another MCP tool |
| 4 | Two sessions can't see each other's files |

**Exit condition:** Dev Lead witnesses both spikes, comparison documented. Winner becomes demo runtime. If neither passes all criteria, Chainlit remains fallback.

*(Source: Marius, Mar 17 2026 — [#852 comment](https://github.com/DaveX2001/deliverable-tracking/issues/852). Extraction pass 2026-03-17.)*

*(Source: [LibreChat 2026 Roadmap](https://www.librechat.ai/blog/2026-02-18_2026_roadmap). [Chainlit MCP docs](https://docs.chainlit.io/advanced-features/mcp). [Dify v1.6.0 MCP](https://dify.ai/blog/v1-6-0-built-in-two-way-mcp-support). [agentskills.io](https://agentskills.io). Runtime harness research, session 802ad55e.)*

#### 6.4 PSM Persona — Behavioral Layer

The AI's behavioral quality is demo-blocking. Miguel judges whether the AI acts intelligently — readable asset names, proactive semantic insights, no process leakage, no template-feeling rigidity.

**Design approach:** The persona uses Anthropic's Persona Selection Model (PSM). Instead of procedural rules ("do X, then Y"), PSM derives core beliefs from the question "what kind of person would naturally do this well?" — then lets those beliefs guide behavior. Empirical evidence from CCI #629 showed that beliefs hold under pressure while rules get reinterpreted. The persona stays constant across both skills (§6.2) — the active skill changes the procedure, not the disposition.

**Undefined:** The persona's current beliefs and identity. Three PSM passes have been completed on the ARCHIBUS Setup agent (#1094), but the beliefs are still iterating. The authoritative source is the system prompt (`prompts/system-prompt.md`), not this design doc.

*(Source: [Anthropic PSM](https://alignment.anthropic.com/2026/psm/). [CCI #629](https://github.com/DaveX2001/claude-code-improvements/issues/629) — PSM methodology. Session `9e7fda08` — ARCHIBUS persona design. Session `24d94df5` — belief #5 addition. Issue [#1094](https://github.com/DaveX2001/deliverable-tracking/issues/1094).)*

---

## Source

- **Design docs:**
  - [Chain 1B: Bulk Entry Design](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-bulk-entry-design) (parent router)
  - [Step 3: Fill Level-by-Level](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/chain-1b-step3-design)
- **Transcripts:**
  - [AI Data Entry Demo (Feb 23, 2026)](/Users/verdant/Downloads/AI Data Entry Demo Transcript.txt)
- **Data artifacts:**
  - [AssetImportDescription (36 fields)](https://docs.google.com/spreadsheets/d/12xs98WKdpTLHz8U6mccOXo5clFwCqvOviuEch_VNMTw/edit)
  - [Housekeeping Tracker (FMM)](/Users/verdant/Downloads/Housekeeping Tracker Jan 2026 v1.xlsb) — second Excel sample for hierarchy inference testing
- **Issue:** [#852](https://github.com/DaveX2001/deliverable-tracking/issues/852)
- **Project Index:** [#373](https://mariuswilsch.github.io/public-wilsch-ai-pages/project/archibus-fm-assistant/transcript-index-373)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/7d4d0137-c41c-4df3-835f-0218a90eba19.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/919879a0-f08e-4ce4-acfd-8ef68c39ef55.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch__archibus-bulk-import/23cb9666-8d53-4479-ab6c-e775b7020083.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch__archibus-bulk-import/58bfde61-b97e-45fe-9a57-c8bdc231b7fe.jsonl
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/57d480dc-e7ea-4ee3-b96f-dce4a2e6f4ca.jsonl
- **Transcript:** [Rein <> Marius (Feb 27, 2026)](https://app.fireflies.ai/view/01KJFJ49ZFV2VWS7E811W7DJ6Z)
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/b1add1e7-2807-4c2b-9cc7-1df0be954eb9.jsonl
- **Reference:** [Anthropic Skill Best Practices — Progressive Disclosure](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- **Reference implementation:** [ARCHIBUS POC](https://github.com/MariusWilsch/ARCHIBUS__archibus-poc) — FastMCP server pattern, LibreChat integration, auth middleware, Docker deployment
- **Reference:** [FastMCP v3 Documentation](https://gofastmcp.com/) — generate-cli, providers, authentication
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/71694ab4-1303-48d1-8912-c2193b65802f.jsonl
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/986d5873-591d-4309-b641-7feacf9378ef.jsonl
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/0914cc69-d3b3-4937-838c-d3140990c480.jsonl
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/831e4df9-3e17-4516-87f3-e27d5cc65115.jsonl
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/0ffc456e-f9f5-49dc-bcd1-cb55c815b788.jsonl
- **Transcript:** [AI Data Entry Demo (Feb 23, 2026)](https://github.com/user-attachments/files/25829896/AI.Data.Entry.Demo.Transcript.-.Meeting.Report.txt) — Miguel's ETL expectations, audience criteria, terminology ("container" not "phantom")
- **Reference:** [Anthropic PSM — Persona Selection Model](https://alignment.anthropic.com/2026/psm/) — behavioral layer for prompt architecture
- **Reference:** [Anthropic Prompt Engineering Course](https://www.youtube.com/watch?v=ysPbXH0LpIE) — 10-section mechanical prompt recipe
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/d39c2d40-a2af-4063-ae8e-ebacd36a83ae.jsonl
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/ff776065-b200-4fda-98bf-c957873ae951.jsonl
- **Transcript:** [Rein <> Marius (Mar 11, 2026)](https://app.fireflies.ai/view/01KKE7GE79F1AYWE4AJCCEHYFK) — Step 2b enum resolution, extended properties, asset types scope, demo requirements
- **Reference:** [Anthropic Skills Guide PDF](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf) — Category 3 MCP Enhancement pattern, progressive disclosure architecture
- **Reference:** [LiteLLM Skills docs](https://docs.litellm.ai/docs/skills) — `container.skills` parameter, `/v1/skills` endpoint
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/f2a480c1-4a8c-497b-9d1f-ea898e84ce00.jsonl
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/802ad55e-fa15-4e0d-ad65-b05ced810e1c.jsonl
- **Reference:** [agentskills.io specification](https://agentskills.io/specification) — Agent Skills open standard, 32+ tool implementations
- **Reference:** [LibreChat Agent Skills Issue #11106](https://github.com/danny-avila/LibreChat/issues/11106) — Q1 2026 roadmap, no PR as of March 2026
- **Reference:** [LibreChat file→MCP gap Issue #8060](https://github.com/danny-avila/LibreChat/issues/8060) — signed URL proposal, unresolved
- **Reference:** [Chainlit MCP documentation](https://docs.chainlit.io/advanced-features/mcp) — native stdio/SSE/HTTP, `.path` attribute
- **Reference:** [Dify v1.6.0 two-way MCP](https://dify.ai/blog/v1-6-0-built-in-two-way-mcp-support) — HTTP transport only
- **Reference:** [Chainlit Ask User API](https://docs.chainlit.io/advanced-features/ask-user) — AskFileMessage, AskElementMessage (consent-gated forms)
- **Reference:** [Chainlit Commands](https://docs.chainlit.io/concepts/command) — dynamic UI actions, set_commands()
- **Reference:** [Chainlit MCP Cookbook](https://github.com/Chainlit/cookbook/tree/main/mcp) — end-to-end MCP + Claude integration example
- **Reference:** [FastMCP v3 Apps](https://gofastmcp.com/apps/overview) — interactive UIs rendered in conversation
- **Reference:** [FastMCP v3 Sampling](https://gofastmcp.com/clients/sampling) — built-in Anthropic/OpenAI/Gemini handlers
- **Session:** /Users/verdant/.claude/projects/-Users-verdant-Documents-projects-billable-MariusWilsch--archibus-bulk-import/82c5d45b-eb25-4f94-a806-e081fc46d0cb.jsonl
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/73f6fb9f-3b9f-46d2-9da7-473a46f593d3.jsonl
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/a9e661cd-8665-48d9-84f9-fb97a68e973d.jsonl
- **Spike:** FastMCP SkillsDirectoryProvider + ResourcesAsTools (L1 validated, 2026-03-16)
- **Spike:** Filesystem MCP + shared volume simulation (L1 validated, 2026-03-16)
- **Session:** /Users/daveFem/.claude/projects/-Users-daveFem-Desktop-claude-projects-01-ARCHIBUS--deliverable/fabb74be-8370-4ad2-8074-b7b1b207a765.jsonl
- **Reference:** [OpenWebUI Skills](https://docs.openwebui.com/features/ai-knowledge/skills) — native `view_skill` lazy loading, markdown-based skills
- **Reference:** [OpenWebUI MCP](https://docs.openwebui.com/features/extensibility/mcp) — Streamable HTTP only, native since v0.6.31
- **Reference:** [OpenWebUI Open Terminal](https://docs.openwebui.com/features/extensibility/open-terminal/) — full OS access via Docker
- **Reference:** [mcpo proxy](https://github.com/open-webui/mcpo) — stdio→HTTP bridge, v0.0.20, production-ready
