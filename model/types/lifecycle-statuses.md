# Type Registry: Lifecycle Statuses

> Type ID: `lifecycle_status`  
> Version: 0.1  
> Date: 2026-05-25  
> Used by: tool-registry schema (`status` field)

Defines the valid lifecycle states for tool registry entries.

## Values

| ID | Label | Description | Status |
|---|---|---|---|
| `draft` | Draft | Entry created but not yet verified. Not included in scoring engine output. | active |
| `active` | Active | Verified and live. Included in scoring engine. | active |
| `deprecated` | Deprecated | Tool discontinued, replaced, or no longer recommended. Excluded from scoring. Retained for audit history. | active |

## Transition rules

```
draft  ──► active      (after verification pipeline passes)
active ──► deprecated  (on discontinuation, replacement, or quality failure)
draft  ──► deprecated  (if submission is rejected after review)
```

Transitions are performed by the registry agent or a maintainer. Backwards transitions (e.g. `deprecated → active`) require explicit justification in the PR.

## Extension rules

New statuses may be added when an existing value cannot represent a new lifecycle state. Candidate examples (not yet active):
- `archived` — historical record, predates current schema
- `under_review` — flagged for re-verification
- `suspended` — temporarily excluded pending investigation

Add new values here only. No other files need to change.
