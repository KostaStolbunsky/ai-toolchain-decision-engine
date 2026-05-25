# Type Registry: Price Signals

> Type ID: `price_signal`  
> Version: 0.1  
> Date: 2026-05-25  
> Used by: tool-registry schema (`price_signal` field)

Defines a relative cost signal for use in the EvaluationFrame and recommendation UI. This is a coarse signal — not a precise price. Precise pricing is described in the free-text `paid_tiers` field.

## Values

| ID | Label | Description | Status |
|---|---|---|---|
| `low` | Low | Free tier available or total cost well below category average. Accessible to individuals and small teams. | active |
| `medium` | Medium | Paid product with reasonable pricing. Accessible to teams and small organisations with a budget. | active |
| `high` | High | Enterprise pricing, per-seat licensing, or significant usage costs at scale. Requires budget approval. | active |

## Relationship to `cost-tco` criterion score

`price_signal` is a registry-level field for UI filtering and rough sorting. `criteria_scores.cost-tco` is the evaluated score used in the scoring engine. They are related but not identical — a tool may have `price_signal: medium` but score `cost-tco: high` if its TCO including setup and maintenance is low.

## Extension rules

Candidate additions (not yet active):
- `variable` — cost is highly unpredictable (e.g. pure token-based with no cap)
- `open_source` — no cost, but self-hosting effort applies

Add new values here only. No other files need to change.
