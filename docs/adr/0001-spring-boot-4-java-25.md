# ADR 0001: Spring Boot 4 + Java 25 LTS

- **Status:** Accepted
- **Date:** 2026-04-30
- **Deciders:** Kamesh Chtahuranga

## Context

Zylos is a greenfield platform built in 2026. We must choose a Java + Spring
Boot baseline that maximizes (a) modernity, (b) ecosystem maturity,
(c) long support window.

## Decision

- **Java 25 (LTS).** Supported through 2031 minimum.
- **Spring Boot 4.0.x.** Latest major; supports Java 25 as first-class.

## Rationale

- Java 25 LTS: virtual threads (mature), pattern matching, record patterns,
  stable values — foundational features for the architecture.
- Spring Boot 4: native `spring-boot-starter-opentelemetry`, JSpecify
  null-safety portfolio-wide, Jakarta EE 11, modular jars, API Versioning
  built-in.
- Spring Boot 3.5.x reaches end-of-OSS-support on June 30, 2026 — only ~2
  months from project start. Building on it would force an immediate
  upgrade.

## Trade-offs Accepted

- Some third-party libraries lag on SB 4 support (e.g., Resilience4j's
  artifact still named `resilience4j-spring-boot3` even though 2.4.0 added
  SB 4 support). We document each oddity in the parent POM.

## Alternatives Rejected

- **Spring Boot 3.5 + Java 21.** Stable but EOL is imminent (Jun 2026);
  uneconomical for a new project.
- **Spring Boot 3.5 + Java 25.** Incoherent — Java 25 is recommended for
  Spring Boot 4, not 3.5.
