# ADR-003 Resistance Simulator

## Decision

The module simulates resistances, not temperatures directly.

## Motivation

An ohmic resistance simulator remains universal and compatible with PT100, PT1000, NTC, and other resistive sensors, without embedding sensor-specific temperature curves in Phase 1.

## Consequences

- Simpler Phase 1 implementation
- Broader sensor-path coverage
- Temperature conversion left to the user or to a later software helper
