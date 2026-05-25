# Type Registry: Confidence Levels

> Type ID: `confidence_level`  
> Version: 0.1  
> Date: 2026-05-25  
> Used by: scoring algorithm (model/scoring.md), Recommendation entity (METAMODEL.md)

Defines the confidence levels assigned to a Recommendation by the scoring engine. Confidence reflects how clearly one candidate outperforms others given the current EvaluationFrame and candidate pool.

## Values

| ID | Label | Conditions | `review_required` default | Status |
|---|---|---|---|---|
| `high` | High | ≥3 candidates evaluated AND winner score ≥0.7 AND gap ≥0.15 | `false` | active |
| `medium` | Medium | ≥2 candidates AND winner score ≥0.5 AND gap ≥0.05 | `false` | active |
| `low` | Low | <2 candidates OR winner score <0.5 OR gap <0.05 | `true` | active |

**Gap** = `score[rank_1] − score[rank_2]`. If only one candidate survived Phase 1, gap = `1.0` but confidence is capped at `medium`.

## Thresholds

All numeric thresholds (0.7, 0.5, 0.15, 0.05) are defined here. The scoring engine reads from this registry — they are not hardcoded in `model/scoring.md`.

Changing a threshold = updating this file only.

## Extension rules

Candidate additions (not yet active):
- `insufficient_data` — not enough registry entries exist for this node to produce a meaningful ranking
- `conflicting_overrides` — overrides produced contradictory results; human resolution required

Add new values here only. No other files need to change.
