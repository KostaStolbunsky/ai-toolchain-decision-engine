# Use Case Taxonomy

> Draft v0.1 — 2026-05-23

Use cases are defined abstractly by **outcome type**, not by specific technology or domain. This ensures the taxonomy remains stable as the toolchain market evolves.

## Taxonomy Schema (draft)

```yaml
id: string
label: string
description: string
typical_user_type: [user_type]
primary_criteria: [criteria_id]
lifecycle_emphasis: [stage]   # which stages are most critical
example_patterns: []          # concrete examples (non-normative)
```

## Top-level Use Case Categories (draft)

| ID | Label | Description |
|---|---|---|
| `uc-prototype` | Prototype / Proof of Concept | Rapid validation of an idea or technical approach |
| `uc-internal-app` | Internal Enterprise Application | App for internal organizational use (tools, portals, dashboards) |
| `uc-public-saas` | Public SaaS Product | Commercial application served to external end users |
| `uc-automation` | Automation / Agent Workflow | AI-powered process automation, RPA replacement, agentic workflows |
| `uc-data-product` | Data / Analytics Product | Data pipelines, BI, ML models served as products |
| `uc-embedded-ai` | AI Feature in Existing Product | Adding AI capabilities to an already-live product |
| `uc-app-modernization` | App Modernization / Legacy Migration | Re-platforming or modernizing existing systems |
| `uc-knowledge-system` | Knowledge / Documentation System | Internal knowledge bases, RAG systems, documentation portals |
| `uc-support-automation` | Support / Service Desk Automation | AI-powered customer or internal support flows |
| `uc-compliance-workflow` | Security / Compliance Workflow | Audit, governance, regulatory compliance automation |

> ⚠️ This list is intentionally non-exhaustive. The taxonomy is designed to be extended without breaking existing logic.
