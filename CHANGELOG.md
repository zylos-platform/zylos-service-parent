# Changelog

All notable changes to this repository will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this repository adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added

- Mandatory security-starter enforcement: a Maven Enforcer `evaluateBeanshell`
  rule fails the build of any service that does not declare
  `app.zylos:zylos-infra-security-starter` (ADR 0004).
- `zylos-infra-security-starter` added to `<dependencyManagement>` (version
  managed centrally; services declare it without a version).
- `zylos.security-starter.enforcement.exempt` property for documented
  exceptions (default: parent and starter only).

### Notes

- ADR 0004 documents the decision and why the enforcer rule (not a test-jar or
  a hard parent dependency) is the correct mechanism given POM packaging and
  the parent↔starter inheritance relationship.
