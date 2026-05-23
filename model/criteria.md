# Evaluation Criteria Schema

> Draft v0.1 — 2026-05-23

Criteria are used to evaluate and score tool implementations at each node. They are abstract and apply across all tool types.

## Criteria Schema (draft)

```yaml
id: string
label: string
description: string
weight_default: float      # default importance weight (0.0 – 1.0)
applicable_stages: [stage] # which lifecycle stages this applies to
applicable_user_types: [user_type]
data_type: enum            # boolean | scale | enum | text
options: []                # if enum
```

## Primary Enterprise Criteria

| ID | Label | Description | Default Weight |
|---|---|---|---|
| `security-compliance` | Security / Compliance | Regulatory fit, data residency, approved vendor status, PII/PHI handling | 0.25 |
| `integration-depth` | Integration Depth | Compatibility with existing enterprise ecosystem (IAM, ERP, CRM, CI/CD, DWH) | 0.20 |
| `observability` | Observability & Auditability | Logging, tracing, audit trail, RBAC, compliance evidence | 0.20 |
| `cost-tco` | Cost (TCO) | Subscriptions, API usage, seats, setup, maintenance, hidden operational costs | 0.20 |
| `speed-delivery` | Speed of Delivery | Time-to-value, ease of integration, delivery pipeline impact | 0.15 |

## Additional Criteria (to be defined)

- `vendor-lock-in` — degree of dependency on a single vendor
- `self-hosting` — availability of on-premise / self-hosted option
- `api-access` — programmatic access without UI dependency
- `model-agnostic` — ability to swap underlying AI models
- `community-support` — size and activity of open-source community
- `enterprise-sla` — availability of SLA-backed enterprise support
- `data-privacy` — data not used for training, residency guarantees
