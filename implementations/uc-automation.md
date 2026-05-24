# Implementations — uc-automation

> Version: 0.1  
> Status: Draft  
> Date: 2026-05-24  
> Use case: Automation / Agent Workflow  
> Typical profile: department or enterprise, maturity:growing or established, integration-depth critical

This file registers concrete tool implementations for each node relevant to the `uc-automation` use case. Automation workflows span the widest range of org types — from solo no-code pipelines to enterprise multi-agent orchestration.

---

## Node coverage for uc-automation

| Node | Cardinality | Required / Optional |
|---|---|---|
| `ideation-chat` | single | optional |
| `orchestrator` | single | required |
| `agent-role` | multiple | required |
| `artifact-store` | single | required |
| `knowledge-base` | single | optional |
| `coding-agent` | single | optional |
| `test-agent` | single | optional |
| `observability` | single | required |
| `governance` | single | required (dept/enterprise) |

---

## Node: orchestrator

### n8n
```yaml
id: n8n
label: n8n
node_id: orchestrator
hosting: cloud
open_source: true
license: "Sustainable Use License (source-available)"
criteria_scores:
  security-compliance: medium   # self-hosted option; cloud SOC2 in progress
  integration-depth: high       # 400+ integrations, custom nodes
  observability: medium         # execution logs; no native APM
  cost-tco: high                # self-hosted free; cloud from $24/mo
  speed-delivery: high          # visual builder, fast iteration
notes: "Best balance of power and speed for department-level automation. Self-hosted option solves most compliance needs. Active community."
last_reviewed: 2026-05-24
```

### make
```yaml
id: make
label: Make (formerly Integromat)
node_id: orchestrator
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2 Type II
  integration-depth: high       # 1500+ apps
  observability: medium
  cost-tco: high                # free tier; Core $9/mo
  speed-delivery: high
notes: "Easier than n8n for non-technical users. Weaker for complex agent logic. No self-hosted option."
last_reviewed: 2026-05-24
```

### zapier
```yaml
id: zapier
label: Zapier
node_id: orchestrator
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2
  integration-depth: high       # 6000+ apps
  observability: low            # limited run history on lower plans
  cost-tco: medium              # free tier limited; Starter $29.99/mo
  speed-delivery: high
notes: "Widest app coverage. Best for simple linear automations. Not suitable for complex branching agent logic."
last_reviewed: 2026-05-24
```

### temporal
```yaml
id: temporal
label: Temporal
node_id: orchestrator
hosting: cloud
open_source: true
license: MIT
criteria_scores:
  security-compliance: high     # SOC2; self-hosted available
  integration-depth: medium     # code-first, integrates via SDK
  observability: high           # built-in workflow visibility, replay, retry
  cost-tco: medium              # self-hosted free; cloud usage-based
  speed-delivery: low           # significant engineering investment required
notes: "Enterprise-grade workflow orchestration. Best when reliability, retries, and long-running workflows matter. Requires engineering team."
last_reviewed: 2026-05-24
```

### langchain
```yaml
id: langchain
label: LangChain
node_id: orchestrator
hosting: cloud
open_source: true
license: MIT
criteria_scores:
  security-compliance: medium   # depends on deployment
  integration-depth: high       # LLM-agnostic, wide tool ecosystem
  observability: medium         # LangSmith for tracing
  cost-tco: high                # open source core; LangSmith paid
  speed-delivery: medium        # code-first, Python/JS
notes: "De facto standard for LLM-powered agent orchestration. Best when AI reasoning is core to the workflow, not just a step."
last_reviewed: 2026-05-24
```

---

## Node: agent-role

### openai-assistants
```yaml
id: openai-assistants
label: OpenAI Assistants API
node_id: agent-role
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2; no EU data residency guarantee
  integration-depth: medium     # API-first, function calling
  observability: low            # limited built-in tracing
  cost-tco: medium              # usage-based
  speed-delivery: high
notes: "Fast to prototype agent roles. Function calling + code interpreter. Not suitable for strict data residency requirements."
last_reviewed: 2026-05-24
```

### claude-api
```yaml
id: claude-api
label: Claude API (Anthropic)
node_id: agent-role
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium
  integration-depth: medium
  observability: low
  cost-tco: medium
  speed-delivery: high
notes: "Strong for reasoning-heavy agent roles. Long context window (200k) suited to document-processing agents. Model-specific, not model-agnostic."
last_reviewed: 2026-05-24
```

### autogen
```yaml
id: autogen
label: AutoGen (Microsoft)
node_id: agent-role
hosting: cloud
open_source: true
license: MIT
criteria_scores:
  security-compliance: medium   # depends on deployment
  integration-depth: high       # strong Azure/Microsoft ecosystem fit
  observability: medium
  cost-tco: high                # open source
  speed-delivery: medium
notes: "Best for multi-agent role definition in Microsoft-stack environments. Studio UI lowers barrier for non-engineers."
last_reviewed: 2026-05-24
```

