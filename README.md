# zylos-service-parent

Shared Maven parent POM for all Zylos backend services.

Every service inherits from this. Pin updates here propagate to every
service on its next build. **This is the version control plane for the
backend.**

## Pinned Stack (April 2026)

| Component          | Version                                      |
| ------------------ | -------------------------------------------- |
| Java               | 25 (LTS)                                     |
| Spring Boot        | 4.0.6                                        |
| Spring Cloud       | 2025.1.1 (Oakwood)                           |
| Spring Modulith    | 2.0.5                                        |
| Resilience4j       | 2.4.0 (`resilience4j-spring-boot3` artifact) |
| MapStruct          | 1.6.3                                        |
| Testcontainers     | 2.0.5 (new naming)                           |
| Confluent Platform | 8.0.0                                        |
| Apache Avro        | 1.12.0                                       |

See [`docs/how-to-use.md`](docs/how-to-use.md) for the inheritance guide.
See [`docs/version-policy.md`](docs/version-policy.md) for the bump policy.

## Architecture Decisions

- [ADR 0001: Spring Boot 4 + Java 25](docs/adr/0001-spring-boot-4-java-25.md)
- [ADR 0002: Resilience4j naming oddity](docs/adr/0002-resilience4j-on-spring-boot-4.md)
- [ADR 0003: Spotless over Checkstyle](docs/adr/0003-spotless-over-checkstyle.md)

## Verifying Locally

```bash
mvn -B -ntp validate          # POM well-formed, plugins resolve
mvn -B -ntp dependency:tree   # See the resolved BOM imports
mvn -B -ntp enforcer:enforce  # JDK + Maven version + dependency rules
```

You don't `mvn install` this repo unless you intend to publish it locally
for downstream consumption.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). All version bumps require an ADR
update if the rationale isn't trivial.
