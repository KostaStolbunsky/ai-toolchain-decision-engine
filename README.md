# AI Toolchain Decision Engine (ATDE)

> An abstract, domain-agnostic decision engine for selecting AI toolchains, workflows, and supporting systems — for any type of application, product, or organisation.

🟡 **Status:** Active design — model layer complete, UX and implementation not started

---

## What it does

ATDE guides users through an interactive assessment to compose the optimal combination of AI tools for every stage of a development lifecycle: from initial idea and specification, through orchestration and multi-agent execution, to deployment, observability, and governance.

The engine is abstract by design — it works with roles and criteria, not product names. Concrete tools are registered separately and scored against the model. The registry is a living system: continuously updated by an autonomous agent and open to community contributions.

---

## How it works

1. **Assessment** — user answers a short context-aware questionnaire (wizard or advanced mode)
2. **EvaluationFrame** — weights and hard rules are derived from the context profile
3. **Scoring** — each candidate tool is scored via a weighted sum across applicable criteria; overrides filter or adjust scores
4. **Recommendation** — ranked list of tools per node with rationale, alternatives, caveats, and confidence level

Scoring is fully deterministic and rule-based. No AI inference in the scoring pipeline.

---

## Core principles

- **Abstract & agnostic** — all nodes, datasets, taxonomies, and rules are domain- and technology-agnostic
- **Registry of types** — all extensible value sets live in `model/types/`; no enum values are hardcoded in schemas or code
- **Living registry** — tools are discovered, verified, and updated autonomously; humans retain override rights
- **Controllably extensible** — the model grows in all directions without breaking existing logic
- **Enterprise-grade by default** — security/compliance, integration depth, observability, cost, and speed of delivery are first-class criteria

---

## Navigation

| Area | Location |
|---|---|
| Decision model | [`model/`](model/) |
| Assessment flows | [`assessment/`](assessment/) |
| Tool registry | [`registry/`](registry/) |
| Architecture decisions | [`adr/`](adr/) |
| Project documentation | [`docs/`](docs/) |

---

## Project tracking

[AI Toolchain Decision Engine on Linear](https://linear.app/stolbunsky/project/ai-toolchain-decision-engine-c8bec269c408)
