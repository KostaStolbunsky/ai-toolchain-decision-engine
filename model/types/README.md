# Type Registries

> Pattern: Registry of Types  
> Date: 2026-05-25

All extensible value sets in ATDE are defined here as independent type registries. No enum values are hardcoded inline in schemas.

## Principle

Inspired by Kubernetes CRDs, OpenAPI `$ref`, Protobuf type files, and AWS CloudFormation type registries:

- Each type registry is a standalone file in `model/types/`
- Schemas reference types by ID: `# $ref: model/types/<file>.md#<type-id>`
- Adding a new value = adding one entry to one file. No schema changes. No code changes.
- Removing or renaming a value = breaking change, requires major version bump and migration.

## Type Registry Index

| File | Type | Used by |
|---|---|---|
| `lifecycle-statuses.md` | `lifecycle_status` | tool-registry schema |
| `data-sources.md` | `data_source` | tool-registry schema |
| `hosting-types.md` | `hosting_type` | tool-registry schema, METAMODEL Implementation |
| `score-values.md` | `score_value` | tool-registry criteria_scores, scoring algorithm |
| `price-signals.md` | `price_signal` | tool-registry schema |
| `confidence-levels.md` | `confidence_level` | scoring algorithm, Recommendation entity |

## Extending a type

1. Open the relevant file in `model/types/`
2. Add a new entry following the existing format
3. Set `status: active` for the new value
4. Open a PR — no other files need to change

## Deprecating a value

1. Set `status: deprecated` on the entry
2. Add a `deprecated_in` date and `replaced_by` reference if applicable
3. The value remains in the registry for backwards compatibility — never delete
