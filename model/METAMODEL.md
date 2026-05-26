# Abstract Metamodel

> Version: 0.2
> Status: Draft
> Date: 2026-05-26
> xleo task ref: STO-31

This document defines the abstract metamodel of the ATDE decision engine. It is the foundational schema — all other model artifacts (nodes, criteria, assessment flows, decision trees) are instances or extensions of this metamodel.

---

## Principles

- All entities are **abstract** — no tool names, vendor names, or product-specific terms appear here.
- All entities are **domain-agnostic** — valid for any AI toolchain, not just software development.
- The model is **extensible** — new entities, roles, and connections can be added without breaking existing logic.
- **Registry of types** — all extensible value sets live in `model/types/`; entity schemas reference them via `$ref` instead of hardcoding enum values.
- **Immutability** — entity schemas are versioned. Breaking changes require a version bump.

---

## Core entities

The metamodel has six core entity types:

```
┌─────────────────────────────────────────────────────────┐
│                     METAMODEL                           │
│                                                         │
│  ContextProfile ──► DecisionPath ──► Recommendation     │
│       │                  │                              │
│       ▼                  ▼                              │
│  EvaluationFrame    Node (role)                         │
│                          │                              │
│                          ▼                              │
│                    Implementation                       │
└─────────────────────────────────────────────────────────┘
```

---

### 1. ContextProfile

Represents the user or organization seeking a recommendation. It is the **entry point** to the decision engine.

```yaml
ContextProfile:
  id: string
  type: string                    # $ref: model/types/context-profile-types.md
  dimensions:
    use_case: string
    maturity: string              # $ref: model/types/maturity-levels.md
    scale: string                 # $ref: model/types/scale-levels.md
    budget_sensitivity: string    # $ref: model/types/budget-sensitivity-levels.md
    stack_openness: string        # $ref: model/types/stack-openness.md
    compliance_requirements: []
    integration_constraints: []
  entry_point: string            # $ref: model/types/context-entry-points.md
```

**Rules:**
- `type` determines the assessment entry point (solo vs. organization).
- `dimensions` are populated progressively through the assessment flow.
- At least `use_case` or `maturity + scale` must be present to enter the decision path.

---

### 2. Node

Represents an **abstract functional role** in the AI toolchain lifecycle. A node is not a tool — it is a capability slot that can be filled by one or more implementations.

```yaml
Node:
  id: string
  label: string
  description: string
  stage: stage_id
  inputs: [node_id]
  outputs: [node_id]
  required_by: [node_id]
  optional_for: [node_id]
  criteria: [criteria_id]
  implementations: [impl_id]
  cardinality: string            # $ref: model/types/cardinalities.md
  extensible: boolean
```

**Rules:**
- `cardinality: single` — only one active implementation at a time.
- `cardinality: multiple` — stacking implementations is allowed.
- `extensible: false` — implementation list is curated and frozen per version.

---

### 3. Implementation

Represents a **concrete tool, product, or service** that fulfills a Node's role. Implementations are the leaves of the model — never abstract.

```yaml
Implementation:
  id: string
  label: string
  node_id: node_id
  version: string
  hosting: string                # $ref: model/types/hosting-types.md
  open_source: boolean
  license: string
  criteria_scores:
    [criteria_id]: string        # $ref: model/types/score-values.md
  notes: string
  last_reviewed: date
```

**Rules:**
- Must reference exactly one `node_id`.
- `criteria_scores` must cover all `criteria` listed on the parent Node.
- Reviewed on a defined cadence.

---

### 4. EvaluationFrame

Represents the **weighted scoring context** for a specific ContextProfile.

```yaml
EvaluationFrame:
  id: string
  context_profile_id: string
  weights:
    [criteria_id]: float          # 0.0 – 1.0, sum must equal 1.0
  overrides:
    - criteria_id: string
      condition: string
      action: string             # $ref: model/types/override-actions.md
```

**Rules:**
- Weights must sum to 1.0.
- Overrides are applied before scoring — they can exclude implementations entirely.
- A profile without a custom EvaluationFrame uses default criteria weights.

---

### 5. DecisionPath

Represents the **traversal route** through the node graph. Generated dynamically — not stored statically.

```yaml
DecisionPath:
  id: string
  context_profile_id: string
  evaluation_frame_id: string
  steps:
    - step: integer
      node_id: string
      question: string
      answer: string
      candidates: [impl_id]
      filtered_out: [impl_id]
      reason: string
  status: string                 # $ref: model/types/decision-path-statuses.md
```

**Rules:**
- Always tied to exactly one ContextProfile and one EvaluationFrame.
- Steps are sequential and cannot be skipped.
- Complete when all required nodes have been evaluated.

---

### 6. Recommendation

Represents the **final output** of the decision engine.

```yaml
Recommendation:
  id: string
  context_profile_id: string
  decision_path_id: string
  generated_at: datetime
  output_type: string            # $ref: model/types/recommendation-output-types.md
  items:
    - node_id: string
      implementation_id: string
      score: float
      rationale: string
      alternatives: [impl_id]
      caveats: string
  confidence: string             # $ref: model/types/confidence-levels.md
  review_required: boolean
```

**Rules:**
- Every item must include `rationale` — no black-box outputs.
- `confidence: low` or `review_required: true` triggers a UI warning (informational, not a gate).
- Recommendations are immutable after generation.

---

## Lifecycle stages

| Stage ID | Label |
|---|---|
| `ideation` | Ideation |
| `specification` | Specification |
| `knowledge` | Knowledge & Artifacts |
| `orchestration` | Orchestration |
| `implementation` | Implementation |
| `quality` | Quality & Validation |
| `deployment` | Deployment |
| `observability` | Observability |
| `operations` | Operations & Evolution |

---

## Connection types

| Type | Meaning |
|---|---|
| `feeds_into` | Output of node A is input to node B |
| `requires` | Node B cannot function without node A |
| `enhances` | Node A improves node B but is not required |
| `conflicts_with` | Nodes A and B should not be active simultaneously |
| `replaces` | Node A is a more capable superset of node B |

---

## Properties taxonomy

| Type | Values |
|---|---|
| `string (enum ref)` | Defined value set, referenced via `model/types/` |
| `string` | Free text, no constraint |
| `boolean` | true / false |
| `float` | 0.0 – 1.0 |
| `[type]` | Array of the base type |
| `date` | ISO 8601 |
| `datetime` | ISO 8601 with time |

---

## Versioning rules

| Change type | Action required |
|---|---|
| Add new optional property | Minor version bump |
| Add new entity type | Minor version bump |
| Change property type or name | Major version bump |
| Remove entity type | Major version bump + deprecation notice |
| Add new value to a type registry | Minor version bump in that type file |
| Remove value from a type registry | Major version bump in that type file |

---

## Open questions

> Open questions from this document are tracked in Linear with label [`open-question`](https://linear.app/stolbunsky/issues?label=open-question).
