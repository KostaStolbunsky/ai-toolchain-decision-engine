# Tool Registry

> Version: 0.3  
> Status: Draft  
> Date: 2026-05-25  
> xleo task ref: STO-35

This document defines the schema for registering concrete tools as implementations of abstract nodes. All tool data lives in `registry/tools/` as individual YAML files — one file per tool.

All type references in this schema point to `model/types/`. No enum values are defined inline.

---

## Storage format

- **Format:** YAML, one file per tool
- **Location:** `registry/tools/<tool-id>.yaml`
- **Naming:** kebab-case, e.g. `perplexity.yaml`, `claude-code.yaml`
- **Versioning:** changes to existing entries require updating `last_verified`; schema changes require bumping `schema_version`
- **Additions:** new tools are added via PR or autonomous agent pipeline; see contribution guide below

---

## Schema

```yaml
schema_version: string        # registry schema version this entry conforms to

id: string                    # unique tool identifier, kebab-case
label: string                 # official product/tool name
node_id: string               # $ref: model/nodes.md — which abstract node this implements
website: url                  # official product URL

## Lifecycle
status: string                # $ref: model/types/lifecycle-statuses.md
data_source: string           # $ref: model/types/data-sources.md
contributed_by: string        # agent ID, GitHub username, or "maintainer"
last_verified: date           # ISO 8601 — when entry data was last verified

## Identity
hosting: string               # $ref: model/types/hosting-types.md
open_source: boolean
license: string               # SPDX identifier or "proprietary"

## Access
api_access: boolean
api_docs: url
model_agnostic: boolean

## Pricing
pricing_model: string         # free | freemium | subscription | usage_based | enterprise | open_source
free_tier: boolean
free_tier_limits: string
paid_tiers: string
price_signal: string          # $ref: model/types/price-signals.md

## Compliance & Geography
data_residency: [string]
compliance_certifications: []
no_training_on_data: boolean

## Integrations
integrations: []

## Criteria scores
criteria_scores:
  [criteria_id]: string       # $ref: model/types/score-values.md
  # include only criteria applicable to the parent node

## Notes
notes: string
```

---

## Lifecycle states

See [`model/types/lifecycle-statuses.md`](types/lifecycle-statuses.md) for all valid values, transition rules, and extension guide.

Only `active` entries are included in scoring.

---

## Data sources

See [`model/types/data-sources.md`](types/data-sources.md) for all valid values and trust model.

---

## Criteria score values

See [`model/types/score-values.md`](types/score-values.md) for all valid values and numeric mappings.

---

## Contribution guide

1. Fork the repository
2. Create or update a YAML file in `registry/tools/`
3. Set `status: draft` and `data_source: user_submission`
4. Set `contributed_by` to your GitHub username
5. Open a pull request — the review checklist will be applied

The autonomous agent creates entries as `agent_discovery`. All sources go through the same verification pipeline. Humans retain final override on any agent decision.

---

## Versioning rules

| Change type | Action |
|---|---|
| Add new optional field | Update schema, bump minor version |
| Rename or remove field | Update schema, bump major version, migrate all entries |
| Add new type value | Update relevant file in `model/types/` only — no schema change |
| Update a tool entry | Update `last_verified`, no schema bump needed |

---

## Open questions

- [ ] Should `criteria_scores` carry evidence/source references, or rating only?
- [ ] Should tools spanning multiple nodes be registered once or once per node?
- [ ] What is the mandatory verification checklist for `agent_discovery` entries before promotion to `active`?
