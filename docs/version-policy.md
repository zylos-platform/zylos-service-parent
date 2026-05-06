# Version Pinning Policy

## Cadence

- **Major bumps (e.g., Spring Boot 4 → 5):** planned migration with ADR.
- **Minor bumps (e.g., 4.0 → 4.1):** in a quiet sprint, with smoke tests.
- **Patch bumps (e.g., 4.0.5 → 4.0.6):** monthly, automated where possible.

## Source of Truth

The `<properties>` block in `pom.xml` is the single source of truth.
Comments in the POM document **why** non-obvious versions are pinned where
they are.

## Bumping a Version

1. Open a PR that updates the property.
2. CI builds the parent (validates the POM).
3. Locally test against at least one downstream service before merging.
4. Tag the parent with a new semver after merge.

## Ban List

Versions we explicitly never use:

| Library      | Banned Version | Reason                               |
| ------------ | -------------- | ------------------------------------ |
| Spring Cloud | 2025.1.0       | Incompatible with Spring Boot 4.0.1+ |
| Spring Boot  | 3.4.x          | EOL since Dec 2025                   |
| Jackson 2    | < 2.21.1       | Older 2.20.x lost upstream support   |
