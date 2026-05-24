# Assessment Flow — Solo / Small Team (Use Case First)

> Version: 0.2  
> Status: Draft  
> Date: 2026-05-24  
> xleo task ref: T-003 / STO-33  
> Entry point: `use_case_first`  
> ContextProfile.type: `solo`  
> Depends on: METAMODEL.md, DECISION_TREE.md

This document defines the full assessment flow for solo users and small teams. The entry point is the use case — what the user wants to build or automate. Org constraints are collected after, weighted by the use case.

---

## Flow overview

```
Q0 (entry gate)
└── solo/small_team
    └── Q1A: use case selection
        └── Q1A-2: maturity
            └── Q1A-3: budget sensitivity
                └── Q2S: stack openness
                    └── Q3S: collaboration needs
                        └── Q4S: deployment preference
                            └── Q5S: AI model preference
                                └── [EvaluationFrame built]
                                    └── [Node traversal → Recommendation]
```

Maximum 7 steps before recommendation. Additional clarifying questions are inline, not new steps.

---

## Q0 — Entry gate

> Defined in DECISION_TREE.md. Answer A routes here.

**Answer recorded:** `ContextProfile.scale = individual | small_team`

---

## Q1A — Use case

**Question:** What are you primarily trying to build or automate?

| Option | Label | Use case ID |
|---|---|---|
| A | A prototype or proof of concept | `uc-prototype` |
| B | An internal tool or dashboard | `uc-internal-app` |
| C | A public-facing product or SaaS | `uc-public-saas` |
| D | An automated workflow or agent pipeline | `uc-automation` |
| E | A data or analytics product | `uc-data-product` |
| F | An AI feature inside an existing product | `uc-embedded-ai` |
| G | A knowledge base or documentation system | `uc-knowledge-system` |
| H | Something else / I’m not sure | → clarification |

**Output:** `ContextProfile.dimensions.use_case`

**Clarification branch (H):**
1. Show one-line description of each use case A–G.
2. Ask again.
3. If still unclear → set `use_case = uc-prototype`, add flag `use_case_uncertain: true`.
4. Flag surfaces in Recommendation as: *“Based on limited information, we defaulted to prototype scope. Review if your needs differ.”*

**Multi-use-case edge case:**
If user explicitly states two use cases (e.g. “prototype that becomes a SaaS”):
- Set primary `use_case` to the first stated.
- Set `use_case_secondary` to the second.
- Weight node traversal toward primary, surface secondary as “Future consideration” in Recommendation.

---

## Q1A-2 — Maturity

**Question:** How would you describe your current technical setup?

| Option | Label | Maturity |
|---|---|---|
| A | Starting from scratch, no existing stack | `early` |
| B | I have some tools but nothing formal | `growing` |
| C | I have an established workflow I want to extend | `established` |

**Output:** `ContextProfile.dimensions.maturity`

**Impact on node traversal:**
- `early` → prioritize nodes with low setup friction, strong defaults, good documentation.
- `growing` → balance flexibility and ease.
- `established` → prioritize integration depth, API access, extensibility.

---

## Q1A-3 — Budget sensitivity

**Question:** How sensitive are you to cost?

| Option | Label | Budget sensitivity |
|---|---|---|
| A | Free or open-source preferred | `high` |
| B | Willing to pay for the right tool | `medium` |
| C | Cost is not a primary concern | `low` |

**Output:** `ContextProfile.dimensions.budget_sensitivity`

**EvaluationFrame impact:**
- `high` → `cost-tco` weight boosted to 0.35, implementations with no free tier filtered out.
- `medium` → default weights applied.
- `low` → `cost-tco` weight reduced to 0.05, `speed-delivery` and `integration-depth` boosted.

---

## Q2S — Stack openness

**Question:** What is your preference for the tools you use?

| Option | Label | Stack openness |
|---|---|---|
| A | Open source strongly preferred | `open_source` |
| B | Mix of open source and commercial is fine | `mixed` |
| C | Commercial / proprietary tools are fine | `proprietary` |

