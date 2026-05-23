# Open Questions & Topics for Further Discussion

> This document tracks all topics, open decisions and areas that require further research, discussion or validation. Updated continuously.

---

## 🟡 Model Architecture

- [ ] **Meta-model schema**: What are the canonical abstract node types? What properties does each node have? What are the valid relationship types between nodes?
- [ ] **Use case taxonomy**: How do we define and classify use cases in a way that is abstract, complete and stable over time?
- [ ] **Criteria schema**: How do we formalize evaluation criteria so they can be weighted, combined and extended?
- [ ] **Tool registry structure**: How do tools get registered? What fields are required (name, role, pricing, limits, integrations, compliance, geography, API access)?
- [ ] **Scoring / ranking logic**: How does the engine turn answers into a scored recommendation? Rule-based, weighted matrix, AI-assisted or hybrid?
- [ ] **Versioning**: How do we version the model itself when tools or criteria change?

---

## 🟢 Use Cases

- [ ] Finalize the top-level use case taxonomy (abstract, extensible)
- [ ] Define sub-categories for each major use case type
- [ ] Map typical toolchain patterns per use case type
- [ ] Identify edge cases and hybrid scenarios (e.g. "internal tool that becomes a SaaS")

---

## 🔵 UX & Product

- [ ] **Question flow design**: What is the exact sequence of questions in wizard mode? How does branching work?
- [ ] **Advanced mode**: What additional controls, filters and views does it expose?
- [ ] **Output format**: What does a recommendation look like? Card? Table? Diagram? Exportable doc?
- [ ] **Entry point logic**: Exactly how does the solo vs organization split work in the UI?
- [ ] **Saving / sharing configurations**: Do users need accounts? Anonymous sessions? Shareable links?
- [ ] **Progressive disclosure**: How do we reveal complexity without overwhelming novice users?

---

## 🟣 Toolchain Research

- [ ] **Chat / Spec layer**: Full comparison of Perplexity, Claude, ChatGPT, Gemini, Mistral and others for initial spec writing
- [ ] **Orchestration layer**: n8n, Zapier, Make, LangGraph, CrewAI, AutoGen, Comet, and others — pricing, capabilities, lock-in
- [ ] **Coding agents**: Claude Code, Codex, GitHub Copilot, Cursor, Windsurf, Devin — strengths, limits, pricing, workflow integration
- [ ] **Artifact / knowledge store**: Git, Notion, Confluence, SharePoint, Obsidian — role in the chain
- [ ] **Multi-agent roles**: How to model Architect, Developer, QA, Security, Legal and other agent roles
- [ ] **Hosting & deployment**: Vercel, Netlify, Railway, Fly.io, AWS/GCP/Azure, Cloudflare — pricing, complexity, scalability
- [ ] **Observability stack**: LangSmith, Helicone, Langfuse, Datadog, custom — AI-specific observability
- [ ] **Security & compliance tools**: Per-region analysis (EU AI Act, GDPR, SOC2, HIPAA, etc.)

---

## 🔴 Business & Monetization

- [ ] **Free tier definition**: What is free forever vs. what becomes premium?
- [ ] **Monetization models**: Freemium, API access, enterprise tier, white-label, affiliate/referral for recommended tools
- [ ] **Open-source model**: What license? MIT, Apache 2.0, BSL? What stays open vs. proprietary?
- [ ] **Community contribution model**: How do others contribute new tools, criteria updates, use cases?
- [ ] **Launch strategy**: Where and how to launch (GitHub, Product Hunt, HN, communities)?

---

## ⚪ Technical Implementation

- [ ] **Frontend framework**: What tech stack for the web app itself?
- [ ] **Data storage**: Where does the model live? JSON/YAML in Git, database, headless CMS?
- [ ] **AI-assistance inside the engine**: Does the engine itself use AI to generate explanations and recommendations, or is it purely rule-based?
- [ ] **API design**: What endpoints does the engine expose for external integrations?
- [ ] **Deployment / hosting of the engine itself**: Who hosts it and how?

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
