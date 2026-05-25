# Type Registry: Score Values

> Type ID: `score_value`  
> Version: 0.1  
> Date: 2026-05-25  
> Used by: tool-registry `criteria_scores` fields, scoring algorithm (model/scoring.md)

Defines the three-point qualitative scale used to score tool implementations against evaluation criteria. Also defines the numeric mapping used by the scoring engine.

## Values

| ID | Label | Numeric value | Description | Status |
|---|---|---|---|---|
| `high` | High | `1.0` | Strongly satisfies this criterion. Minimal risk or friction. | active |
| `medium` | Medium | `0.5` | Partially satisfies. Acceptable with known caveats. | active |
| `low` | Low | `0.0` | Weak or unverified. Significant risk or friction present. | active |

## Numeric mapping

The scoring engine reads `score_value` IDs from registry entries and maps them to numeric values as defined above. The mapping is defined here and referenced by `model/scoring.md` — it is not hardcoded in the engine.

If the scale is extended (e.g. adding `very_high: 1.2` for boosted scores), only this file and `model/scoring.md` need to change.

## Extension rules

The three-point scale is intentionally simple. Extensions should be considered carefully — adding granularity increases registry maintenance burden.

Candidate additions (not yet active):
- `verified_high` — `high` with third-party evidence on file
- `unknown` — not yet assessed; treated as `low` by the engine until evaluated

Add new values here only.
