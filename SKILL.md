---
name: qfd-house-of-quality
description: >
  Generates House of Quality (QFD) worksheets — either as a self-contained interactive HTML tool
  or as a pre-filled JSON file that can be imported into the tool. Use this skill whenever the
  user mentions Quality Function Deployment, QFD, House of Quality, voice of the customer,
  engineering prioritisation, or wants to map customer requirements to technical attributes.
  Also trigger when someone asks to build a product planning matrix, competitor benchmarking
  worksheet, design trade-off analysis, or says things like "create a QFD for my product",
  "fill in a House of Quality for [domain]", or "generate QFD data for [product]".
  Always use this skill for any QFD-related request — even if the user only describes a product
  and asks Claude to reason about requirements and attributes.
---

# QFD — House of Quality Skill

Produces House of Quality worksheets in two complementary output modes:

| Mode | When to use | Output |
|---|---|---|
| **HTML tool** | User wants the interactive worksheet to use themselves | `house_of_quality.html` |
| **JSON data** | User describes a product/domain and wants it pre-filled | `<project>.json` importable into the HTML |
| **Both** | User wants a ready-to-open worksheet with their data already loaded | HTML + JSON |

---

## Mode 1 — Deliver the HTML tool

1. Copy `assets/house_of_quality.html` to `/mnt/user-data/outputs/house_of_quality.html`
2. Use `present_files` to share it
3. Tell the user: open in any browser, use **Import JSON** to load a pre-filled dataset

---

## Mode 2 — Generate a QFD JSON file

When the user describes a product, domain, or scenario, reason through it and produce a
complete JSON file matching the schema below. The file can be imported into the HTML tool via **Import JSON**.

### Step-by-step reasoning process

**1. Identify customer requirements (5–8 recommended)**
- What do end-users care about? Think across: performance, usability, reliability, aesthetics, cost, safety
- Assign importance weights that sum **exactly to 100**
- Order from highest to lowest importance

**2. Identify engineering attributes (4–7 recommended)**
- What technical levers does the design team control?
- Each attribute must be measurable (include a unit)
- Set direction: `↑` (maximise), `↓` (minimise), `●` (hit a target value)

**3. Fill the relationship matrix**
- For every (requirement, attribute) pair, ask: does this attribute directly affect this requirement?
- Use `9` (strong), `3` (moderate), `1` (weak), omit the key entirely for no relationship
- Aim for sparse-to-moderate fill — too many 9s means poor differentiation

**4. Fill correlations (roof)**
- For every pair of engineering attributes: do they help or conflict with each other?
- Use `1` (positive synergy) or `-1` (negative trade-off), omit for no interaction
- Key format: always `"<lower_id>-<higher_id>"` (e.g. `"0-2"` not `"2-0"`)

**5. Add competitors and new design ratings (1–7 scale)**
- 1 = requirement barely addressed, 7 = fully satisfied
- Rate honestly — gaps drive the value of the analysis

**6. Set design targets**
- Quantified goal for each engineering attribute (e.g. `"≤ 2.5 kg"`, `"≥ 95%"`)

### JSON Schema

```jsonc
{
  "title": "House of Quality — <Product Name>",
  "meta": {
    "product": "<Product / Project name>",
    "team":    "<Team or author>",
    "date":    "<YYYY-MM-DD>",
    "version": "v1.0"
  },

  // Customer requirements — ids must be sequential integers starting at 0
  "customerReqs": [
    { "id": 0, "name": "<requirement>", "importance": <1-100> },
    // importance values MUST sum to exactly 100
  ],

  // Engineering attributes — ids must be sequential integers starting at 0
  "engineeringAttrs": [
    { "id": 0, "name": "<attribute>", "unit": "<unit>", "dir": "↑" }
    // dir: "↑" maximise | "↓" minimise | "●" target value
  ],

  // Relationship matrix — only include non-zero cells
  // Key format: "<customerReq id>-<engineeringAttr id>"
  // Values: 9 (strong), 3 (moderate), 1 (weak)
  "relationships": {
    "0-0": 9,
    "0-1": 3
  },

  // Correlation roof — only include non-zero pairs
  // Key format: "<lower attr id>-<higher attr id>" (always lower first)
  // Values: 1 (positive synergy), -1 (negative / trade-off)
  "correlations": {
    "0-1": -1,
    "1-2":  1
  },

  // Competitors — add as many as relevant
  "competitors": [
    {
      "id": 0,
      "name": "Competitor A",
      // ratings keys are customerReq ids as strings; values 1-7
      "ratings": { "0": 5, "1": 4 }
    }
  ],

  // New design ratings — customerReq ids as strings; values 1-7
  "newDesign": { "0": 4 },

  // Design targets — engineeringAttr ids as strings; free-text value
  "targets": { "0": "≤ 18 lbs" }
}
```

### Validation checklist before writing the file

- [ ] `customerReqs[*].importance` sums to exactly 100
- [ ] All `customerReqs` ids are `0, 1, 2, ...` (no gaps)
- [ ] All `engineeringAttrs` ids are `0, 1, 2, ...` (no gaps)
- [ ] Every relationship key references valid req and attr ids
- [ ] Correlation keys use `"<lower>-<higher>"` order
- [ ] Every competitor has a `ratings` entry for all req ids
- [ ] `newDesign` has an entry for every req id
- [ ] `targets` has an entry for every attr id

### Output the file

Save to `/mnt/user-data/outputs/<project-slug>.json` and present with `present_files`.
Tell the user: open `house_of_quality.html` in a browser, click **Import JSON**, and select this file.

---

## Mode 3 — Both outputs together

1. Generate the JSON (Mode 2)
2. Copy the HTML to outputs (Mode 1)
3. Present both files with `present_files`

---

## QFD Calculation Reference

| Formula | Description |
|---|---|
| `Priority(j) = Σ [ Importance(i) × Relationship(i,j) ]` | Weighted sum across all customer rows for column j |
| `Weight(j) = Priority(j) ÷ Σ Priority × 100%` | Normalised relative importance |
| `Σ Importance = 100%` | Importance weights must sum to 100 |
| Relationship scale: 9 / 3 / 1 | Strong / Moderate / Weak (geometric, not linear) |
| Competitive rating: 1–7 | 1 = not addressed · 7 = fully satisfied |
| Correlation: +1 / −1 | Positive synergy / Trade-off / conflict |

---

## Asset inventory

| File | Purpose |
|---|---|
| `assets/house_of_quality.html` | Complete self-contained interactive QFD tool |
| `assets/template.json` | Car door example — reference for JSON structure and import testing |
