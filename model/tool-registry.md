# Tool Registry

> Version: 0.1  
> Status: Draft  
> Date: 2026-05-25  
> xleo task ref: STO-35

This document defines the schema for registering concrete tools as implementations of abstract nodes. All tool data lives in `registry/tools/` as individual YAML files — one file per tool.

---

## Storage format

- **Format:** YAML, one file per tool
- **Location:** `registry/tools/<tool-id>.yaml`
- **Naming:** kebab-case, e.g. `perplexity.yaml`, `claude-code.yaml`
- **Versioning:** changes to existing entries require updating `last_reviewed` and bumping the file-level `schema_version` if the schema itself changed
- **Additions:** new tools are added via PR; see `CONTRIBUTING.md` for review criteria

---

## Schema

```yaml
schema_version: string        # registry schema version this entry conforms to

id: string                    # unique tool identifier, kebab-case
label: string                 # official product/tool name
node_id: string               # which abstract node this implements (from nodes.md)
website: url                  # official product URL
last_reviewed: date           # ISO 8601 — when this entry was last verified

## Identity
hosting: enum                 # cloud | self_hosted | hybrid
open_source: boolean
license: string               # SPDX identifier or "proprietary"

## Access
api_access: boolean           # programmatic API available (not UI-only)
api_docs: url                 # link to API documentation, if available
model_agnostic: boolean       # can swap underlying AI model

## Pricing
pricing_model: enum           # free | freemium | subscription | usage_based | enterprise | open_source
free_tier: boolean
free_tier_limits: string      # brief description of free tier limits
paid_tiers: string            # brief description of paid options
price_signal: enum            # low | medium | high   (relative cost signal for EvaluationFrame)

## Compliance & Geography
data_residency: [string]      # e.g. ["EU", "US"] — where data can be stored
compliance_certifications: [] # e.g. ["SOC2", "ISO27001", "GDPR", "HIPAA"]
no_training_on_data: boolean  # vendor commitment not to train on user data

## Integrations
integrations: []              # named systems this tool integrates with natively

## Criteria scores
# Score each criterion applicable to the parent node: high | medium | low
criteria_scores:
  security-compliance: enum   # high | medium | low
  integration-depth: enum
  observability: enum
  cost-tco: enum
  speed-delivery: enum
  # include only criteria applicable to the node; omit others

## Notes
notes: string                 # free-text caveats, known limitations, or important context
```

---

## Criteria score definitions

| Score | Meaning |
|---|---|
| `high` | Strongly satisfies this criterion — minimal risk or friction |
| `medium` | Partially satisfies — acceptable with caveats |
| `low` | Weak or unverified — significant risk or friction |

---

## Versioning rules

| Change type | Action |
|---|---|
| Add new optional field | Update schema, bump minor version |
| Rename or remove field | Update schema, bump major version, migrate all entries |
| Add new enum value | Update schema docs, no migration needed |
| Update a tool entry | Update `last_reviewed`, no schema bump needed |

---

## Open questions

- [ ] Should `criteria_scores` be structured scores (with evidence/source) or just a rating?
- [ ] Should tools with `node_id` spanning multiple nodes be registered once or once per node?
- [ ] How do we handle tools that change pricing or compliance status frequently?
