# Tool Registry

> Version: 0.2  
> Status: Draft  
> Date: 2026-05-25  
> xleo task ref: STO-35

This document defines the schema for registering concrete tools as implementations of abstract nodes. All tool data lives in `registry/tools/` as individual YAML files — one file per tool.

---

## Storage format

- **Format:** YAML, one file per tool
- **Location:** `registry/tools/<tool-id>.yaml`
- **Naming:** kebab-case, e.g. `perplexity.yaml`, `claude-code.yaml`
- **Versioning:** changes to existing entries require updating `last_verified` and bumping the file-level `schema_version` if the schema itself changed
- **Additions:** new tools are added via PR or autonomous agent pipeline; see contribution guide below

---

## Schema

```yaml
schema_version: string        # registry schema version this entry conforms to

id: string                    # unique tool identifier, kebab-case
label: string                 # official product/tool name
node_id: string               # which abstract node this implements (from nodes.md)
website: url                  # official product URL

## Lifecycle
status: enum                  # draft | active | deprecated
data_source: enum             # agent_discovery | user_submission | manual
contributed_by: string        # agent ID, GitHub username, or "maintainer"
last_verified: date           # ISO 8601 — when entry data was last verified against the actual product

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

## Lifecycle states

| Status | Meaning |
|---|---|
| `draft` | Entry created but not yet verified — not used by scoring engine |
| `active` | Verified and live — used by scoring engine |
| `deprecated` | Tool no longer recommended or discontinued — excluded from scoring, retained for history |

Only `active` entries are included in scoring.

---

## Data sources

| Source | Meaning |
|---|---|
| `agent_discovery` | Entry created or updated by the autonomous registry agent |
| `user_submission` | Submitted by a community contributor via pull request or submission form |
| `manual` | Created directly by a maintainer |

All sources go through the same verification pipeline. Source is recorded for traceability and conflict-of-interest review.

---

## Criteria score definitions

| Score | Meaning |
|---|---|
| `high` | Strongly satisfies this criterion — minimal risk or friction |
| `medium` | Partially satisfies — acceptable with caveats |
| `low` | Weak or unverified — significant risk or friction |

---

## Contribution guide

Anyone can propose a new tool or update an existing entry:

1. Fork the repository
2. Create or update a YAML file in `registry/tools/`
3. Set `status: draft` and `data_source: user_submission`
4. Set `contributed_by` to your GitHub username
5. Open a pull request — the review checklist will be applied automatically

The autonomous agent may also create entries directly as `agent_discovery`. These follow the same schema and go through the same verification pipeline.

Maintainers and the agent have the right to promote entries to `active`, `deprecated`, or reject submissions. Humans retain final override on any agent decision.

---

## Versioning rules

| Change type | Action |
|---|---|
| Add new optional field | Update schema, bump minor version |
| Rename or remove field | Update schema, bump major version, migrate all entries |
| Add new enum value | Update schema docs, no migration needed |
| Update a tool entry | Update `last_verified`, no schema bump needed |

---

## Open questions

- [ ] Should `criteria_scores` be structured scores (with evidence/source) or just a rating?
- [ ] Should tools with `node_id` spanning multiple nodes be registered once or once per node?
- [ ] How do we handle tools that change pricing or compliance status frequently — automated staleness detection?
- [ ] What is the verification pipeline for `agent_discovery` entries — which checks are mandatory before promoting to `active`?
