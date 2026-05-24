# Quiz Flow — Organization (Org Context First)

> Version: 0.1  
> Status: Draft  
> Date: 2026-05-24  
> xleo task ref: T-004 / STO-34  
> Entry point: `org_context_first`  
> ContextProfile.type: `organization`  
> Covers: Department (1B) and Enterprise (1C) routes  
> Depends on: METAMODEL.md, DECISION_TREE.md

This document defines the full quiz flow for department and enterprise users. The entry point is the organizational context — stack, governance, compliance — before use case is asked. Org constraints narrow the solution space first; use case refines it.

---

## Flow overview

```
Q0 (entry gate)
├── department (1B)
│   └── Q1B:  stack
│       └── Q1B-2: compliance
│           └── Q2O:  use case
│               └── Q3O:  maturity
│                   └── Q4O:  budget authority
│                       └── Q5O:  team size
│                           └── [EvaluationFrame built]
│                               └── [Node traversal → Recommendation]
│
└── enterprise (1C)
    └── Q1C:  stack
        └── Q1C-2: governance model
            └── Q1C-3: compliance (required)
                └── Q1C-4: existing AI investment
                    └── Q2O:  use case
                        └── Q3O:  maturity
                            └── Q4O:  budget authority
                                └── Q5O:  team size
                                    └── [EvaluationFrame built]
                                        └── [Node traversal → Recommendation]
```

Department: max 6 questions. Enterprise: max 9 questions. No question is skippable.

---

## Q0 — Entry gate

> Defined in DECISION_TREE.md.
- Answer B → Department route (1B)
- Answer C → Enterprise route (1C)

---

## Department route (1B)

### Q1B — Tech stack

**Question:** Which of the following best describes your current tech environment?

| Option | Label | Stack |
|---|---|---|
| A | Microsoft ecosystem (M365, Azure, Teams, Copilot) | `microsoft` |
| B | Google ecosystem (Workspace, GCP, Gemini) | `google` |
| C | AWS-based infrastructure | `aws` |
| D | Mixed or cloud-agnostic | `mixed` |
| E | On-premise or self-hosted priority | `on_premise` |

**Output:** `ContextProfile.dimensions.integration_constraints`

**EvaluationFrame impact:**
- `microsoft` → boost `integration-depth` for Microsoft-native implementations.
- `on_premise` → exclude `cloud`-only implementations; set `self_hosted` filter.
- `mixed` → no filter, boost `model-agnostic` and `api-access` criteria.

---

### Q1B-2 — Compliance

**Question:** Do you have specific compliance or data handling requirements?

| Option | Label | Compliance |
|---|---|---|
| A | GDPR / EU data residency required | `[gdpr]` |
| B | SOC 2 or ISO 27001 vendor certification required | `[soc2]` |
| C | Industry-specific (HIPAA, PCI DSS, FedRAMP, etc.) | `[industry]` |
| D | Internal security policy only | `[internal]` |
| E | No specific requirements | `[]` |

**Multi-select:** Yes — user may select multiple.

**Output:** `ContextProfile.dimensions.compliance_requirements`

**EvaluationFrame impact:**
- Any non-empty compliance list → `security-compliance` override: `require`.
- `gdpr` → additionally filter out implementations with no EU data residency option.
- `industry` → trigger follow-up: *Which industry regulation?* (free text, stored as note).

---

## Enterprise route (1C)

### Q1C — Tech stack

Same as Q1B. No difference in options.

---

### Q1C-2 — Governance model

**Question:** How is AI tooling typically approved in your organization?

| Option | Label | Governance |
|---|---|---|
| A | Centralized IT procurement — approved vendor list required | `centralized` |
| B | Team-level decisions with IT security review | `federated` |
| C | No formal process yet | `informal` |

**Output:** Sets governance context.

**EvaluationFrame impact:**
- `centralized` → boost `integration-depth` to 0.30 and `observability` to 0.25; filter to implementations with enterprise SLA available.
- `federated` → balanced weights, no hard filters.
- `informal` → surface governance risk note in Recommendation: *“No formal AI governance process detected. Consider establishing one before rolling out tooling at scale.”*

---

### Q1C-3 — Compliance (required)

Same options as Q1B-2. In the enterprise route, this question is **mandatory** — answer E (no requirements) is valid but must be explicitly selected.

**Additional follow-up for enterprise:**
If `gdpr` or `industry` selected: *Is your data processed in a specific geographic region?* (free text, stored as note in ContextProfile).

---

### Q1C-4 — Existing AI investment

**Question:** Does your organization already have AI tools in production?

| Option | Label | Maturity pre-set |
|---|---|---|
| A | Yes — we have tools in production and want to extend the stack | `established` |
| B | Pilots only — nothing in production yet | `growing` |
| C | No AI tools in use yet | `early` |

**Output:** Pre-sets `ContextProfile.dimensions.maturity`. Still confirmed at Q3O.

---

## Shared questions (both routes, after org context)

### Q2O — Use case

**Question:** What is the primary outcome your team is trying to achieve?

Same options as QUIZ_SOLO Q1A (A–H), with one addition:

| Option | Label | Use case ID |
|---|---|---|
| A | A prototype or proof of concept | `uc-prototype` |
| B | An internal tool or dashboard | `uc-internal-app` |
| C | A public-facing product or SaaS | `uc-public-saas` |
| D | An automated workflow or agent pipeline | `uc-automation` |
| E | A data or analytics product | `uc-data-product` |
| F | An AI feature inside an existing product | `uc-embedded-ai` |
| G | A knowledge base or documentation system | `uc-knowledge-system` |
| H | Modernizing or migrating a legacy system | `uc-app-modernization` |
| I | Support or service desk automation | `uc-support-automation` |
| J | Something else / I’m not sure | → clarification |

