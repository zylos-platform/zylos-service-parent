# How to Use the Zylos Service Parent

This document explains how a Zylos backend service inherits from
`zylos-service-parent` and what it gets for free.

## Inheriting

In the service's `pom.xml`:

```xml
<parent>
    <groupId>app.zylos</groupId>
    <artifactId>zylos-service-parent</artifactId>
    <version>0.1.0</version>
    <relativePath/>
</parent>

<groupId>app.zylos</groupId>
<artifactId>zylos-catalog-service</artifactId>
<version>0.1.0-SNAPSHOT</version>
<packaging>jar</packaging>
```

That's it. By inheriting, the service gets:

### Automatic Behavior
- Java 25 compilation with `-parameters` and warnings-as-errors.
- Spotless formatting check on `validate`.
- JaCoCo coverage check on `verify` (70% line coverage minimum).
- Maven Enforcer rules: JDK 25, dependency convergence, no duplicates.
- Git SHA / branch / build-time stamped into `git.properties`.

### Always-Included Dependencies
- `spring-boot-starter-actuator` (health/readiness probes).
- `org.jspecify:jspecify` (null-safety annotations).
- `net.logstash.logback:logstash-logback-encoder` (structured JSON logs).
- `spring-boot-starter-test` (test scope).
- `archunit-junit5` (test scope).

### Version-Managed (declare without versions)
- All Spring Boot 4.0.6 dependencies.
- All Spring Cloud 2025.1.1 dependencies.
- Spring Modulith 2.0.5.
- Resilience4j 2.4.0 (artifact: `resilience4j-spring-boot3`).
- MapStruct 1.6.3.
- Testcontainers 2.0.5 (note 2.x naming: `testcontainers-postgresql` etc.).
- Confluent Avro 8.0.0 + Apache Avro 1.12.0.
- WireMock, ArchUnit, JSpecify, uuid-creator.

## Service-Level Conventions

### Required `application.yml` Skeleton

Every service must include at minimum:

```yaml
spring:
  application:
    name: zylos-<service-name>     # MUST match service identity
  profiles:
    active: ${SPRING_PROFILES_ACTIVE:local}

management:
  endpoints:
    web:
      exposure:
        include: health,info,prometheus,metrics
  endpoint:
    health:
      probes:
        enabled: true
      show-details: when-authorized
  health:
    livenessstate:
      enabled: true
    readinessstate:
      enabled: true
  info:
    git:
      mode: full
  opentelemetry:
    resource-attributes:
      service.name: ${spring.application.name}
      service.version: ${project.version}
      deployment.environment: ${spring.profiles.active}
```

### Required Module Layout (Hexagonal)

```
src/main/java/app/zylos/<service>/
├── domain/                  # Aggregates, value objects, domain events
├── application/             # Use cases, application services
├── adapters/
│   ├── inbound/             # REST controllers, Kafka consumers, gRPC
│   └── outbound/            # Repositories, Kafka producers, HTTP clients
└── <Service>Application.java
```

This boundary is enforced by an ArchUnit test you should add per service.

## Updating the Parent

When the parent is updated and released, services receive the change by
bumping the `<parent><version>` in their POM. We use semver; minor versions
are non-breaking.

## Reporting Issues

File issues against `zylos-service-parent` for any cross-service version,
plugin, or build configuration concern.
