# Registry

> Date: 2026-05-26  
> Related ADR: [ADR-005 — Data Storage Architecture](../adr/adr-005-data-storage-architecture.md)

This directory defines the tool registry structure. Understanding the split between **seed data** (Git) and **live data** (database) is essential before contributing.

---

## Two-layer registry

### `registry/tools/seed/` — Git

Contains **10–20 canonical tool entries** used for:
- Local development and testing
- Contributor onboarding and examples
- Schema validation reference
- CI/CD pipeline fixtures

Seed entries are **not** the live registry. They are curated examples that demonstrate the schema correctly and cover diverse node types.

**This is the only registry data that lives in Git.**

### Live registry — Database (Turso / libSQL)

The production registry lives in a libSQL database, not in Git. It contains all active tool entries and is managed by:

- The autonomous registry agent (`agent_discovery` entries)
- Community contributors via the submission pipeline (`user_submission` entries)
- Maintainers directly (`manual` entries)

All live entries follow the same schema as seed entries. See [`model/tool-registry.md`](../model/tool-registry.md) for the full schema.

---

## `registry/schema/`

Database migration files — versioned SQL. These define the DB schema and evolve it over time. All migrations are in Git and applied in order.

---

## Contributing a tool

1. Check [`model/tool-registry.md`](../model/tool-registry.md) for the current schema
2. If adding a seed example: create a YAML in `registry/tools/seed/` and open a PR
3. If submitting a live entry: use the submission pipeline (coming soon) or open a PR with a YAML in `registry/tools/seed/` marked `data_source: user_submission`
4. The review checklist is applied automatically to all submissions

---

## Schema migrations

When the tool registry schema changes:

1. Update `model/tool-registry.md` (schema definition)
2. Add a migration file to `registry/schema/` (e.g. `002_add_region_field.sql`)
3. Update seed entries to conform to the new schema
4. Bump `schema_version` in all seed YAML files

See [ADR-005](../adr/adr-005-data-storage-architecture.md) for the full storage architecture rationale.
