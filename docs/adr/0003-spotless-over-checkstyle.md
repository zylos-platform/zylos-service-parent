# ADR 0003: Spotless over Checkstyle

- **Status:** Accepted
- **Date:** 2026-04-30
- **Deciders:** Kamesh Chathuranga

## Context

Code formatting consistency is mandatory across all Zylos services. Two
common Java tools: Checkstyle (a linter that reports violations) and
Spotless (a formatter that fixes them).

## Decision

Use **Spotless Maven Plugin 2.46.1** with **Palantir Java Format**.

## Rationale

- **Spotless fixes; Checkstyle nags.** `mvn spotless:apply` autoformats
  all files. Checkstyle would just print 200 violations and require manual
  fixes.
- **Palantir Java Format** is forked from Google Java Format with more
  pleasant defaults: 4-space indentation, 120-character lines (vs. Google's
  4-space + 100). 
- **Single tool, single source of formatting truth.** No bikeshedding in
  PRs about whitespace.

## Trade-offs Accepted

- Spotless runs in `validate` (early in the build), failing fast on
  formatting issues. Slightly slower local builds; in exchange, no
  formatting commits in PRs.

## Alternatives Rejected

- **Checkstyle alone:** doesn't auto-fix.
- **EditorConfig alone:** no enforcement; relies on each developer's IDE
  honoring it.
- **Checkstyle + Spotless:** redundant.
