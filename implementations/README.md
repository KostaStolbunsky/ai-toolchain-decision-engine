# Implementations

This directory contains worked examples of the ATDE model applied to specific use cases. Each file walks through a complete assessment-to-recommendation flow for a defined context.

Implementations are reference material — they demonstrate how the abstract model behaves in practice and serve as validation that the model produces sensible outputs.

## Contents

| File | Use case |
|---|---|
| [`uc-prototype.md`](uc-prototype.md) | Solo builder — rapid prototyping context |
| [`uc-automation.md`](uc-automation.md) | Automation and workflow orchestration context |

## Adding a new implementation

1. Choose a representative use case not already covered
2. Run through the full assessment flow from [`assessment/`](../assessment/)
3. Apply the scoring model from [`model/scoring.md`](../model/scoring.md)
4. Document the resulting recommendation with rationale
5. Name the file `uc-<slug>.md` and add it to the index above
