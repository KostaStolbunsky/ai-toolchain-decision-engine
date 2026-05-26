# Assessment

This directory contains the assessment flows used to build a user's context profile before scoring begins.

The assessment is the entry point into the decision engine. It collects the information needed to construct an `EvaluationFrame` — the set of weights, hard rules, and filters applied during scoring.

## Flows

| File | Entry point | For |
|---|---|---|
| [`ASSESSMENT_SOLO.md`](ASSESSMENT_SOLO.md) | Use case first | Individual users and solo builders |
| [`ASSESSMENT_ORG.md`](ASSESSMENT_ORG.md) | Org context first | Teams, departments, and enterprises |

## How it connects

The assessment produces a `ContextProfile`. The scoring engine reads this profile to configure the `EvaluationFrame`. See [`model/DECISION_TREE.md`](../model/DECISION_TREE.md) for the full routing logic.
