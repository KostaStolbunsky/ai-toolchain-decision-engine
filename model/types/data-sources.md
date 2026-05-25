# Type Registry: Data Sources

> Type ID: `data_source`  
> Version: 0.1  
> Date: 2026-05-25  
> Used by: tool-registry schema (`data_source` field)

Defines how a registry entry was created or last updated. Used for traceability, trust scoring, and conflict-of-interest review.

## Values

| ID | Label | Description | Trust level | Status |
|---|---|---|---|---|
| `agent_discovery` | Agent Discovery | Entry created or updated by the autonomous registry agent via its discovery pipeline. | verified | active |
| `user_submission` | User Submission | Submitted by a community contributor via PR or submission form. Goes through the same verification pipeline as agent entries. | unverified until pipeline passes | active |
| `manual` | Manual | Created directly by a maintainer. Treated as trusted by default. | trusted | active |

## Trust model

All sources go through the same verification pipeline before an entry reaches `active` status. The `data_source` field is retained permanently for audit purposes — it reflects the **origin** of the entry, not its current trust level.

Conflict-of-interest checks apply most strictly to `user_submission` entries.

## Extension rules

New sources may be added when a new entry origin type emerges. Candidate examples (not yet active):
- `partner_submission` — submitted by a verified partner organisation
- `api_sync` — synced automatically from a trusted external data provider
- `import` — bulk-imported from a legacy dataset

Add new values here only. No other files need to change.
