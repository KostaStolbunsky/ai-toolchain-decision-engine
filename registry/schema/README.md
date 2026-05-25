# Database Schema Migrations

> Related ADR: [ADR-005](../../adr/adr-005-data-storage-architecture.md)

This directory contains versioned SQL migration files for the ATDE database (Turso / libSQL / SQLite-compatible).

## Conventions

- Files are numbered sequentially: `001_initial_schema.sql`, `002_add_fts.sql`, etc.
- Each file is **append-only** — never edit a migration that has been applied to production
- Destructive changes (rename, remove column) require a new migration + data backfill
- Migrations are applied in numeric order by the deployment pipeline

## Status

No migrations yet — pending implementation phase.
