# Scoring & Ranking Logic

> Version: 0.1  
> Status: Draft  
> Date: 2026-05-25  
> xleo task ref: STO-36  
> Depends on: METAMODEL.md, model/criteria.md, model/tool-registry.md

This document defines how the ATDE engine converts a populated `ContextProfile` and `EvaluationFrame` into a scored `Recommendation`. The algorithm is rule-based and fully deterministic — no AI inference in the scoring pipeline itself.

---

## Overview

Scoring happens in four sequential phases:

```
Phase 1: Override filtering     ← hard rules, binary exclusion/requirement
Phase 2: Score calculation      ← weighted sum per implementation
Phase 3: Ranking                ← sort by score, identify winner and alternatives
Phase 4: Confidence & flags     ← assess certainty, set review_required if needed
```

No phase is skipped. Each phase produces output that feeds the next.

---

## Phase 1 — Override filtering

Overrides are hard rules defined in the `EvaluationFrame`. They run **before** any score is calculated and can eliminate implementations entirely.

### Override actions

| Action | Effect |
|---|---|
| `exclude` | Implementation is removed from the candidate pool entirely. Cannot be recovered by score. |
| `require` | All implementations that do NOT satisfy this criterion are excluded. |
| `boost` | Adds +0.15 to the final score of qualifying implementations (applied in Phase 3). |
| `penalize` | Subtracts -0.15 from the final score of qualifying implementations (applied in Phase 3). |

### Conflict resolution

When multiple overrides apply to the same implementation:

1. `exclude` always wins — if any override excludes an implementation, it is out regardless of other rules.
2. `require` is evaluated next — if the implementation fails a `require` condition, it is excluded.
3. `boost` and `penalize` are additive — multiple boosts or penalties stack, but the final adjusted score is clamped to `[0.0, 1.0]`.
4. If two overrides conflict (e.g. `boost` and `penalize` on the same criterion), both are applied and they partially cancel out.
5. Override order within the same action type follows declaration order in the EvaluationFrame.

### Output of Phase 1

A filtered candidate pool: implementations that passed all `exclude` and `require` rules.

---

## Phase 2 — Score calculation

### Score mapping

Criteria scores in the registry use a three-point scale. They are mapped to numeric values:

| Registry score | Numeric value |
|---|---|
| `high` | 1.0 |
| `medium` | 0.5 |
| `low` | 0.0 |

### Weighted sum formula

For each implementation that survived Phase 1:

```
raw_score = Σ (criteria_weight[i] × criteria_score_numeric[i])
            for all criteria applicable to the node
```

Where:
- `criteria_weight[i]` comes from the `EvaluationFrame.weights` (or default weights from `criteria.md` if no custom frame)
- `criteria_score_numeric[i]` is the mapped numeric value from the registry entry
- Weights must sum to 1.0 — enforced at EvaluationFrame creation

### Score range

`raw_score` is always in `[0.0, 1.0]` before boost/penalize adjustments.

After boost/penalize from Phase 1 overrides, the score is clamped:
```
final_score = clamp(raw_score + boost_delta - penalize_delta, 0.0, 1.0)
```

---

## Phase 3 — Ranking

Implementations are sorted by `final_score` descending.

- **Winner**: highest scoring implementation → `Recommendation.items[0]`
- **Alternatives**: remaining candidates in score order → `Recommendation.items[1..n]`
- **Filtered out**: implementations excluded in Phase 1, with reason recorded in `DecisionPath.steps[].filtered_out`

If two implementations have equal `final_score`, the tie is broken by:
1. Higher `speed-delivery` score (favours faster time-to-value)
2. Higher `cost-tco` score (favours lower cost)
3. Alphabetical by `id` (deterministic fallback)

---

## Phase 4 — Confidence & flags

### Confidence levels

| Level | Conditions |
|---|---|
| `high` | ≥ 3 candidates evaluated AND winner score ≥ 0.7 AND gap between 1st and 2nd ≥ 0.15 |
| `medium` | 2+ candidates evaluated AND winner score ≥ 0.5 AND gap ≥ 0.05 |
| `low` | Fewer than 2 candidates, OR winner score < 0.5, OR gap < 0.05 |

Gap = `score[rank_1] - score[rank_2]`. If only one candidate survived Phase 1, gap = 1.0 (no competitor) but confidence is capped at `medium` due to limited pool.

### review_required flag

`review_required: true` is set when ANY of the following conditions are met:

- `confidence: low`
- All surviving candidates have `final_score < 0.4` (no good fit found)
- Fewer than 2 candidates survived Phase 1
- Winner and second place are separated by less than `0.05` (effectively a tie)

