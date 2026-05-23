# ADR-001: Full E2E Scope for MVP

**Status**: Accepted
**Date**: 2026-05-23

## Context
Initial question was whether to build a narrow tool selector (e.g. only for the ideation/chat layer) or a full end-to-end recommendation engine covering the entire development lifecycle.

## Decision
Even in MVP, the core value proposition is the **full end-to-end stack** — from initial idea through specification, orchestration, coding agents, deployment and observability. Partial tools already exist; the unique value here is the full chain with alternatives and pricing at every node.

## Consequences
- Broader initial scope, but stronger differentiation
- Requires a more comprehensive meta-model from the start
- MVP output remains simple (recommendation only, no step-by-step guides)
