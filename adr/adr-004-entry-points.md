# ADR-004: Two Entry Points — Solo vs Organization

**Status**: Accepted
**Date**: 2026-05-23

## Context
Solo developers and enterprise decision makers have fundamentally different starting contexts. Org-level users need to establish hard constraints (compliance, approved vendors, integration landscape) before evaluating tools. Solo users benefit from starting with their use case directly.

## Decision
The engine provides two distinct entry points:
- **Solo / Individual**: question flow starts with the use case, then constraints
- **Organization / Enterprise**: question flow starts with organizational context (industry, size, geography, regulatory environment, existing stack), then narrows to use case and tool selection

A third "quick start" path ("just give me a recommendation") is also provided for users who want to skip the structured flow.

## Consequences
- More complex initial routing logic
- Better recommendation accuracy for both audience types
- Enterprise path naturally filters out non-compliant tools early