`review_required` is **informational only** — it does not block the recommendation. The UI surfaces it as a warning to the user.

---

## Worked example

### Context

A solo developer building a prototype, budget-sensitive, no compliance requirements.

```yaml
ContextProfile:
  type: solo
  dimensions:
    use_case: uc-prototype
    maturity: early
    scale: individual
    budget_sensitivity: high
    compliance_requirements: []
```

### EvaluationFrame (budget-sensitive solo)

Default weights adjusted for high budget sensitivity:

```yaml
EvaluationFrame:
  weights:
    security-compliance: 0.10   # reduced — no compliance requirements
    integration-depth:   0.15   # reduced — solo, minimal integration needs
    observability:       0.10   # reduced — prototype, not production
    cost-tco:            0.40   # boosted — budget is primary concern
    speed-delivery:      0.25   # boosted — prototype speed matters
  overrides: []                 # no hard exclusions
```

Weights sum: 0.10 + 0.15 + 0.10 + 0.40 + 0.25 = **1.00** ✓

### Candidates for node: `ideation-chat`

From registry (using perplexity.yaml as example; assume two more candidates exist):

| Implementation | security-compliance | integration-depth | observability | cost-tco | speed-delivery |
|---|---|---|---|---|---|
| `perplexity` | low (0.0) | medium (0.5) | low (0.0) | high (1.0) | high (1.0) |
| `candidate-b` | medium (0.5) | high (1.0) | medium (0.5) | medium (0.5) | medium (0.5) |
| `candidate-c` | high (1.0) | medium (0.5) | high (1.0) | low (0.0) | medium (0.5) |

### Phase 1 — Override filtering

No overrides defined → all 3 candidates pass.

### Phase 2 — Score calculation

```
perplexity:  (0.10×0.0) + (0.15×0.5) + (0.10×0.0) + (0.40×1.0) + (0.25×1.0)
           = 0.00 + 0.075 + 0.00 + 0.40 + 0.25 = 0.725

candidate-b: (0.10×0.5) + (0.15×1.0) + (0.10×0.5) + (0.40×0.5) + (0.25×0.5)
           = 0.05 + 0.15 + 0.05 + 0.20 + 0.125 = 0.575

candidate-c: (0.10×1.0) + (0.15×0.5) + (0.10×1.0) + (0.40×0.0) + (0.25×0.5)
           = 0.10 + 0.075 + 0.10 + 0.00 + 0.125 = 0.400
```

No boosts or penalties → final scores equal raw scores.

### Phase 3 — Ranking

| Rank | Implementation | Score |
|---|---|---|
| 1 | `perplexity` | 0.725 |
| 2 | `candidate-b` | 0.575 |
| 3 | `candidate-c` | 0.400 |

Gap between 1st and 2nd: 0.725 − 0.575 = **0.150**

### Phase 4 — Confidence

- Candidates evaluated: 3 ✓
- Winner score: 0.725 ≥ 0.7 ✓
- Gap: 0.150 ≥ 0.15 ✓

→ `confidence: high`  
→ `review_required: false`

### Resulting Recommendation (partial)

```yaml
Recommendation:
  output_type: ranked_list
  items:
    - node_id: ideation-chat
      implementation_id: perplexity
      score: 0.725
      rationale: "Best fit for budget-sensitive solo prototype: highest cost-tco and speed-delivery scores match the EvaluationFrame weights."
      alternatives: [candidate-b, candidate-c]
      caveats: "Low security-compliance score — not suitable if compliance requirements are added later."
  confidence: high
  review_required: false
```

---

## Edge cases

| Situation | Behaviour |
|---|---|
| All candidates excluded by overrides | Recommendation has 0 items for this node; `confidence: low`; `review_required: true`; rationale explains which override excluded all candidates |
| Only 1 candidate survives | That candidate is the winner; confidence capped at `medium`; `review_required: true` |
| All scores identical | Tie-breaking rules apply (speed-delivery → cost-tco → alphabetical) |
| Node has `cardinality: multiple` | Top N implementations are all included as primary items, not alternatives |
| Missing criteria score in registry | Treated as `low (0.0)` with a warning flag on the Recommendation item |

---

## Open questions

- [ ] Should `boost` and `penalize` delta values (currently ±0.15) be configurable per EvaluationFrame, or fixed globally?
- [ ] For `cardinality: multiple` nodes, what is the maximum N? Fixed cap or profile-driven?
- [ ] Should the rationale be generated by an AI model or constructed from a template? Template is simpler and more predictable; AI rationale is more natural.
