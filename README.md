# QFD — House of Quality · Claude Skill

A Claude skill that generates **House of Quality (QFD) worksheets** — either as a self-contained interactive HTML tool, or as a pre-filled JSON file ready to import, or both at once.

---

## What is QFD?

**Quality Function Deployment (QFD)** is a structured methodology for translating customer needs ("voice of the customer") into specific engineering requirements. The **House of Quality** is its primary tool — a matrix that maps customer requirements against product attributes to prioritise engineering effort.

---

## Two output modes

### 1 · Interactive HTML tool

A fully featured worksheet that runs in any browser — no install, no server.

| Section | Description |
|---|---|
| Project header | Editable title, product/project, team, date, version |
| Correlation roof | Click to set `+` (synergy) or `−` (trade-off) between attributes |
| Customer requirements | Editable rows with importance weights (must sum to 100%) |
| Engineering attributes | Editable columns with unit and optimisation direction (↑ ↓ ●) |
| Relationship matrix | Click to cycle 9 → 3 → 1 → · per the standard QFD strength scale |
| Competitive evaluation | 1–7 ratings per competitor and new design |
| Priority Score | Auto-calculated weighted sum per engineering attribute |
| Relative Weight | Normalised percentage with mini bar chart |
| Design Target | Editable target value per attribute |
| Import / Export JSON | Save and restore full session state |
| Default Template | One-click reset to the built-in car door example |
| Clear All | Modal confirmation before wiping all data |
| Methodology & Formulas | Collapsible reference panel with all six QFD formulas |

### 2 · Pre-filled JSON data file

Ask Claude to generate a QFD dataset for any product or domain. Claude reasons through:
- Customer requirements with calibrated importance weights (summing to 100)
- Engineering attributes with units and optimisation directions
- Relationship matrix (9 / 3 / 1)
- Correlation roof (+1 / −1)
- Competitor and new-design ratings (1–7)
- Design targets

The resulting `.json` file imports directly into the HTML tool via **⬆ Import JSON**.

**Example prompts:**
```
"Generate a QFD JSON for a smartphone camera"
"Create a House of Quality dataset for a hospital bed"
"Build a QFD for a B2B SaaS onboarding experience"
"Give me both the HTML tool and a pre-filled JSON for an electric bicycle"
```

---

## File structure

```
qfd-house-of-quality/
├── SKILL.md                   ← Skill definition: triggers, instructions, JSON schema
├── README.md                  ← This file
└── docs/
    ├── house_of_quality.html  ← Complete self-contained interactive QFD tool
    └── template.json          ← Car door example — importable reference dataset
```

---

## JSON format

The schema used by both the tool's export and the skill's generation:

```json
{
  "title": "House of Quality — Product Name",
  "meta": { "product": "", "team": "", "date": "YYYY-MM-DD", "version": "v1.0" },
  "customerReqs": [
    { "id": 0, "name": "Requirement", "importance": 25 }
  ],
  "engineeringAttrs": [
    { "id": 0, "name": "Attribute", "unit": "unit", "dir": "↑" }
  ],
  "relationships": { "0-0": 9, "0-1": 3 },
  "correlations":  { "0-1": -1 },
  "competitors": [
    { "id": 0, "name": "Competitor A", "ratings": { "0": 5 } }
  ],
  "newDesign": { "0": 4 },
  "targets":   { "0": "≤ 18 lbs" }
}
```

See `docs/template.json` for a complete working example (car door).

---

## Formulas

```
Priority(j)  = Σ [ Importance(i) × Relationship(i,j) ]
Weight(j)    = Priority(j) ÷ Σ Priority × 100%
Σ Importance = 100%

Relationship : 9 = Strong · 3 = Moderate · 1 = Weak
Competition  : 1 (not addressed) → 7 (fully satisfied)
Correlation  : +1 (synergy)  ·  −1 (trade-off)
```

---

## Installation

### As a Claude skill

Copy the `qfd-house-of-quality/` folder into your Claude skills directory (e.g. `/mnt/skills/user/`). Claude picks it up automatically from `SKILL.md`.

**Trigger phrases:** "create a House of Quality", "QFD matrix", "voice of the customer worksheet", "generate QFD data for [product]", etc.

### Standalone (no Claude required)

Open `docs/house_of_quality.html` in any modern browser. Use `docs/template.json` as a starting point — edit it, then import via **⬆ Import JSON**.

---

## Reference

Based on the QFD methodology as described in:

> Hill, C.W.L. & Rothaermel, F.T. — *Managing the New Product Development Process*, Chapter 11 (House of Quality for a Car Door example).

---

## License

MIT — free to use, modify, and share.
