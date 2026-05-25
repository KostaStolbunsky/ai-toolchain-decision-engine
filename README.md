# AI Toolchain Decision Engine (ATDE)

> An abstract, domain-agnostic decision engine for selecting AI toolchains, workflows, and supporting systems — for any type of application, product, or organisation.

---

## Vision

ATDE guides users through an interactive assessment to compose the optimal combination of AI tools for every stage of a development lifecycle: from initial idea and specification, through orchestration and multi-agent execution, to deployment, observability, and governance.

The engine is abstract by design — it works with roles and criteria, not product names. Concrete tools are registered separately and scored against the model. The registry is a living system: continuously updated by an autonomous agent and open to community contributions.

---

## Core Principles

- **Abstract & agnostic** — all nodes, datasets, taxonomies, and rules are domain- and technology-agnostic
- **Registry of types** — all extensible value sets live in `model/types/`; no enum values are hardcoded in schemas or code
- **Living registry** — tools are discovered, verified, and updated autonomously; humans retain override rights
- **Controllably extensible** — the model grows in all directions without breaking existing logic
- **Two-mode UX** — simplified wizard for general users, advanced interface for professionals
- **Enterprise-grade by default** — security/compliance, integration depth, observability, cost, and speed of delivery are first-class criteria
- **Security by design** — threat model covers every layer: registry integrity, scoring pipeline, contribution abuse, API and audit trail

---

## Target Audience

| Profile | Entry point |
|---|---|
| Solo builder / solopreneur | Use case first |
| Department / team | Organisational context first |
| Enterprise | Governance and compliance context first |

---

## How It Works

1. **Assessment** — user answers a short context-aware questionnaire (wizard or advanced mode)
2. **EvaluationFrame** — weights and hard rules are derived from the context profile
3. **Scoring** — each candidate tool is scored via a weighted sum across applicable criteria; overrides filter or adjust scores
4. **Recommendation** — ranked list of tools per node with rationale, alternatives, caveats, and confidence level

> Scoring is fully deterministic and rule-based. No AI inference in the scoring pipeline.

---

## Key Evaluation Criteria

| Criterion | Default weight |
|---|---|
| Security / Compliance | 0.25 |
| Integration Depth | 0.20 |
| Observability & Auditability | 0.20 |
| Cost (TCO) | 0.20 |
| Speed of Delivery | 0.15 |

Weights are adjustable per context profile via the EvaluationFrame.

---

## Repository Structure

```
/
├── README.md                        # This file
├── adr/                             # Architecture Decision Records
├── assessment/                      # Assessment flows (solo and org entry points)
│   ├── ASSESSMENT_SOLO.md
│   └── ASSESSMENT_ORG.md
├── docs/                            # Project documentation
│   ├── open-questions.md            # Open decisions and topics for discussion
│   └── session-notes/               # Session notes for continuity
├── model/                           # Abstract decision model
│   ├── METAMODEL.md                 # Core entity schema
│   ├── DECISION_TREE.md             # Layer 0 and Layer 1 decision routing
│   ├── nodes.md                     # 14 abstract node types across 9 lifecycle stages
│   ├── use-cases.md                 # Use case taxonomy (10 top-level categories)
│   ├── criteria.md                  # Evaluation criteria schema and default weights
│   ├── scoring.md                   # Scoring and ranking algorithm (4 phases, worked example)
│   ├── tool-registry.md             # Tool registry schema and contribution guide
│   └── types/                       # Type registries — all extensible value sets
│       ├── README.md                # Index and extension guide
│       ├── lifecycle-statuses.md    # draft | active | deprecated
│       ├── data-sources.md          # agent_discovery | user_submission | manual
│       ├── hosting-types.md         # cloud | self_hosted | hybrid
│       ├── score-values.md          # high | medium | low → 1.0 / 0.5 / 0.0
│       ├── price-signals.md         # low | medium | high
│       └── confidence-levels.md     # high | medium | low + thresholds
└── registry/                        # Concrete tool implementations
    └── tools/                       # One YAML file per registered tool
        ├── perplexity.yaml
        ├── claude-code.yaml
        └── railway.yaml
```

---

## Model Status

| Component | Status |
|---|---|
| Metamodel (core entities) | ✅ Done — v0.1 |
| Decision tree (Layer 0 + 1) | ✅ Done |
| Assessment flows (solo + org) | ✅ Done |
| Node types (14 nodes, 9 stages) | ✅ Done |
| Use case taxonomy (top level) | ✅ Done |
| Criteria schema | ✅ Done (primary criteria) |
| Type registries | ✅ Done — v0.1 |
| Tool registry schema | ✅ Done — v0.3 |
| Scoring & ranking logic | ✅ Done — v0.2 |
| Registry lifecycle / contribution pipeline | ✅ Done |
| Security / threat model | 🔴 Planned — STO-37 |
| UX design | 🔴 Not started |

---

## Registry

The tool registry lives in `registry/tools/`. Each file is a YAML entry describing a concrete tool: its node role, pricing, compliance, integrations, and criteria scores.

Registry entries have a lifecycle defined in [`model/types/lifecycle-statuses.md`](model/types/lifecycle-statuses.md). They are created and updated by an autonomous agent and open to community contribution via pull request.

See [`model/tool-registry.md`](model/tool-registry.md) for the full schema and contribution guide.

---

## Type Registries

All extensible value sets (statuses, sources, score values, confidence levels, etc.) live in [`model/types/`](model/types/). No enum values are hardcoded in schemas or engine code.

Adding a new value = one entry in one file. No schema changes. No code changes.

---

## Security

Security is a cross-cutting concern in ATDE. Key threat vectors include registry poisoning via agent manipulation, user submission abuse, scoring pipeline tampering, and audit trail integrity. A formal threat model is tracked in [STO-37](https://linear.app/stolbunsky/issue/STO-37).

---

## Project Tracking

[AI Toolchain Decision Engine on Linear](https://linear.app/stolbunsky/project/ai-toolchain-decision-engine-c8bec269c408)

---

## Status

🟡 Active design — model layer complete, UX and implementation not started
