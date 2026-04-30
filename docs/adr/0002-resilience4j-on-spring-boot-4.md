# ADR 0002: Resilience4j on Spring Boot 4 — Artifact Naming Oddity

- **Status:** Accepted (workaround)
- **Date:** 2026-04-30
- **Deciders:** Kamesh Chathuranga

## Context

Zylos uses Resilience4j for circuit breakers, retries, bulkheads, time
limiters, and rate limiters. As of April 2026, Resilience4j has not
released a `resilience4j-spring-boot4` artifact, despite Spring Boot 4 GA
shipping November 2025.

## Decision

We pin **Resilience4j 2.4.0** and use the **`resilience4j-spring-boot3`**
artifact, which works correctly on Spring Boot 4.

## Rationale

Resilience4j 2.4.0 added Spring Boot 4 / Spring Cloud 5 support
(per the project's release notes). The artifact retains the `spring-boot3`
suffix because internally it depends on `resilience4j-spring6`, which is
forward-compatible with Spring Framework 7 (the engine underneath SB 4).

GitHub issue resilience4j/resilience4j#2371 tracks a possible future
rename to `resilience4j-spring-boot4`.

## Action Items

When the project releases `resilience4j-spring-boot4`, we re-evaluate and
likely migrate. Until then, this is the canonical wiring.
