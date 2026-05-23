# Interactive Decision Engine

> An abstract, agnostic and controllably extensible AI toolchain decision engine — from idea to production.

[![Status](https://img.shields.io/badge/status-concept%2FMVP%20planning-blue)](#)
[![License](https://img.shields.io/badge/license-MIT-green)](#)

## What is this?

An interactive decision engine (quiz/wizard) that helps developers, architects and organizations select the optimal AI toolchain for building web applications — covering every stage from the initial idea and technical specification to orchestration, coding agents, deployment, observability and cost.

Unlike narrow "which AI is best" tools, this engine maps the **full end-to-end development lifecycle** and provides reasoned, comparable recommendations at every node — with alternatives, trade-offs and pricing.

## Core Principles

- **Abstract & agnostic** — all nodes, datasets, categories and rules are technology-agnostic; tools are implementations, not the model itself
- **Controllably extensible** — the model can be expanded in any direction without breaking existing logic
- **Dual-mode UX** — simplified wizard for general users + advanced interface for professionals
- **Two entry points** — solo/individual (use case first) vs organization (org context first)
- **Transparent trade-offs** — every recommendation comes with alternatives, pros/cons and cost

## Target Audience

| User type | Entry point | Primary concern |
|---|---|---|
| Solo developer / solopreneur | Use case | Speed, cost, simplicity |
| Startup / small team | Use case + constraints | Speed, flexibility, TCO |
| Enterprise architect | Org context | Security, compliance, integration depth |
| Enterprise decision maker | Org context | Cost, observability, vendor fit |

## Enterprise Evaluation Criteria

1. **Security / Compliance** — local regulations, data residency, approved vendor lists, PII handling
2. **Integration Depth** — compatibility with existing ecosystem (IAM, ERP, CRM, CI/CD, DWH etc.)
3. **Observability & Auditability** — logging, traceability, audit trail, role-based access, compliance evidence
4. **Cost (TCO)** — subscriptions, API usage, seats, setup, maintenance, hidden operational costs
5. **Speed of Delivery** — time-to-value, ease of integration, impact on delivery pipeline

## Repository Structure

```
/docs
  vision.md           # Project north star
  open-questions.md   # Topics for further discussion and refinement
/model
  README.md           # Meta-model description
  nodes.md            # Abstract node types
  criteria.md         # Evaluation criteria schema
  use-cases.md        # Use case taxonomy (abstract)
/adr
  README.md           # Architecture Decision Records index
/design
  quiz-flow.md        # User journey and question flow design
  ux-modes.md         # Wizard vs Advanced mode
```

## Status

Currently in **concept and MVP planning** phase. The project is being developed iteratively through interactive discussions.

## Contributing

This is planned as an open-source project. Contribution guidelines will be added once the core model is stable.

---

*Built by [Kosta Stolbunsky](https://github.com/KostaStolbunsky) · Prague, CZ*