**Output:** `ContextProfile.dimensions.stack_openness`

**Impact:** If `open_source` → EvaluationFrame override: exclude implementations with `open_source: false` unless no open-source alternative exists at that node.

---

## Q3S — Collaboration needs

**Question:** Will others need to collaborate in the same tools?

| Option | Label | Impact |
|---|---|---|
| A | No, this is just me | Solo mode → no collaboration node required |
| B | Yes, 2–5 people occasionally | Light collaboration → `cardinality: optional` for collab nodes |
| C | Yes, regularly with a small team | Collaboration node becomes `required` |

**Output:** Sets `required_by` flag on `orchestration` and `knowledge-base` nodes.

---

## Q4S — Deployment preference

**Question:** Where do you plan to run or deploy this?

| Option | Label | Hosting |
|---|---|---|
| A | Fully cloud-hosted, I don’t want to manage infra | `cloud` |
| B | I want control but cloud is fine | `cloud` with observability required |
| C | I prefer to self-host or run locally | `self_hosted` |

**Output:** Sets `Implementation.hosting` filter in EvaluationFrame.
- `self_hosted` → exclude `cloud`-only implementations.
- `cloud` → no exclusion, but note vendor lock-in risk in Recommendation if applicable.

---

## Q5S — AI model preference

**Question:** Do you have a preference for the underlying AI model?

| Option | Label | Impact |
|---|---|---|
| A | I want to use a specific model (GPT, Claude, Gemini, etc.) | Sets model constraint |
| B | I want to be able to swap models freely | Boosts `model-agnostic` criteria weight |
| C | No preference | Default weights, no filter |

**Output:** Sets model constraint or boosts `model-agnostic` weight in EvaluationFrame.

**Follow-up if A:**
- Which model? (free text or dropdown)
- Filters implementations to those compatible with the named model.

---

## EvaluationFrame construction

After Q5S, the engine builds the `EvaluationFrame` from the collected profile:

```yaml
EvaluationFrame:
  context_profile_id: [generated]
  weights:
    security-compliance:  0.10   # low weight for solo users by default
    integration-depth:    0.15
    observability:        0.10
    cost-tco:             [dynamic: high=0.35, medium=0.20, low=0.05]
    speed-delivery:       0.25   # high weight for solo — time to value matters
  overrides:
    - if stack_openness = open_source → exclude open_source: false
    - if hosting = self_hosted → exclude cloud-only
    - if model constraint set → exclude incompatible implementations
    - if use_case_uncertain: true → add review_required: true to Recommendation
```

---

## Recommendation output format (solo)

```yaml
Recommendation:
  output_type: toolset
  items:
    - node_id: [e.g. coding-agent]
      implementation_id: [e.g. cursor]
      score: 0.87
      rationale: "Best fit for early-stage solo developer building a SaaS prototype..."
      alternatives: [windsurf, copilot]
      caveats: "No self-hosted option. Vendor lock-in risk if model preferences change."
  confidence: high | medium | low
  review_required: false
  notes: "Based on: solo, uc-public-saas, maturity:early, budget:medium, stack:mixed"
```

---

## Edge cases handled

| Case | Handling |
|---|---|
| User unsure of use case | Clarification branch → default to `uc-prototype` with flag |
| User states two use cases | Primary + secondary, weighted traversal |
| No implementations pass filters | Relax one constraint at a time, surface trade-off |
| All implementations score equally | Sort by `speed-delivery`, flag tie in Recommendation |
| User abandons mid-flow | Save partial `ContextProfile`, offer to resume |

---

## Open questions (to resolve before implementation)

- [ ] Should Q3S (collaboration) affect which nodes are shown, or only their weight?
- [ ] At what score threshold does `confidence` drop from `high` to `medium`? Hypothesis: below 0.70.
- [ ] Should the solo assessment ever ask about compliance? Hypothesis: only if `uc-public-saas` or `uc-compliance-workflow` is selected.
- [ ] Max abandoned sessions before flow is flagged for UX review?
