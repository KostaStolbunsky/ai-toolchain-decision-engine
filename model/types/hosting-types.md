# Type Registry: Hosting Types

> Type ID: `hosting_type`  
> Version: 0.1  
> Date: 2026-05-25  
> Used by: tool-registry schema (`hosting` field), METAMODEL Implementation entity

Defines where a tool's infrastructure runs.

## Values

| ID | Label | Description | Status |
|---|---|---|---|
| `cloud` | Cloud | Fully managed by the vendor. No infrastructure required from the user. | active |
| `self_hosted` | Self-Hosted | User runs the tool on their own infrastructure. Full control, full responsibility. | active |
| `hybrid` | Hybrid | Core functionality is cloud-managed; certain components (data, agents, runners) can be self-hosted. | active |

## Scoring implications

`hosting_type` is used as a filter input in the EvaluationFrame. Organisations with strict data residency requirements may apply an `exclude` override for `cloud` entries.

## Extension rules

Candidate additions (not yet active):
- `edge` — runs at the edge (CDN nodes, device-local)
- `air_gapped` — runs in fully isolated, offline environments

Add new values here only. No other files need to change.
