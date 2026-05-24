# Decision Tree — First Layer

> Version: 0.2  
> Status: Draft  
> Date: 2026-05-24  
> xleo task ref: T-002 / STO-32  
> Depends on: METAMODEL.md

This document defines the first layer of the ATDE decision tree. The first layer determines the `ContextProfile` type and entry point, then routes the user into the appropriate assessment flow.

---

## Overview

The decision tree operates in layers:

```
Layer 0: Entry gate         ← this document covers Layer 0 and Layer 1
Layer 1: Profile routing
Layer 2: Use case / org context (see assessment/ASSESSMENT_SOLO.md, assessment/ASSESSMENT_ORG.md)
Layer 3: Constraints & criteria weighting
Layer 4: Node traversal
Layer 5: Scoring & recommendation output
```

Each layer produces structured output that feeds the next. No layer is skipped.

---

## Layer 0: Entry gate

The engine starts with a single question to determine the `ContextProfile.type`.

### Q0 — Who are you building for?

```
Q0: Who are you building for?
├── A) Myself or a small team (1–5 people)
├── B) A department or business unit (6–100 people)
└── C) An enterprise or large organization (100+ people)
```

| Answer | ContextProfile.type | ContextProfile.scale | Route to |
|---|---|---|---|
| A | `solo` | `individual` or `small_team` | Layer 1A |
| B | `organization` | `department` | Layer 1B |
| C | `organization` | `enterprise` | Layer 1C |

**Rules:**
- This question is always the first. No exceptions.
- Answer cannot be skipped.
- Answer sets `entry_point` for the rest of the session.

---

## Layer 1A: Solo / small team profile routing

Entry point: **use case first**. The solo user knows what they want to build but may not have organizational constraints.

### Q1A — What is the primary outcome you want to achieve?

```
Q1A: What are you primarily trying to build or automate?
├── A) A prototype or proof of concept                  → uc-prototype
├── B) An internal tool or dashboard                    → uc-internal-app
├── C) A public-facing product or SaaS                  → uc-public-saas
├── D) An automated workflow or agent pipeline          → uc-automation
├── E) A data or analytics product                      → uc-data-product
├── F) An AI feature inside an existing product         → uc-embedded-ai
├── G) A knowledge base or documentation system         → uc-knowledge-system
└── H) Something else / I'm not sure                   → clarification branch
```

**Output:** Sets `ContextProfile.dimensions.use_case`.

**Clarification branch (H):**
If user selects “I’m not sure”, show a one-sentence description of each use case and ask again. If still unclear after second pass → route to `uc-prototype` as default with a note.

### Q1A-2 — What is your maturity level?

```
Q1A-2: How would you describe your current technical setup?
├── A) Starting from scratch, no existing stack        → maturity: early
├── B) I have some tools but nothing formal             → maturity: growing
└── C) I have an established workflow I want to extend  → maturity: established
```

**Output:** Sets `ContextProfile.dimensions.maturity`.

### Q1A-3 — Budget sensitivity

```
Q1A-3: How sensitive are you to cost?
├── A) Free / open-source preferred                     → budget_sensitivity: high
├── B) Willing to pay for the right tool                → budget_sensitivity: medium
└── C) Cost is not a primary concern                    → budget_sensitivity: low
```

**Output:** Sets `ContextProfile.dimensions.budget_sensitivity`. Feeds `EvaluationFrame` weights.

---

## Layer 1B: Department profile routing

Entry point: **org context first**, then use case. The department user has organizational constraints that narrow the solution space before use case matters.

### Q1B — What does your organization already use?

```
Q1B: Which of the following best describes your current tech environment?
├── A) Microsoft ecosystem (M365, Azure, Teams)          → stack: microsoft
├── B) Google ecosystem (Workspace, GCP)                → stack: google
├── C) AWS-based infrastructure                         → stack: aws
├── D) Mixed / cloud-agnostic                           → stack: mixed
└── E) On-premise / self-hosted priority                 → stack: on_premise
```

**Output:** Sets `ContextProfile.dimensions.integration_constraints`.

### Q1B-2 — Compliance requirements

```
Q1B-2: Do you have specific compliance or data requirements?
├── A) GDPR / data residency in EU                      → compliance: [gdpr]
├── B) SOC 2 / ISO 27001 vendor requirement              → compliance: [soc2]
├── C) Industry-specific (HIPAA, PCI, etc.)              → compliance: [industry]
├── D) Internal security policy only                    → compliance: [internal]
└── E) No specific requirements                         → compliance: []
```

**Output:** Sets `ContextProfile.dimensions.compliance_requirements`. If non-empty → EvaluationFrame sets `security-compliance` override to `require`.

### Q1B-3 — Use case

Same question set as Q1A but asked after org context is known. Use case is narrowed by stack and compliance answers.

---

## Layer 1C: Enterprise profile routing

Entry point: **org context first**, compliance-heavy. Enterprise users have the most constraints and the highest stakes.

### Q1C — Same as Q1B stack question

With additional follow-up:

### Q1C-2 — Governance model

```
Q1C-2: How is AI tooling typically approved in your organization?
├── A) Centralized IT procurement / approved vendor list  → governance: centralized
├── B) Team-level decisions with IT review               → governance: federated
└── C) No formal process yet                            → governance: informal
```

**Output:** Sets governance context. If `centralized` → EvaluationFrame boosts `integration-depth` and `observability` weights significantly.

### Q1C-3 — Compliance (same as Q1B-2 but required, not optional)

### Q1C-4 — Existing AI investment

```
Q1C-4: Does your organization already have AI tools in production?
├── A) Yes, and we want to extend the existing stack     → maturity: established
├── B) Pilots only, nothing in production yet            → maturity: growing
└── C) No AI tools in use yet                           → maturity: early
```

---

## Layer 1 output

After Layer 1 completes, the engine has a fully populated `ContextProfile`:

```yaml
ContextProfile:
  type: solo | organization
  entry_point: use_case_first | org_context_first
  dimensions:
    use_case: [uc-id]
    maturity: early | growing | established | enterprise
    scale: individual | small_team | department | enterprise
    budget_sensitivity: low | medium | high
    stack_openness: open_source | mixed | proprietary
    compliance_requirements: []
    integration_constraints: []
```

This profile is passed to the `EvaluationFrame` builder and then to Layer 2 (node traversal).

---

## Routing summary

```
Q0
├── solo/small_team ──► Layer 1A ──► use_case ──► maturity ──► budget ──► EvalFrame
├── department ────► Layer 1B ──► stack ──► compliance ──► use_case ──► EvalFrame
└── enterprise ────► Layer 1C ──► stack ──► governance ──► compliance ──► ai_investment ──► EvalFrame
```

---

## Open questions

- [ ] Q0 answer B (department) vs C (enterprise): where exactly is the boundary? Is it headcount, budget authority, or governance model?
- [ ] Should Q1A use case options include multi-select, or is single primary use case sufficient for Layer 1?
- [ ] For Layer 1C, should `governance: centralized` immediately filter out tools not on the approved vendor list, or just reweight?
- [ ] How many steps maximum per layer before user abandonment risk becomes significant? Hypothesis: max 5 per layer.
