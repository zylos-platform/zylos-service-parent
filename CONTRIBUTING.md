# Contributing to zylos-service-parent

## Branching

- `main` is always deployable.
- Branch off `main` with `feature/<id>-<desc>`, `fix/<id>-<desc>`, or `chore/<desc>`.
- Branches are short-lived (1–2 days).

## Commits

Follow [Conventional Commits](https://www.conventionalcommits.org/):

`<type>(<scope>): <subject>`

Types: `feat`, `fix`, `chore`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `revert`.

All commits must be signed (SSH signing).

## Pull Requests

- All changes via PR (no direct pushes to `main`).
- Squash-merge only.
- CI must be green.
- At least one approving review required.

## Local Development

See [README.md](README.md).

## Mandatory Security Starter

Every Zylos service **must** declare a dependency on the security starter:

```xml

<dependency>
    <groupId>app.zylos</groupId>
    <artifactId>zylos-infra-security-starter</artifactId>
</dependency>
```

No version is needed — it's managed by this parent.

This is enforced at build time by a Maven Enforcer rule (ADR 0004). A service
that omits the starter fails the build at the `validate` phase with a message
explaining the fix. The check is automatic; you don't add anything to your
project to activate it.

If a parent-inheriting project legitimately should not include the starter
(rare — e.g. a pure non-network library), add its artifactId to the
`zylos.security-starter.enforcement.exempt` property and document the exception
in an ADR.
