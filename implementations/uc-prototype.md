# Implementations — uc-prototype

> Version: 0.1  
> Status: Draft  
> Date: 2026-05-24  
> Use case: Prototype / Proof of Concept  
> Typical profile: solo, maturity:early, budget:high or medium, speed matters most

This file registers concrete tool implementations for each node relevant to the `uc-prototype` use case. Scores are initial assessments based on publicly available information and subject to review.

---

## Node coverage for uc-prototype

| Node | Cardinality | Required / Optional |
|---|---|---|
| `ideation-chat` | single | required |
| `spec-writer` | single | optional |
| `artifact-store` | single | required |
| `coding-agent` | single | required |
| `hosting` | single | required |
| `ci-cd` | single | optional |
| `test-agent` | single | optional |
| `observability` | single | optional |

---

## Node: ideation-chat

### perplexity
```yaml
id: perplexity
label: Perplexity AI
node_id: ideation-chat
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # no SOC2 for free tier; Pro has better controls
  integration-depth: low        # no native integrations, API available
  observability: low            # no audit trail
  cost-tco: high                # free tier available; Pro ~$20/mo
  speed-delivery: high          # instant, no setup
notes: "Best for research and idea exploration at the start of a prototype. Not a coding tool."
last_reviewed: 2026-05-24
```

### chatgpt
```yaml
id: chatgpt
label: ChatGPT (OpenAI)
node_id: ideation-chat
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2 for Team/Enterprise plans
  integration-depth: medium     # GPT Actions, API, plugin ecosystem
  observability: low            # limited audit trail on consumer plans
  cost-tco: high                # free tier; Plus $20/mo
  speed-delivery: high          # instant
notes: "Strong for ideation and spec drafting. GPT-4o handles multi-modal input well for rapid prototyping."
last_reviewed: 2026-05-24
```

### claude
```yaml
id: claude
label: Claude (Anthropic)
node_id: ideation-chat
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2 Type II for Teams/Enterprise
  integration-depth: low        # API-first, limited native integrations
  observability: low
  cost-tco: high                # free tier; Pro $20/mo
  speed-delivery: high
notes: "Excellent for long-context reasoning and structured spec writing. Strong for technical documentation."
last_reviewed: 2026-05-24
```

---

## Node: spec-writer

### claude-projects
```yaml
id: claude-projects
label: Claude Projects (Anthropic)
node_id: spec-writer
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium
  integration-depth: low
  observability: low
  cost-tco: high                # included in Claude Pro
  speed-delivery: high
notes: "Projects feature allows persistent context per spec. Excellent for iterative spec refinement."
last_reviewed: 2026-05-24
```

### linear
```yaml
id: linear
label: Linear
node_id: spec-writer
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2
  integration-depth: medium     # GitHub, Figma, Slack integrations
  observability: medium         # activity log, audit trail on Business plan
  cost-tco: high                # free for small teams
  speed-delivery: high
notes: "Not an AI spec tool, but excellent for structuring and tracking specs as issues. Pairs well with AI-generated content."
last_reviewed: 2026-05-24
```

### notion-ai
```yaml
id: notion-ai
label: Notion AI
node_id: spec-writer
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2
  integration-depth: medium     # API, Zapier, Slack
  observability: low
  cost-tco: medium              # Notion free + AI add-on $10/mo
  speed-delivery: high
notes: "Good for collaborative spec writing. AI assists inline. Less structured than dedicated tools."
last_reviewed: 2026-05-24
```

---

## Node: artifact-store

### github
```yaml
id: github
label: GitHub
node_id: artifact-store
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: high     # SOC2, GDPR compliant, private repos
  integration-depth: high       # de facto standard, integrates with everything
  observability: high           # full audit log on Enterprise
  cost-tco: high                # free for public/private repos up to limits
  speed-delivery: high
notes: "Default choice for artifact storage. Version control, issues, CI/CD all in one."
last_reviewed: 2026-05-24
```

