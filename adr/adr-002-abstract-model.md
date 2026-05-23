# ADR-002: Abstract Agnostic Meta-Model

**Status**: Accepted
**Date**: 2026-05-23

## Context
The AI toolchain market changes rapidly. A model built around specific product names would become outdated quickly and require constant structural rewrites.

## Decision
All nodes, datasets, categories, taxonomies and rules in the internal model must be **fully abstract and technology-agnostic**. Specific tools and platforms are registered as implementations of abstract roles, not as first-class model entities. The model must be controllably extensible in all directions without breaking existing logic.

## Consequences
- Higher initial design effort
- Long-term stability and maintainability
- Supports community contribution without model breakage
- Requires clear separation between canonical model layer and tool registry layer