**Difference from solo flow:** Options H and I are added — more common in org context.

**Output:** `ContextProfile.dimensions.use_case`

---

### Q3O — Maturity confirmation

**Question:** How would you describe your team’s current AI toolchain maturity?

| Option | Label | Maturity |
|---|---|---|
| A | No tools, starting from scratch | `early` |
| B | Some tools in use, no unified approach | `growing` |
| C | Established workflow, looking to extend | `established` |
| D | Mature stack, evaluating strategic additions | `enterprise` |

**Note:** For Enterprise route, if Q1C-4 pre-set maturity, this question shows pre-selected answer and asks user to confirm or override.

**Output:** Confirms or overrides `ContextProfile.dimensions.maturity`.

---

### Q4O — Budget authority

**Question:** How is budget for new tools typically handled in your team?

| Option | Label | Budget sensitivity |
|---|---|---|
| A | Tight budget, need to justify every cost | `high` |
| B | Reasonable budget with standard approval | `medium` |
| C | Strategic budget available for the right solution | `low` |

**Output:** `ContextProfile.dimensions.budget_sensitivity`

**Difference from solo:** Framing is around team/org budget authority, not personal preference.

---

### Q5O — Team size using the tools

**Question:** How many people will actively use or interact with these tools?

| Option | Label | Impact |
|---|---|---|
| A | 1–5 people | Lightweight collab nodes sufficient |
| B | 6–20 people | Collaboration and access control nodes required |
| C | 21–100 people | Governance and observability nodes required |
| D | 100+ people | Full governance, SSO, RBAC, audit trail required |

**Output:** Sets `required_by` flags on `governance`, `observability`, and `knowledge-base` nodes based on team size.

**EvaluationFrame impact:**
- 21+ → `observability` weight boosted.
- 100+ → `observability` and `security-compliance` both boosted; `integration-depth` required.

---

## EvaluationFrame construction (org)

```yaml
EvaluationFrame:
  context_profile_id: [generated]
  weights:                            # defaults for org, adjusted by answers
    security-compliance:  0.25        # higher than solo by default
    integration-depth:    0.25        # org ecosystem fit is critical
    observability:        0.20
    cost-tco:             [dynamic: high=0.25, medium=0.15, low=0.05]
    speed-delivery:       0.15        # lower than solo — quality over speed
  overrides:
    - if compliance non-empty → security-compliance: require
    - if gdpr → exclude no-EU-residency implementations
    - if governance = centralized → integration-depth += 0.10, observability += 0.05
    - if stack = on_premise → exclude cloud-only implementations
    - if team_size >= 21 → observability: require
    - if team_size >= 100 → governance node: required, SSO/RBAC filter active
```

---

## Recommendation output format (org)

```yaml
Recommendation:
  output_type: stack            # org users get a full stack, not a single tool
  items:
    - node_id: orchestrator
      implementation_id: [e.g. n8n-enterprise]
      score: 0.84
      rationale: "Best fit for department-level automation with EU data residency..."
      alternatives: [make, temporal]
      caveats: "Self-hosted deployment required for GDPR compliance."
  confidence: high | medium | low
  review_required: true         # always true for org recommendations
  governance_note: "No formal AI governance process detected. Recommendation pending formal review."
  notes: "Based on: organization/department, stack:microsoft, compliance:[gdpr], uc-automation, maturity:growing"
```

**Key difference from solo:** `review_required` is always `true` for org recommendations. No org recommendation is auto-actionable.

---

## Edge cases handled

| Case | Handling |
|---|---|
| Multiple compliance requirements | All applied; most restrictive filter wins |
| Governance = informal + team 100+ | Surface governance risk alert in Recommendation |
| Stack = on_premise + no self-hosted implementations at a node | Surface gap: *“No self-hosted option available for [node]. Closest alternative: [impl] with data residency option.”* |
| Use case unclear in org context | Default to `uc-internal-app` (most common org use case) with flag |
| Maturity conflict (Q1C-4 vs Q3O) | Q3O answer wins; Q1C-4 pre-set shown as suggestion only |
| Budget = high sensitivity + enterprise stack required | Surface trade-off: *“Your compliance and team size requirements limit free/open-source options.”* |

---

## Differences from solo flow

| Dimension | Solo | Org |
|---|---|---|
| Entry point | Use case first | Org context first |
| Compliance question | Only if SaaS/compliance UC | Always present |
| Governance question | Not asked | Required for enterprise |
| Use case options | 8 options | 10 options (adds modernization, support) |
| Budget framing | Personal preference | Team/org budget authority |
| Recommendation type | Toolset | Full stack |
| review_required | Conditional | Always true |
| Max questions | 7 | 6 (dept) / 9 (enterprise) |

---

## Open questions (to resolve before implementation)

- [ ] Should Q5O (team size) affect which nodes are shown, or only their required/optional status?
- [ ] For centralized governance: should the engine ask for the approved vendor list explicitly, or assume it will be applied post-recommendation?
- [ ] Should department and enterprise routes eventually split into two separate quiz files, or stay unified here?
- [ ] At what team size threshold does `governance` node become mandatory? Hypothesis: 21+, confirmed above — needs stakeholder validation.
- [ ] Should org recommendations include a *“not recommended”* section for implementations that were considered and excluded?