### gitlab
```yaml
id: gitlab
label: GitLab
node_id: artifact-store
hosting: cloud
open_source: true
license: MIT (CE) / proprietary (EE)
criteria_scores:
  security-compliance: high
  integration-depth: high
  observability: high
  cost-tco: high                # free tier generous
  speed-delivery: medium        # slightly higher setup friction than GitHub
notes: "Strong self-hosted option. Full DevSecOps platform. Preferred when on-premise is required."
last_reviewed: 2026-05-24
```

---

## Node: coding-agent

### cursor
```yaml
id: cursor
label: Cursor
node_id: coding-agent
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium   # SOC2; privacy mode available
  integration-depth: medium     # VS Code-based, GitHub native
  observability: low
  cost-tco: high                # free tier; Pro $20/mo
  speed-delivery: high
notes: "Best-in-class for solo prototyping. Context-aware code generation, strong autocomplete, agent mode for multi-file edits."
last_reviewed: 2026-05-24
```

### windsurf
```yaml
id: windsurf
label: Windsurf (Codeium)
node_id: coding-agent
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium
  integration-depth: medium
  observability: low
  cost-tco: high                # free tier; Pro $15/mo
  speed-delivery: high
notes: "Strong Cursor alternative. Cascade agent mode handles multi-step tasks well. Slightly more generous free tier."
last_reviewed: 2026-05-24
```

### github-copilot
```yaml
id: github-copilot
label: GitHub Copilot
node_id: coding-agent
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: high     # enterprise controls, IP indemnification
  integration-depth: high       # native GitHub, VS Code, JetBrains, etc.
  observability: medium
  cost-tco: medium              # $10/mo individual; free for students/OSS
  speed-delivery: high
notes: "Best integration with GitHub ecosystem. Copilot Workspace adds agent-style task execution. Lower ceiling than Cursor for pure coding speed."
last_reviewed: 2026-05-24
```

### claude-code
```yaml
id: claude-code
label: Claude Code (Anthropic)
node_id: coding-agent
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium
  integration-depth: medium     # terminal/CLI first, IDE integrations growing
  observability: low
  cost-tco: medium              # usage-based API pricing
  speed-delivery: high
notes: "Strongest for complex multi-file reasoning and architectural tasks. CLI-first makes it excellent for automation pipelines."
last_reviewed: 2026-05-24
```

---

## Node: hosting

### vercel
```yaml
id: vercel
label: Vercel
node_id: hosting
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium
  integration-depth: high       # GitHub-native, framework-first
  observability: medium
  cost-tco: high                # generous free tier; Pro $20/mo
  speed-delivery: high
notes: "Default for frontend/fullstack prototypes. Zero-config deployments from GitHub. Ideal for Next.js, React, etc."
last_reviewed: 2026-05-24
```

### railway
```yaml
id: railway
label: Railway
node_id: hosting
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: low      # no formal compliance certifications
  integration-depth: medium
  observability: medium
  cost-tco: high                # $5 free credit/mo; usage-based
  speed-delivery: high
notes: "Best for backend prototypes. Instant deployments, built-in databases, minimal config. Not for production at scale."
last_reviewed: 2026-05-24
```

### render
```yaml
id: render
label: Render
node_id: hosting
hosting: cloud
open_source: false
license: proprietary
criteria_scores:
  security-compliance: medium
  integration-depth: medium
  observability: medium
  cost-tco: high                # free tier for static/services with limits
  speed-delivery: high
notes: "Good Heroku replacement for prototypes. Static sites free, services spin down on free tier."
last_reviewed: 2026-05-24
```

---

## Recommended stack for uc-prototype (solo, early, budget:high)

```yaml
recommended_stack:
  ideation-chat:  chatgpt or claude      # personal preference
  spec-writer:    claude-projects        # best context retention for specs
  artifact-store: github                 # no alternative at this stage
  coding-agent:   cursor                 # highest time-to-working-code
  hosting:        vercel or railway      # depends on frontend vs backend focus
  ci-cd:          github-actions         # included with GitHub, zero extra cost
  test-agent:     skip for v1            # add when prototype stabilizes
  observability:  skip for v1            # add before user-facing launch
```
