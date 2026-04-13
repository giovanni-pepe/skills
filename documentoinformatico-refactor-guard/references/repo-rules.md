# DocumentoInformatico Repo Rules

This file records verified repo facts for `C:\Users\giova\Desktop\Progetti\Siag\documentoinformatico`.

## Module Map

- Root project: Maven multi-module aggregator `documentoinformatico`
- Modules declared in root `pom.xml`:
  - `domain`
  - `jpa-out-adapter`
  - `camel-out-adapter`
  - `mail-out-adapter`
  - `application`
  - `camel-in-adapter`
  - `bootstrap`
  - `rest-in-adapter`
  - `eprocs-out-adapter`

## Verified Architecture Constraints

- `README.md` states `domain` and `application` are the core modules.
- `README.md` states `domain` must contain business model, entities, value objects, and abstract
  ports only.
- `README.md` states `application` contains use cases, orchestration, and validators.
- `README.md` states `domain` and `application` must depend only on JDK and internal project
  types.
- `README.md` explicitly disallows Spring, SLF4J, Jackson, Jakarta Validation, Swagger, and other
  infrastructure libraries in `domain` and `application`.
- `bootstrap/src/test/java/.../HexagonalArchitectureTest.java` enforces:
  - `..domain..` cannot depend outside `java..` and `..domain..`
  - `..application..` cannot depend outside `java..`, `..application..`, and `..domain..`
  - `..adapter.in..` cannot depend on `it.bz.prov.documentoinformatico.application.service`
- `bootstrap/src/test/java/.../CoreModulePomBoundariesTest.java` enforces:
  - `domain/pom.xml` declares no dependencies
  - `application/pom.xml` main-scope dependency is only `domain`

## Tooling And Validation Facts

- Java version in root `pom.xml`: 21
- Spring Boot version in root `pom.xml`: 3.2.0
- Camel version in root `pom.xml`: 4.4.1
- MapStruct version in root `pom.xml`: 1.5.5.Final
- Liquibase version in root `pom.xml`: 4.33.0
- Formatting is handled by `spotless-maven-plugin` with `googleJavaFormat` 1.22.0.
- Root `pom.xml` binds Spotless `apply` to Maven phase `validate`.
- `README.md` documents `mvn clean package` as the main project build command.
- Because Spotless runs `apply` in `validate`, root lifecycle commands such as `mvn test`,
  `mvn package`, or `mvn verify` can rewrite files.

## Notable Code Conventions Observed

- `application` uses ports under `application.port.in` and `application.port.out`.
- `application` implementation classes live under `application.service`.
- `application` contains a local logging wrapper:
  `application/src/main/java/it/bz/prov/documentoinformatico/application/support/ApplicationLogger.java`
- `rest-in-adapter` uses Spring MVC resources and MapStruct mappers.
- `rest-in-adapter` contains DTOs under `adapter.in.rest.dto` and mappers under
  `adapter.in.rest.mapper`.
- Constructors are used for dependency injection in inspected classes such as
  `CreateFascicoloService` and `FascicoliResource`.

## Validation Hints

- Prefer static inspection first when the request is review-only.
- Prefer narrow module-level validation over root validation when possible.
- If broad Maven validation is needed, warn that Spotless may rewrite files.

## Explicit Non-Goals For Conservative Refactors

- No REST contract changes by default
- No OpenAPI or Swagger behavior changes by default
- No Liquibase or database schema changes by default
- No messaging contract changes by default
- No module dependency graph changes by default
- No cross-layer ownership changes by default
