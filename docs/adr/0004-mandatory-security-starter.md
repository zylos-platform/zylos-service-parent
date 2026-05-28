# ADR 0004: Mandatory Security Starter Enforcement

- **Status:** Accepted
- **Date:** 2026-05-28
- **Relates to:** zylos-infra-security-starter ADR 0001

## Context

Every Zylos backend service must enforce the platform security stack — JWT
validation, actor-chain authorization, OPA integration, identity
observability — uniformly. That stack ships as `zylos-infra-security-starter`,
which every service inherits via Spring Boot autoconfiguration.

Autoconfiguration only activates if the starter is on the classpath. A
service that forgets to declare the dependency (or removes it during
refactoring, or excludes it deliberately) silently ships with no security
stack — the most dangerous possible failure mode, because it's invisible: the
service builds, starts, and serves traffic with authentication and
authorization simply absent.

We need a build-time guard that fails the build of any service missing the
starter.

## Decision

Enforce the dependency via a **Maven Enforcer `evaluateBeanshell` rule** in
`zylos-service-parent`, active for every child through the parent's
always-on enforcer plugin execution.

The rule, running at the `validate` phase, inspects the child's declared
dependencies and fails the build unless either:

- the child declares `app.zylos:zylos-infra-security-starter`, or
- the child's artifactId is in the `zylos.security-starter.enforcement.exempt`
  property (default: the parent and the starter themselves).

The starter's version is managed in the parent's `<dependencyManagement>`, so
services declare the dependency without a version.

## Why not the alternatives

### Why not a hard dependency in the parent's `<dependencies>`

The cleanest-sounding option — put the starter in the parent's hard
`<dependencies>` so every child inherits it automatically — is impossible:
the starter itself inherits from this parent, so it would depend on itself,
a build cycle. The enforcer rule sidesteps this by checking declared
dependencies rather than injecting one, and by exempting the starter.

### Why not a shared test-jar with an ArchUnit / Class.forName presence test

The original plan was a test-jar in the parent containing a presence test
that services run. Two problems:

1. **POM packaging has no test lifecycle.** `zylos-service-parent` is
   `<packaging>pom</packaging>`; it has no compile/test/jar phases and cannot
   produce a test-jar of compiled test code.
2. **Opt-in isn't enforcement.** Even if the test lived in a separate
   jar-packaged artifact, a service that omits the starter could equally omit
   the test-jar — the very services we most want to catch are the ones that
   wouldn't include the check.

The enforcer rule, by contrast, is inherited and active automatically for
every child without the child opting in. It is true enforcement.

### Why BeanShell

`evaluateBeanshell` is the built-in enforcer mechanism for custom conditions
without authoring and publishing a separate Java enforcer-rule artifact. The
script uses classic Java (iterator loop, no streams/lambdas) for interpreter
compatibility. A future move to a compiled custom rule is possible if the
logic grows, but for a single dependency-presence check BeanShell is
proportionate.

## Defense in depth: optional ArchUnit test for services

The enforcer guarantees the dependency is *declared*. Services that want an
additional test-time assertion (and a home for richer architectural rules —
e.g. "no controller bypasses the security config") can add an ArchUnit test
to their own suite. A template is provided in `docs/how-to-use.md`. This is
optional; the enforcer is the authoritative guard.

## Trade-offs Accepted

- **Declared-dependency check, not resolved-classpath check.** The rule
  inspects `project.getDependencies()` (declared, including inherited), not
  the fully-resolved artifact graph. A service that pulls the starter purely
  transitively (without declaring it) would fail the rule — but that's the
  correct posture: the security stack should be an explicit, declared
  dependency, not an accident of transitivity.

- **Exemption is a property, editable per project.** A determined developer
  can add their artifactId to the exemption property to bypass the rule. This
  is acceptable: the goal is to prevent *accidental* omission and make
  deliberate omission a visible, reviewable change (the exemption edit shows
  up in the diff and should trigger the ADR requirement). The guard is a
  tripwire, not a sandbox.

- **BeanShell maintenance.** BeanShell is old and unglamorous. The script is
  small and stable; if it grows, promote to a compiled custom enforcer rule.

## Verification

- A service POM that declares the starter: enforcer passes.
- A service POM that omits it: enforcer fails at `validate` with the guidance
  message.
- The parent's own build and the starter's own build: exempt, pass.
- The gateway (Sub-phase, first real consumer): will be validated against
  this rule on its first build — declaring the starter is part of its setup.

## References

- zylos-infra-security-starter ADR 0001 (starter design)
- Maven Enforcer `evaluateBeanshell`: <https://maven.apache.org/enforcer/enforcer-rules/evaluateBeanshell.html>
