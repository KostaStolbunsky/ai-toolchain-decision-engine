# Project Brief — AI Toolchain Decision Engine

> Last updated: 2026-05-24
> Status: `discovery`
> Owner: Kosta Stolbunsky

---

## Problem Being Solved

Choosing the right AI toolchain for a real project is complex, fragmented, and context-dependent. No single resource guides a user through the full end-to-end stack selection — from idea to production — taking into account their specific constraints, use case, and organizational context.

---

## Target Audience

- Solo builders and solopreneurs who start from a use case
- Organizations and enterprises who start from organizational context and constraints
- Both technical and non-technical decision-makers

---

## Proposed Solution

An interactive, quiz-style decision engine with two modes: a simplified wizard for general users and an advanced interface for professionals. The engine traverses an abstract, extensible decision model and outputs a recommended AI toolchain with alternatives and trade-offs per layer.

---

## Core Principles

- **Abstract & agnostic** — all nodes, taxonomies, and rules are domain and technology agnostic
- **Controllably extensible** — the model grows in all directions without breaking existing logic
- **Two-mode UX** — wizard for general users, advanced mode for professionals
- **Enterprise-grade by default** — security/compliance, integration depth, observability, cost, speed of delivery are first-class criteria

---

## MVP Scope

**In scope:**
- Interactive decision flow: user type → use case → constraints → priorities
- Abstract decision model traversal
- Output: recommended toolchain per layer with alternatives and trade-offs
- Clean, actionable recommendations — no deep step-by-step plans

**Out of scope:**
- Step-by-step execution plans
- Code generation
- Workflow automation
- Monetization layer

---

## Key Risks and Open Assumptions

| Assumption / Risk | Type | Status |
|---|---|---|
| Users can meaningfully describe their use case through a quiz | assumption | open |
| Enterprise users will trust an open-source tool for toolchain selection | assumption | open |
| The abstract model can be kept current as AI tooling evolves rapidly | risk | open |
| There is sufficient community interest to sustain contributions | assumption | open |

---

## Repository Structure

```
/
├── README.md
├── docs/
├── adr/
└── model/
```
