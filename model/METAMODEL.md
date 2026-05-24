# Abstract Metamodel

> Version: 0.1  
> Status: Draft  
> Date: 2026-05-24  
> xleo task ref: T-001 / STO-31

This document defines the abstract metamodel of the ATDE decision engine. It is the foundational schema — all other model artifacts (nodes, criteria, assessment flows, decision trees) are instances or extensions of this metamodel.

---

## Principles

- All entities are **abstract** — no tool names, vendor names, or product-specific terms appear here.
- All entities are **domain-agnostic** — valid for any AI toolchain, not just software development.
- The model is **extensible** — new entities, roles, and connections can be added without breaking existing logic.
- **Immutability** — entity schemas are versioned. Breaking changes require a version bump.

---

## Core entities

The metamodel has five core entity types:

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

Represents the user or organization that is seeking a recommendation. It is the **entry point** to the decision engine.

```yaml
ContextProfile:
  id: string                      # unique identifier
  type: enum                      # solo | organization
  dimensions:                     # key-value context dimensions
    use_case: string              # primary intended use
    maturity: enum                # early | growing | established | enterprise
    scale: enum                   # individual | small_team | department | enterprise
    budget_sensitivity: enum      # low | medium | high
    stack_openness: enum          # open_source | mixed | proprietary
    compliance_requirements: []   # list of named compliance needs
    integration_constraints: []   # named existing systems to integrate with
  entry_point: enum               # use_case_first | org_context_first
```

**Rules:**
- `type` determines the assessment entry point (see STATUS_TRANSITIONS).
- `dimensions` are populated progressively through the assessment flow.
- At least `use_case` or `maturity + scale` must be present to enter the decision path.

---

### 2. Node

Represents an **abstract functional role** in the AI toolchain lifecycle. A node is not a tool — it is a capability slot that can be filled by one or more implementations.

```yaml
Node:
  id: string                      # unique identifier, kebab-case
  label: string                   # human-readable name
  description: string             # what capability this node provides
  stage: stage_id                 # which lifecycle stage this belongs to
  inputs: [node_id]               # nodes that feed into this node
  outputs: [node_id]              # nodes this node feeds into
  required_by: [node_id]         # nodes that cannot function without this one
  optional_for: [node_id]        # nodes this enhances but does not block
  criteria: [criteria_id]         # evaluation criteria applicable to this node
  implementations: [impl_id]      # registered tool implementations
  cardinality: enum               # single | multiple | optional
  extensible: boolean             # whether new implementations can be added freely
```

**Rules:**
- A node with `cardinality: single` means only one implementation should be active at a time.
- A node with `cardinality: multiple` allows stacking implementations.
- `extensible: false` means the implementation list is curated and frozen per version.

---

### 3. Implementation

Represents a **concrete tool, product, or service** that fulfills a Node's role. Implementations are the leaves of the model — they are never abstract.

```yaml
Implementation:
  id: string                      # unique identifier
  label: string                   # product/tool name
  node_id: node_id                # which node this implements
  version: string                 # known version or "latest"
  hosting: enum                   # cloud | self_hosted | hybrid
  open_source: boolean
  license: string
  criteria_scores:                # scored against node criteria
    [criteria_id]: score          # high | medium | low
  notes: string                   # free-text caveats or context
  last_reviewed: date
```

**Rules:**
- An implementation must reference exactly one `node_id`.
- `criteria_scores` must cover all `criteria` listed on the parent Node.
- Implementations are versioned and reviewed on a defined cadence.

---

### 4. EvaluationFrame

Represents the **weighted scoring context** applied to a specific ContextProfile. It maps criteria to weights based on the profile's dimensions.

```yaml
EvaluationFrame:
  id: string
  context_profile_id: string      # which profile this frame applies to
  weights:                        # override default criteria weights
    [criteria_id]: float          # 0.0 – 1.0, sum must equal 1.0
  overrides:                      # hard rules that override scores
    - criteria_id: string
      condition: string           # e.g. "compliance_requirements includes GDPR"
      action: enum                # exclude | require | boost | penalize
```

**Rules:**
- Weights must sum to 1.0 within a frame.
- `overrides` are applied before scoring — they can exclude implementations entirely.
- A ContextProfile without a custom EvaluationFrame uses default criteria weights.

---

### 5. DecisionPath

Represents the **traversal route** through the node graph for a specific ContextProfile. It is generated dynamically — not stored statically.

```yaml
DecisionPath:
  id: string
  context_profile_id: string
  evaluation_frame_id: string
  steps:                          # ordered list of decision steps
    - step: integer
      node_id: string
      question: string            # assessment question for this node
      answer: string              # user's answer
      candidates: [impl_id]       # implementations considered
      filtered_out: [impl_id]     # implementations excluded and why
      reason: string
  status: enum                    # in_progress | complete | abandoned
```

**Rules:**
- A DecisionPath is always tied to exactly one ContextProfile and one EvaluationFrame.
- Steps are sequential and cannot be skipped.
- The path is complete when all required nodes have been evaluated.

---

### 6. Recommendation

Represents the **final output** of the decision engine for a given ContextProfile.

```yaml
Recommendation:
  id: string
  context_profile_id: string
  decision_path_id: string
  generated_at: datetime
  output_type: enum               # single_tool | toolset | ranked_list | stack
  items:
    - node_id: string
      implementation_id: string
      score: float
      rationale: string           # why this implementation was selected
      alternatives: [impl_id]     # runner-up implementations
      caveats: string             # known limitations or conditions
  confidence: enum                # high | medium | low
  review_required: boolean        # flag for human review before acting
```

**Rules:**
- Every recommendation must include `rationale` per item — no black-box outputs.
- `confidence: low` or `review_required: true` must trigger a human-in-the-loop gate.
- Recommendations are immutable after generation — create a new one rather than editing.

---

## Lifecycle stages

Stages are referenced by Node entities. They represent phases of the AI toolchain lifecycle.

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

Connections between nodes define how capability slots relate to each other.

| Type | Meaning |
|---|---|
| `feeds_into` | Output of node A is input to node B |
| `requires` | Node B cannot function without node A |
| `enhances` | Node A improves node B but is not required |
| `conflicts_with` | Nodes A and B should not be active simultaneously |
| `replaces` | Node A is a more capable superset of node B |

---

## Properties taxonomy

All entity properties follow these base types:

| Type | Values |
|---|---|
| `enum` | Predefined set, extensible via version bump |
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
| Add new enum value | Minor version bump |
| Remove enum value | Major version bump |

---

## Open questions

- [ ] Should `DecisionPath` steps map 1:1 to assessment questions, or can one step span multiple questions?
- [ ] Should `Recommendation.output_type` be set by the engine or by the user's preference?
- [ ] How are conflicting `overrides` in EvaluationFrame resolved — first match, priority order, or union?
- [ ] Should a ContextProfile be mutable during the assessment flow, or locked after the first step?
