# ADR-005: Data Storage Architecture

> Status: Accepted  
> Date: 2026-05-26  
> Deciders: project maintainers  
> xleo ref: STO-38

---

## Context

The tool registry will grow to potentially millions of entries. Assessment sessions, scoring history, and audit trails are unbounded data. Git is not a database — using it as primary storage for high-cardinality data causes:

- Git clone/fetch time grows linearly with file count
- GitHub API rate limits hit at scale (5000 req/hr unauthenticated)
- No query capability — full scans only
- No transactional writes
- Disk I/O cost per record is prohibitive at 100k+ files

At the same time, ATDE is an open source project with no infrastructure budget. The storage solution must be free (or near-free) at MVP and early growth stages, and scale gracefully without financial cliffs.

---

## Decision

Adopt a **layered storage architecture** with strict separation of concerns:

### Layer 0 — Git (schema and configuration)

Git remains the source of truth for everything that is **defined, not collected**:

- `model/` — all abstract model files
- `model/types/` — all type registries
- `adr/` — architecture decisions
- `assessment/` — assessment flow definitions
- `registry/tools/seed/` — 10–20 canonical seed entries for development, testing, and contributor onboarding
- DB schema migrations (versioned, in Git)

Rule: **if it's defined once and rarely changes, it belongs in Git.**

### Layer 1 — Turso / libSQL (primary structured storage)

All high-cardinality, queryable, mutable data lives in a **libSQL database** (Turso):

- `tools` — full registry, millions of records
- `tool_scores` — precomputed scores per tool per criteria (materialized)
- `assessment_sessions` — user sessions and context profiles
- `audit_log` — all registry mutations with source, timestamp, actor

**Why Turso / libSQL:**
- SQLite-compatible — zero vendor lock-in, portable
- Edge-distributed — low latency globally
- Free tier: 500 DBs, 9 GB storage, 1B row reads/month
- Full SQL with FTS5 (full-text search, built-in, free)
- No connection pooling issues, no sleep/pause on inactivity

**Why not PostgreSQL / Supabase:** free tier pauses DB after 1 week inactivity, connection limits hit early, overkill for our access patterns.

**Why SQL over NoSQL for registry:** tool entries have a fixed, relational schema. Joins between tools, scores, and nodes are required. SQL guarantees and query planner outperform document stores for this use case.

### Layer 2 — NoSQL (session and variable-schema data)

Assessment sessions have a variable structure — `ContextProfile` differs per user type (solo vs org vs enterprise). This is a document store use case:

- **Firestore** (Google, free tier: 1GB storage, 50k reads/day) or
- **MongoDB Atlas** (free tier: 512MB shared cluster)

This layer is **optional at MVP** — sessions can be stored in Turso initially and migrated later if schema variance requires it.

### Layer 3 — Redis (hot cache)

Scoring results for a given `{ContextProfile hash + node_id}` are deterministic and expensive to recompute. Cache them:

- **Upstash Redis** — serverless, free tier: 10k commands/day, 256MB
- Cache key: `score:{node_id}:{context_profile_hash}`
- TTL: 24h (invalidated on registry mutation for affected node)
- Cache miss — compute from Turso, write to cache, return

This is the **read-through cache** pattern. Second user with identical profile gets <1ms response.

---

## Storage Abstraction Layer

All application code interacts with storage through a **typed interface**, never directly with a provider SDK:

```typescript
interface RegistryStorage {
  getTools(filter: ToolFilter, cursor?: string): PagedResult<Tool>
  getTool(id: string): Tool | null
  upsertTool(tool: Tool): void
  deprecateTool(id: string, reason: string): void
  getAuditLog(toolId: string): AuditEntry[]
}

interface ScoringCache {
  get(nodeId: string, profileHash: string): ScoringResult | null
  set(nodeId: string, profileHash: string, result: ScoringResult, ttl: number): void
  invalidate(nodeId: string): void
}
```

Adapters implement the interface:
- `TursoRegistryStorage implements RegistryStorage`
- `CloudflareD1RegistryStorage implements RegistryStorage` (failover)
- `UpstashScoringCache implements ScoringCache`
- `InMemoryScoringCache implements ScoringCache` (dev/test)

**Swapping a provider = replacing one adapter. Zero business logic changes.**

---

## Failover Strategy

**Not required at MVP.** Tracked as a future milestone.

Target pattern: **Active-Passive with automatic failover**

- Primary: Turso
- Replica: Cloudflare D1 (also SQLite-compatible, free tier: 5GB, 25M reads/day)
- Continuous replication via Turso’s embedded replica feature
- Healthcheck at edge (Cloudflare Workers, free)
- On primary failure: DNS/routing switches to D1 replica automatically
- RTO target: <30 seconds
- RPO target: <60 seconds (replication lag window)

Both Turso and D1 are SQLite-compatible — the same adapter interface works for both. Failover is a routing change, not a data migration.

---

## Scalability Path

| Scale | Storage config | Cost |
|---|---|---|
| MVP (0–1k tools) | Turso free + Git seed | $0 |
| Growth (1k–50k tools) | Turso free + Upstash cache | $0 |
| Scale (50k–500k tools) | Turso Pro + D1 replica + Redis | ~$30/mo |
| Production (500k+ tools) | Turso + sharding by node_id + Redis | ~$100/mo |

Sharding is natural: data is already logically partitioned by `node_id`. No schema changes needed to shard later.

---

## What This Means for the Repository

- `registry/tools/` is renamed to `registry/tools/seed/` — canonical examples only, not the live registry
- A `registry/schema/` directory holds DB migration files (versioned SQL)
- `registry/README.md` explains the full storage architecture for contributors
- Live data lives in Turso, not in Git

---

## Rejected Alternatives

| Option | Reason rejected |
|---|---|
| Git-only (YAML files) | Does not scale past ~1k entries; no query capability; rate limits |
| PostgreSQL / Supabase | Free tier pauses; connection limits; overkill for our SQL needs |
| MongoDB as primary | No joins; schema is actually fixed and relational |
| Elasticsearch | Expensive; overkill; SQLite FTS5 covers our search needs |
| S3 / object storage | No query capability; read latency too high for scoring pipeline |

---

## Consequences

**Positive:**
- Scales to millions of records with no architectural changes
- Zero cost at MVP, low cost at scale
- Provider-agnostic via abstraction layer
- Failover possible without data migration
- Full-text search built in (SQLite FTS5)

**Negative:**
- Registry is no longer fully visible in Git (only seed entries)
- Contributors cannot browse all tools via GitHub UI
- Requires a running DB for full local development (mitigated by seed files + in-memory adapter for tests)

---

## Open questions

- [ ] Should `tool_scores` be recomputed on every scoring request, or only on registry/EvaluationFrame mutation? (precompute vs lazy)
- [ ] What is the public API surface for registry reads? REST? GraphQL? Read-only SDK?
- [ ] Should the seed set be auto-synced to DB on deploy, or managed separately?
