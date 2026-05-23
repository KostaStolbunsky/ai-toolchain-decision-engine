# Abstract Node Types

> Draft v0.1 — 2026-05-23

Each node represents an **abstract functional role** in the AI-assisted development lifecycle. Specific tools and platforms are registered as implementations of these roles.

## Node Schema (draft)

```yaml
id: string                # unique identifier
label: string             # human-readable name
description: string       # what this role does
stage: enum               # which lifecycle stage this belongs to
inputs: [node_id]         # what this node receives
outputs: [node_id]        # what this node produces
criteria: [criteria_id]   # which evaluation criteria apply
implementations: [tool_id] # registered tools for this role
extensible: boolean        # can new implementations be added freely
```

## Lifecycle Stages

1. **Ideation** — problem framing, initial idea, prompt-based spec writing
2. **Specification** — technical requirements, architecture, ADR, API contracts
3. **Knowledge & Artifacts** — storage, versioning, documentation, memory
4. **Orchestration** — workflow coordination, multi-agent management
5. **Implementation** — code generation, coding agents, review
6. **Quality & Validation** — testing, QA, security scanning, compliance check
7. **Deployment** — hosting, CI/CD, release management
8. **Observability** — monitoring, logging, audit, alerting
9. **Operations & Evolution** — maintenance, feedback loops, model updates

## Draft Node List

| Node ID | Label | Stage | Description |
|---|---|---|---|
| `ideation-chat` | Interactive Reasoning Chat | Ideation | AI-powered conversational interface for exploring ideas and creating initial specs |
| `research-agent` | Research Agent | Ideation / Spec | Deep research and information synthesis |
| `spec-writer` | Specification Writer | Specification | Structured technical document and artifact generation |
| `artifact-store` | Artifact Store | Knowledge | Versioned storage for all project documents and outputs |
| `knowledge-base` | Knowledge Base | Knowledge | Long-term, queryable project memory and documentation |
| `orchestrator` | Orchestration Layer | Orchestration | Workflow coordination, agent scheduling, context routing |
| `agent-role` | Agent Role | Orchestration | Abstract multi-agent role (Architect, Dev, QA, Security, Legal, etc.) |
| `coding-agent` | Coding Agent | Implementation | AI-powered code generation, editing and review |
| `test-agent` | Test & QA Agent | Quality | Automated testing, validation and quality assurance |
| `security-agent` | Security Agent | Quality | Security scanning, vulnerability analysis, compliance check |
| `ci-cd` | CI/CD Pipeline | Deployment | Build, test, release automation |
| `hosting` | Hosting Platform | Deployment | Application runtime and infrastructure |
| `observability` | Observability Stack | Observability | Monitoring, tracing, logging, audit trail |
| `governance` | Governance Layer | Observability | Access control, auditability, policy enforcement |
