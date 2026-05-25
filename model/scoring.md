# Scoring & Ranking Logic

> Version: 0.2  
> Status: Draft  
> Date: 2026-05-25  
> xleo task ref: STO-36  
> Depends on: METAMODEL.md, model/criteria.md, model/tool-registry.md, model/types/

This document defines how the ATDE engine converts a populated `ContextProfile` and `EvaluationFrame` into a scored `Recommendation`. The algorithm is rule-based and fully deterministic — no AI inference in the scoring pipeline itself.

All type values (score values, confidence levels) are defined in `model/types/` and referenced here — not hardcoded.

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
| `exclude` | Implementation removed from candidate pool. Cannot be recovered by score. |
| `require` | Implementations not satisfying this criterion are excluded. |
| `boost` | Adds `+0.15` to the final score of qualifying implementations (Phase 3). |
| `penalize` | Subtracts `-0.15` from the final score of qualifying implementations (Phase 3). |

### Conflict resolution

1. `exclude` always wins — any exclude overrides all other rules for that implementation.
2. `require` evaluated next — fail = exclude.
3. `boost` and `penalize` are additive — multiple stack, final score clamped to `[0.0, 1.0]`.
4. Conflicting boost + penalize on same criterion — both applied, partial cancellation.
5. Same-action conflicts resolved by declaration order in the EvaluationFrame.

### Output of Phase 1

Filtered candidate pool — implementations that passed all `exclude` and `require` rules.

---

## Phase 2 — Score calculation

### Score mapping

Qualitative score values are defined in [`model/types/score-values.md`](types/score-values.md). Current mapping:

| Score value ID | Numeric value |
|---|---|
| `high` | `1.0` |
| `medium` | `0.5` |
| `low` | `0.0` |

The engine reads numeric values from the type registry — not from hardcoded constants.

### Weighted sum formula

For each implementation that survived Phase 1:

```
raw_score = Σ (criteria_weight[i] × score_numeric[i])
            for all criteria applicable to the node
```

Where:
- `criteria_weight[i]` — from `EvaluationFrame.weights`, or default from `criteria.md`
- `score_numeric[i]` — numeric mapping from `model/types/score-values.md`
- Weights must sum to `1.0` — enforced at EvaluationFrame creation

### Final score (after Phase 1 adjustments)

```
final_score = clamp(raw_score + boost_delta − penalize_delta, 0.0, 1.0)
```

---

## Phase 3 — Ranking

Sort by `final_score` descending.

- **Winner** → `Recommendation.items[0]`
- **Alternatives** → remaining candidates in score order
- **Filtered out** → recorded in `DecisionPath.steps[].filtered_out` with reason

### Tie-breaking (equal `final_score`)

1. Higher `speed-delivery` score
2. Higher `cost-tco` score
3. Alphabetical by `id` (deterministic fallback)

---

## Phase 4 — Confidence & flags

### Confidence levels

Defined in [`model/types/confidence-levels.md`](types/confidence-levels.md). Current thresholds:

| Level | Conditions |
|---|---|
| `high` | ≥3 candidates AND winner score ≥`0.7` AND gap ≥`0.15` |
| `medium` | ≥2 candidates AND winner score ≥`0.5` AND gap ≥`0.05` |
| `low` | <2 candidates OR winner score <`0.5` OR gap <`0.05` |

All thresholds are defined in `model/types/confidence-levels.md`. Change a threshold there — the engine picks it up automatically.

**Gap** = `score[rank_1] − score[rank_2]`. Single survivor: gap = `1.0`, confidence capped at `medium`.

### review_required flag

`review_required: true` when ANY of:
- `confidence: low`
- All candidates have `final_score < 0.4`
- Fewer than 2 candidates survived Phase 1
- Gap between 1st and 2nd < `0.05`

`review_required` is **informational only** — UI warning, not a blocking gate.

---

## Worked example

### Context — solo developer, budget-sensitive prototype

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

### EvaluationFrame

```yaml
EvaluationFrame:
  weights:
    security-compliance: 0.10
    integration-depth:   0.15
    observability:       0.10
    cost-tco:            0.40
    speed-delivery:      0.25
  overrides: []
```

### Candidates for node `ideation-chat`

| Implementation | security-compliance | integration-depth | observability | cost-tco | speed-delivery |
|---|---|---|---|---|---|
| `perplexity` | low → 0.0 | medium → 0.5 | low → 0.0 | high → 1.0 | high → 1.0 |
| `candidate-b` | medium → 0.5 | high → 1.0 | medium → 0.5 | medium → 0.5 | medium → 0.5 |
| `candidate-c` | high → 1.0 | medium → 0.5 | high → 1.0 | low → 0.0 | medium → 0.5 |

### Phase 1 — no overrides → all 3 pass

### Phase 2 — scores

```
perplexity:  (0.10×0.0)+(0.15×0.5)+(0.10×0.0)+(0.40×1.0)+(0.25×1.0) = 0.725
candidate-b: (0.10×0.5)+(0.15×1.0)+(0.10×0.5)+(0.40×0.5)+(0.25×0.5) = 0.575
candidate-c: (0.10×1.0)+(0.15×0.5)+(0.10×1.0)+(0.40×0.0)+(0.25×0.5) = 0.400
```

### Phase 3 — ranking

| Rank | Tool | Score |
|---|---|---|
| 1 | `perplexity` | 0.725 |
| 2 | `candidate-b` | 0.575 |
| 3 | `candidate-c` | 0.400 |

Gap = 0.725 − 0.575 = **0.150**

### Phase 4 — confidence

- Candidates: 3 ✓ — Score: 0.725 ≥ 0.7 ✓ — Gap: 0.150 ≥ 0.15 ✓
- `confidence: high` — `review_required: false`

---

## Edge cases

| Situation | Behaviour |
|---|---|
| All candidates excluded | 0 items for node; `confidence: low`; `review_required: true`; reason recorded |
| 1 candidate survives | Winner by default; confidence capped at `medium`; `review_required: true` |
| All scores identical | Tie-breaking rules apply |
| `cardinality: multiple` node | Top N are all primary items, not alternatives |
| Missing criteria score | Treated as `low` (→ `0.0`) with warning flag on Recommendation item |

---

## Open questions

- [ ] Should `boost`/`penalize` delta (`±0.15`) be configurable per EvaluationFrame, or stay as a type registry constant?
- [ ] For `cardinality: multiple` nodes — what is the maximum N? Fixed cap or profile-driven?
- [ ] Should rationale text be template-generated or AI-generated?
