# Open Questions & Topics for Further Discussion

> This document tracks all topics, open decisions and areas that require further research, discussion or validation. Updated continuously.

---

## 🟡 Model Architecture

- [ ] **DecisionPath steps**: Should steps map 1:1 to assessment questions, or can one step span multiple questions?
- [ ] **Recommendation output type**: Should `Recommendation.output_type` be set by the engine or by the user's preference?
- [ ] **ContextProfile mutability**: Should a ContextProfile be mutable during the assessment flow, or locked after the first step?
- [ ] **EvaluationFrame override conflict resolution**: How are conflicting overrides resolved — first match, priority order, or union?
- [ ] **Agent pipeline — source credibility scoring**: How does the agent evaluate the trustworthiness of a data source when discovering new tools? Needs a credibility model to resist manipulation.

---

## 🟢 Use Cases

- [ ] Define sub-categories for each major use case type
- [ ] Fill `lifecycle_emphasis` and `example_patterns` per use case in the taxonomy schema
- [ ] Map typical toolchain patterns per use case type
- [ ] Identify edge cases and hybrid scenarios (e.g. "internal tool that becomes a SaaS")

---

## 🔵 UX & Product

- [ ] **Question flow design**: What is the exact sequence of questions in wizard mode? How does branching work?
- [ ] **Advanced mode**: What additional controls, filters and views does it expose?
- [ ] **Output format**: What does a recommendation look like? Card? Table? Diagram? Exportable doc?
- [ ] **Assessment entry point boundary**: Q0 answer B (department) vs C (enterprise) — headcount, budget authority, or governance model?
- [ ] **Multi-select**: Should use case selection in Layer 1A allow multi-select, or single primary use case only?
- [ ] **Saving / sharing configurations**: Do users need accounts? Anonymous sessions? Shareable links?
- [ ] **Progressive disclosure**: How do we reveal complexity without overwhelming novice users?
- [ ] **Max steps per layer**: How many questions maximum before user abandonment risk? Hypothesis: max 5.
- [ ] **review_required UX**: How does the UI surface low-confidence recommendations? Warning banner, confidence indicator, or separate "uncertain" state?

---

## 🟣 Toolchain Research

- [ ] **Chat / Spec layer**: Full comparison of Perplexity, Claude, ChatGPT, Gemini, Mistral for initial spec writing
- [ ] **Orchestration layer**: n8n, Zapier, Make, LangGraph, CrewAI, AutoGen — pricing, capabilities, lock-in
- [ ] **Coding agents**: Claude Code, Codex, GitHub Copilot, Cursor, Windsurf, Devin — strengths, limits, pricing
- [ ] **Artifact / knowledge store**: Git, Notion, Confluence, SharePoint, Obsidian — role in the chain
- [ ] **Multi-agent roles**: How to model Architect, Developer, QA, Security, Legal agent roles
- [ ] **Hosting & deployment**: Vercel, Netlify, Railway, Fly.io, AWS/GCP/Azure, Cloudflare
- [ ] **Observability stack**: LangSmith, Helicone, Langfuse, Datadog, custom
- [ ] **Security & compliance tools**: Per-region analysis (EU AI Act, GDPR, SOC2, HIPAA)

---

## 🔴 Business & Monetization

- [ ] **Free tier definition**: What is free forever vs. what becomes premium?
- [ ] **Monetization models**: Freemium, API access, enterprise tier, white-label, affiliate/referral
- [ ] **Open-source model**: What license? MIT, Apache 2.0, BSL?
- [ ] **Community contribution model**: How do others contribute new tools, criteria updates, use cases?
- [ ] **Launch strategy**: Where and how to launch (GitHub, Product Hunt, HN, communities)?

---

## 🔒 Platform Security

> Dedicated section. Security is a cross-cutting concern that touches every layer of the platform.
> Full threat model and design tracked in STO-37.

- [ ] Registry poisoning via agent manipulation
- [ ] User submission abuse and conflict-of-interest detection
- [ ] Scoring pipeline manipulation
- [ ] Supply chain integrity verification
- [ ] API abuse: rate limiting, authentication, prevention
- [ ] Data privacy in contributions (GDPR implications)
- [ ] Audit trail integrity

---

## ⚪ Technical Implementation

- [ ] **Frontend framework**: What tech stack for the web app?
- [ ] **AI-assistance inside the engine**: Rule-based, AI-assisted, or hybrid recommendation?
- [ ] **API design**: What endpoints does the engine expose for external integrations?
- [ ] **Layer 1C governance filter**: Should `governance: centralized` immediately filter out non-approved tools, or just reweight?

---

## ✅ Resolved Decisions

| Decision | Resolution | Date |
|---|---|---|
| MVP output depth | Simple recommendation with alternatives — no step-by-step guides | 2026-05-23 |
| E2E vs single-layer focus | Full E2E stack from idea to production | 2026-05-23 |
| Abstract model | Yes — all nodes, data and rules must be abstract/agnostic | 2026-05-23 |
| Dual UX mode | Wizard (simple) + Advanced (professional) | 2026-05-23 |
| Entry point logic | Solo: use case first; Organization: org context first | 2026-05-23 |
| Primary focus | Enterprise-grade production-ready app as north star | 2026-05-23 |
| Distribution | Free public online service, open-source, future monetization | 2026-05-23 |
| Meta-model schema | Defined in model/METAMODEL.md — 6 core entities, connection types, properties taxonomy | 2026-05-24 |
| Versioning strategy | Semantic versioning rules defined in METAMODEL.md | 2026-05-24 |
| Use case taxonomy — top level | 10 top-level use case IDs defined in model/use-cases.md | 2026-05-24 |
| Decision tree — first layer | Layer 0 and Layer 1 (solo, department, enterprise) defined in model/DECISION_TREE.md | 2026-05-24 |
| Assessment flows | Solo and org entry point flows defined in assessment files | 2026-05-24 |
| Assessment terminology | Renamed quiz → assessment across all model and assessment files | 2026-05-24 |
| Tool registry schema | YAML-in-Git, seed/live split, schema in model/tool-registry.md | 2026-05-25 |
| Enum / type strategy | Registry-of-types pattern: all enums live in model/types/, referenced via $ref | 2026-05-26 |
| review_required behaviour | Informational flag only — UI warning, not a blocking gate | 2026-05-25 |
| Contribution pipeline | Single unified pipeline. Source tagged via data_source field | 2026-05-25 |
| Human override model | Audit trail + override right. Human can intervene at any point. Agent is primary. | 2026-05-25 |
| Registry lifecycle fields | status, data_source, contributed_by, last_verified added to registry schema | 2026-05-25 |
| Scoring / ranking logic | Fully defined in model/scoring.md v0.2 (STO-36 Done) | 2026-05-25 |
| Data storage architecture | ADR-005 accepted: Git + Turso + Redis + optional NoSQL | 2026-05-25 |
| Registry as living system | Not static. Agent handles updates autonomously. | 2026-05-25 |
