# Type Registry: Decision Path Statuses

> Type ID: `decision_path_status`
> Version: 0.1
> Date: 2026-05-26
> Used by: `DecisionPath.status`

| ID | Label | Description | Status |
|---|---|---|---|
| `in_progress` | In Progress | Path is actively being traversed. | active |
| `complete` | Complete | All required nodes evaluated, recommendation generated. | active |
| `abandoned` | Abandoned | User exited before completion. | active |
