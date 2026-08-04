# ADR-004 Isolation Strategy

## Decision

Apply galvanic isolation primarily on industrial-facing interfaces (DI, DO, current interfaces, and communications when present).

## Motivation

Reduce cost while still protecting external equipment and limiting ground-loop risk where isolation provides the most value.

## Consequences

- Hybrid isolation instead of isolating every block
- Better cost / robustness balance
- Isolation domain details to be finalized in electrical architecture
