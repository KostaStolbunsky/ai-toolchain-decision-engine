# Model

This directory contains the abstract decision model that powers ATDE.

The model is technology-agnostic at its core — it defines abstract roles, criteria, and relationships, not specific products or vendors. Concrete tools are registered separately in `registry/` as implementations of abstract node roles.

## Contents

| File | Description |
|---|---|
| [`METAMODEL.md`](METAMODEL.md) | Core entity schema — all model entities and their relationships |
| [`DECISION_TREE.md`](DECISION_TREE.md) | Layer 0 and Layer 1 decision routing logic |
| [`nodes.md`](nodes.md) | 14 abstract node types across 9 lifecycle stages |
| [`use-cases.md`](use-cases.md) | Use case taxonomy — 10 top-level categories |
| [`criteria.md`](criteria.md) | Evaluation criteria schema and default weights |
| [`scoring.md`](scoring.md) | Scoring and ranking algorithm — 4 phases with worked example |
| [`tool-registry.md`](tool-registry.md) | Tool registry schema and contribution guide |
| [`types/`](types/) | Type registries — all extensible value sets |