### crewai
```yaml
id: crewai
label: CrewAI
node_id: agent-role
hosting: cloud
open_source: true
license: MIT
criteria_scores:
  security-compliance: low      # early stage, limited enterprise controls
  integration-depth: medium
  observability: medium
  cost-tco: high                # open source core
  speed-delivery: high
notes: "Role-based multi-agent framework. Fast to define crews with distinct roles. Best for early-stage automation prototypes, not enterprise production."
last_reviewed: 2026-05-24
```

---

## Node: knowledge-base

### notion
```yaml
id: notion
label: Notion
node_id: knowledge-base
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2
  integration-depth: medium     # API, Zapier, Slack
  observability: low
  cost-tco: high                # free; Plus $10/mo
  speed-delivery: high
notes: "Good for team knowledge bases feeding automation workflows. Not a vector DB. Pairs with RAG pipelines via API."
last_reviewed: 2026-05-24
```

### confluence
```yaml
id: confluence
label: Confluence (Atlassian)
node_id: knowledge-base
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: high     # SOC2, GDPR, enterprise controls
  integration-depth: high       # Jira, Slack, 3000+ apps via Marketplace
  observability: high
  cost-tco: medium              # free up to 10 users; Standard $5.16/user/mo
  speed-delivery: medium
notes: "Enterprise standard for knowledge management. Strong compliance posture. Rovo AI adds intelligent search and automation."
last_reviewed: 2026-05-24
```

### pinecone
```yaml
id: pinecone
label: Pinecone
node_id: knowledge-base
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2
  integration-depth: medium     # API-first, LangChain/LlamaIndex native
  observability: medium
  cost-tco: medium              # free tier; Starter $70/mo
  speed-delivery: high
notes: "Best managed vector DB for RAG-based automation workflows. Pairs naturally with LangChain orchestration."
last_reviewed: 2026-05-24
```

---

## Node: observability

### langsmith
```yaml
id: langsmith
label: LangSmith (LangChain)
node_id: observability
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium
  integration-depth: high       # native LangChain, OpenAI, Anthropic
  observability: high           # traces, evals, prompt management
  cost-tco: medium              # Developer free; Plus $39/mo
  speed-delivery: high
notes: "De facto observability layer for LLM-based automation. Traces every agent step. Essential when LangChain is the orchestrator."
last_reviewed: 2026-05-24
```

### datadog
```yaml
id: datadog
label: Datadog
node_id: observability
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: high     # SOC2, HIPAA, PCI DSS, FedRAMP
  integration-depth: high       # 750+ integrations
  observability: high
  cost-tco: low                 # expensive; usage-based
  speed-delivery: medium
notes: "Enterprise-grade observability. LLM Observability module covers AI-specific monitoring. High cost but full compliance coverage."
last_reviewed: 2026-05-24
```

### helicone
```yaml
id: helicone
label: Helicone
node_id: observability
hosting: cloud
open_source: true
license: Apache 2.0
criteria_scores:
  security-compliance: medium
  integration-depth: medium     # OpenAI, Anthropic, proxy-based
  observability: high           # LLM-specific: cost, latency, prompts
  cost-tco: high                # free tier generous
  speed-delivery: high
notes: "Best lightweight LLM observability for cost and latency tracking. Simple proxy integration. Not a full APM."
last_reviewed: 2026-05-24
```

---

## Recommended stacks for uc-automation

### Solo / small team (early, budget:high)
```yaml
recommended_stack:
  orchestrator:   n8n (cloud)           # visual, fast, generous free tier
  agent-role:     openai-assistants     # fast to prototype
  artifact-store: github
  knowledge-base: notion                # optional, easy to set up
  observability:  helicone              # lightweight, free tier
  governance:     skip for v1
```

### Department (growing, stack:mixed, compliance:[gdpr])
```yaml
recommended_stack:
  orchestrator:   n8n (self-hosted)     # GDPR solved via self-hosting
  agent-role:     langchain + claude-api
  artifact-store: github or gitlab
  knowledge-base: pinecone              # RAG for document-heavy workflows
  observability:  langsmith
  governance:     to be defined in org governance layer
```

### Enterprise (established, stack:microsoft, compliance:[soc2])
```yaml
recommended_stack:
  orchestrator:   temporal              # reliability, retry, long-running
  agent-role:     autogen               # Microsoft ecosystem fit
  artifact-store: github-enterprise or gitlab-ee
  knowledge-base: confluence + pinecone
  observability:  datadog               # full compliance coverage
  governance:     required — see governance node
```
