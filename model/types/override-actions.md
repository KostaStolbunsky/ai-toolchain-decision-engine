# Type Registry: Override Actions

> Type ID: `override_action`
> Version: 0.1
> Date: 2026-05-26
> Used by: `EvaluationFrame.overrides[].action`

| ID | Label | Description | Status |
|---|---|---|---|
| `exclude` | Exclude | Remove implementation from candidates entirely. | active |
| `require` | Require | Force implementation into the recommendation regardless of score. | active |
| `boost` | Boost | Increase the effective score for this implementation. | active |
| `penalize` | Penalize | Decrease the effective score for this implementation. | active |
