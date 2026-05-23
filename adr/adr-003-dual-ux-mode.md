# ADR-003: Dual UX Mode — Wizard + Advanced

**Status**: Accepted
**Date**: 2026-05-23

## Context
The target audience spans both general users (developers with limited AI toolchain knowledge) and professionals (enterprise architects, technical leads). A single UX mode would either overwhelm beginners or frustrate experts.

## Decision
The engine offers two UX modes:
- **Wizard mode**: step-by-step guided questions, plain language, minimal cognitive load, simplified output
- **Advanced mode**: full access to criteria weights, alternative scoring, node-level configuration, detailed trade-off views

Users can switch between modes at any point.

## Consequences
- Doubles UX design surface area for MVP
- Enables broader audience from launch
- Wizard mode can be shipped first; advanced mode follows in v1.1+
