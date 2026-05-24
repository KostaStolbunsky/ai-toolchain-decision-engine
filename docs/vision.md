# Project North Star — Vision Document

> Last updated: 2026-05-24
> Status: `discovery`
> Owner: Kosta Stolbunsky

## Why this exists

The AI toolchain landscape in 2026 is rich, fragmented and fast-moving. Every week new tools emerge, pricing changes, capabilities evolve. Developers and organizations face hundreds of micro-decisions when building AI-assisted applications — from which chat interface to use for specification writing, to which orchestrator, coding agent, hosting platform and observability stack to choose.

There is no neutral, structured, open tool that helps navigate this space end-to-end with transparent alternatives and pricing. This project aims to build exactly that.

## What we are building

An **interactive decision engine** — part quiz, part recommendation system, part knowledge base — that guides users through the full AI-assisted development lifecycle and recommends the optimal toolchain configuration for their specific context, goals and constraints.

## What makes this different

- **E2E coverage**: from ideation chat to production hosting, including orchestration, multi-agent roles, QA, security, legal, observability and cost
- **Abstract model**: the internal model is fully technology-agnostic; tools are plug-in implementations, not hardcoded nodes
- **Alternatives at every node**: not “the best tool” but “here are 3 options with trade-offs and pricing”
- **Context-aware**: recommendations adapt based on user type (solo vs org), use case, constraints and priorities
- **Transparent pricing**: real cost estimates at every layer, including free tiers, limits and hidden costs

## What we are NOT building (MVP)

- A step-by-step implementation guide or code generator
- A platform to execute or orchestrate workflows
- A tool locked to any specific AI vendor or stack

## Design philosophy

### Abstract & agnostic
All nodes in the model (e.g. “interactive reasoning chat”, “research agent”, “coding agent”, “orchestrator”, “artifact store”, “governance layer”) are abstract roles. Specific products (Claude, Perplexity, n8n, Vercel, etc.) are implementations assigned to these roles based on context.

### Controllably extensible
The model must support addition of new nodes, criteria, use cases and tool implementations without requiring structural changes. New dimensions can be added; existing logic remains valid.

### Dual-mode UX
- **Wizard mode**: step-by-step guided questions, plain language, simplified output
- **Advanced mode**: full access to criteria weights, alternative scoring, node-level configuration

### Two entry points
- **Solo/individual**: start with use case → then constraints
- **Organization/enterprise**: start with org context → then use case → then constraints

## MVP scope

1. Core abstract meta-model (nodes, criteria, use cases, tool registry)
2. Interactive question flow (wizard mode)
3. Simple recommendation output with alternatives and pricing
4. Web-based frontend (public, free)
5. Open-source codebase on GitHub

## Future evolution

- Advanced mode with detailed scoring and comparison
- Shareable/exportable configurations
- API access for integration into other tools
- Community-contributed tool registry updates
- Monetization layer (premium features, enterprise tier)
- Step-by-step implementation guides per recommended stack
