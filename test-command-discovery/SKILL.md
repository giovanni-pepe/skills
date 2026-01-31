---
name: test-command-discovery
description: Discover and report the repository's real build/test/lint commands by reading wrappers, build configs, and CI pipelines. Use this before running any tests or builds so other skills never guess how to execute unit, integration, verify, or lint tasks.
---

# Test Command Discovery

## Overview

Map the exact commands this repository uses for unit tests, integration tests, verification, and lint or format gates. Operate in read-only mode: rely on wrapper scripts, build definitions, CI workflows, and documentation, and never invent commands. Every reported command must include the file or workflow that proves it exists.

## Quick Use Checklist

- Identify every build surface (wrappers, package managers, custom scripts) before diving into configs.
- Prefer wrapper binaries that live in the repo (`./mvnw`, `./gradlew`, `./nxw`, `./npmw`, `./gradlew.bat`, and so on).
- Read build files and CI workflows to see how tests run in practice; CI gates outrank documentation.
- Report commands under the required headings with short provenance notes (file path plus relevant section or job).

## Workflow

### 1. Inventory modules and build surfaces

1. Scan the root README plus top-level folders (`back-end`, `front-end`, `mobile`, `common`, etc.) to understand the technology stacks and whether commands are module specific.
2. Locate wrappers fast with `rg --files -g '*mvnw*'`, `rg --files -g '*gradlew*'`, `rg --files -g 'package.json'`, `rg --files -g 'nx.json'`, `rg --files -g 'pnpm-workspace.yaml'`, `rg --files -g '*.sln'`, and similar queries.
3. Note dedicated tooling directories (`scripts/`, `tools/`, `config/`, `common/github/`) that may hide helper scripts or meta commands.
4. When wrappers exist, always favor them over global binaries so the local environment mirrors CI.

### 2. Read build definitions for task names

1. Maven: open each `pom.xml`. Look for `maven-surefire-plugin` (unit tests), `maven-failsafe-plugin` (integration tests), custom profiles (`<profile>`), and `<modules>`. Record commands such as `./mvnw test`, `./mvnw verify`, or `./mvnw -pl module-name verify -Pci`.
2. Gradle: inspect `build.gradle(.kts)` and `settings.gradle`. Capture custom test tasks (`test`, `integrationTest`, `e2eTest`) plus any task wiring inside `tasks.register`. Prefer `./gradlew <task>` so the wrapper pins the Gradle version.
3. JavaScript/TypeScript monorepos: read `package.json`, `nx.json`, `project.json`, and `angular.json`. Note script names (`test`, `lint`, `e2e`, `affected:test`) and check which package manager file exists (`pnpm-lock.yaml`, `yarn.lock`, `package-lock.json`) to choose the proper runner (`pnpm`, `yarn`, `npm`, `nx`, `npx`).
4. .NET: inspect `.sln`, `.csproj`, and the guidance docs under `back-end/` such as `dotnet.md` for commands like `dotnet test` or category-filtered runs.
5. Docker or Compose: if integration tests spin services, read `docker-compose*.yml` and any referenced helper scripts for the required setup.
6. Document missing categories explicitly (for example, "No integration test task defined after searching for `failsafe` or `*IT` classes with `rg -n 'IT'`").

### 3. Confirm with CI and scripts

1. Inspect `.github/workflows/*.yml`, `azure-pipelines*.yml`, `Jenkinsfile`, `deployments/**/pipeline*.yml`, or `common/github/*.yml`. Use `rg -n "run:" -C2 .github/workflows` to see the exact commands.
2. CI commands override everything else. If CI runs `pnpm test:ci -- --runInBand`, prefer that over a generic `pnpm test`.
3. Capture per-job environment details (Node version, JDK, profiles, Docker services). If a command is limited to a nightly job, state that limitation.
4. When CI only contains automation like the current `pr-labeler.yml`, note that there is no automated test pipeline and rely solely on build files or documentation.

### 4. Classify commands into the required buckets

- **Unit**: fastest feedback cycle (for example `./mvnw test`, `pnpm test`, or `dotnet test` without filters). Note module scoping when needed (`cd front-end && pnpm test`).
- **Verify/Check**: the superset used for CI gates (`./mvnw verify`, `pnpm nx affected -t lint,test,build`, `./gradlew check`).
- **Integration**: long running suites such as `failsafe:integration-test`, Gradle `integrationTest`, Cypress or Nx `e2e`, Detox, and similar flows. Record prerequisites (Docker, env vars, profiles).
- **Lint/Format (optional)**: only include if CI or standards require it (`pnpm lint`, `nx format:check`, `golangci-lint run`, etc.).

If a bucket does not exist, state "Not defined" and list the searches performed so users know the gap is intentional.

### 5. Publish the verified commands

1. Use the template below verbatim so downstream tasks can copy and paste without editing.
2. For each command include:
   - The exact invocation (prefer relative paths and wrappers).
   - Scope (root vs module path).
   - Provenance in parentheses, e.g., `(source: path/to/file[:line])`.
   - Notes about environments, profiles, or required services.
3. When multiple modules rely on different toolchains, list one command per module under the relevant heading.

## Reporting Template

````markdown
Verified commands (timestamp: <ISO8601 date>)

- Unit:
  - `<command>` (source: `path/to/build-file[:line]`, notes)
  - ...
- Verify/Check:
  - `<command>` (source: `path/to/ci-workflow[:line]`)
- Integration:
  - `<command>` (source: `path/to/failsafe-config[:line]`, mention required services or env vars)
- Lint/Format (optional):
  - `<command>` (source: `path/to/script[:line]`)

If a category is missing, document the searches performed (example: "No integration tests; checked `.github/workflows`, ran `rg -n 'failsafe' -g 'pom.xml'`").
````

## Tips and Edge Cases

- Use targeted ripgrep queries such as `rg -n "integration" -g '*gradle*'`, `rg -n 'failsafe' -g 'pom.xml'`, and `rg -n 'nx affected' -g '*.yml'` to speed up discovery.
- When `pnpm-lock.yaml` or `yarn.lock` exists, never recommend `npm`; mirror the package manager in the lock file or CI step.
- For Nx or Angular repos, check `nx.json` and each `project.json` for target names and default configurations because many commands are stored there instead of `package.json`.
- Confirm whether scripts rely on environment variables (for example `NODE_ENV=test` or `PROFILE=ci`). Include those variables inline with the command.
- If documentation under `common/` (such as `common/code-quality.md` or `common/github/*`) defines mandatory flows, cite that doc as provenance when CI evidence is missing.
- When multiple valid commands exist, pick the one enforced by CI or standards, and move dev-only shortcuts into notes rather than the verified list.
