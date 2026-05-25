# Threat Model

> Version: 0.1
> Status: Draft
> Date: 2026-05-26
> Owner: STO-37

This document defines the threat model for the ATDE platform. It covers attack surfaces, threat actors, risk levels, and architectural mitigations. This is a living document — revisited as new features are added.

---

## Principles

- Security is a cross-cutting concern — designed in at every layer, not added as a final gate.
- Every major design decision (registry, scoring, API, UX) must include a security consideration.
- Mitigations are architectural first, operational second.
- Transparency over obscurity — threat vectors are published; implementation details of defences are not.

---

## Threat actors

| Actor | Motivation | Capability |
|---|---|---|
| Vendor / affiliate | Promote own tool in registry | Low–Medium |
| SEO / content farm | Synthetic mentions to surface tools | Medium |
| Malicious contributor | Poison registry entry | Low–Medium |
| Compromised agent | Manipulate scoring pipeline | Medium |
| API abuser / scraper | Extract data, DoS, bypass limits | Low–High |
| Insider | Tamper with audit trail or weights | Medium |
| Passive data harvester | Collect contributor/user data | Low |

---

## Threat vectors

### 1. Registry poisoning via agent

**Description:** Agent discovers a tool that was artificially surfaced through SEO manipulation, fake reviews, or synthetic mentions. The tool enters the registry with inflated credibility.

**Risk:** High

**Mitigations:**
- Source credibility scoring applied to every agent-discovered entry (model kept private)
- Multi-source corroboration required before entry is promoted to `active`
- New agent-discovered entries enter registry with `status: pending` by default
- Human review gate for entries flagged as low-credibility

---

### 2. User submission abuse

**Description:** A user submits a tool they are affiliated with (directly or indirectly) without disclosure, inflating its presence in the registry.

**Risk:** Medium

**Mitigations:**
- Conflict-of-interest declaration required on submission form
- `contributed_by` field stored on every registry entry
- Submissions from the same contributor for competing tools in the same node flagged for review
- Community flagging mechanism (future)

---

### 3. Scoring pipeline manipulation

**Description:** A bad actor influences criteria scores or EvaluationFrame weights to favour a specific tool — either via direct data manipulation or by compromising the agent.

**Risk:** High

**Mitigations:**
- Criteria scores stored as immutable records — changes create a new versioned entry
- EvaluationFrame weight changes are logged with actor identity and timestamp
- Scoring logic is rule-based and auditable — no black-box AI scoring at the core
- Agent actions on scoring data are logged in the audit trail

---

### 4. Supply chain integrity

**Description:** A registry entry claims to describe a tool accurately, but the description has been manipulated to misrepresent capabilities, pricing, or compliance status.

**Risk:** High

**Mitigations:**
- `last_verified` field required on all entries — stale entries flagged automatically
- Primary source links required (official docs, pricing page) — not third-party summaries
- Breaking changes to an entry (capability, pricing, license) reset `status` to `pending`
- Human review before `status: active` is restored after a breaking change

---

### 5. API abuse

**Description:** The recommendation API is abused via scraping, automated enumeration, or denial-of-service attacks.

**Risk:** Medium

**Mitigations:**
- Rate limiting applied at the API gateway layer
- API key required for programmatic access above free tier
- Anomaly detection on usage patterns (future)
- Public read endpoints are read-only and return no sensitive contributor data

---

### 6. Audit trail tampering

**Description:** The human override log or agent action log is modified to hide malicious activity or falsify decision history.

**Risk:** High

**Mitigations:**
- Audit log is append-only — no delete or update operations permitted
- Each log entry includes a hash of the previous entry (hash chain)
- Log integrity verification endpoint for operators
- Separate storage from primary database with restricted write access

---

### 7. Data privacy in contributions

**Description:** Contributor or user data collected during submissions or assessment flows is exposed, misused, or retained beyond its purpose.

**Risk:** Medium

**Mitigations:**
- Data minimisation by design — collect only what is needed
- Anonymous assessment sessions by default (no account required for MVP)
- Contributor identity stored only in audit trail, not in public registry entries
- GDPR-compliant data retention policy required before public launch
- `contributed_by` field in registry entries stores a pseudonymous identifier, not PII

---

## Risk summary

| Vector | Risk | Status |
|---|---|---|
| Registry poisoning via agent | High | Mitigated (architectural) |
| User submission abuse | Medium | Mitigated (architectural) |
| Scoring pipeline manipulation | High | Mitigated (architectural) |
| Supply chain integrity | High | Mitigated (architectural) |
| API abuse | Medium | Mitigated (architectural) |
| Audit trail tampering | High | Mitigated (architectural) |
| Data privacy in contributions | Medium | Mitigated (architectural) |

> All mitigations above are architectural design decisions. Operational and implementation details are addressed separately during build.

---

## Out of scope (v0.1)

- Source credibility scoring algorithm (private — see STO-37)
- Infrastructure-level security (covered in STO-38)
- Pen testing and vulnerability assessment (post-MVP)
- Compliance certification (EU AI Act, SOC2, HIPAA) — future milestone
