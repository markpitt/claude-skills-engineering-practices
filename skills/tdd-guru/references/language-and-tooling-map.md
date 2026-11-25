# Language and Tooling Map

Use this map for polyglot recommendations. Keep advice ecosystem-native.

## Python

- Core test framework: `pytest`
- Property-based: `hypothesis`
- Notes:
  - Prefer small deterministic unit tests and clear test discovery conventions.
  - Use strict/explicit test config where practical for maintainability.
  - Source:
    - <https://docs.pytest.org/en/stable/explanation/goodpractices.html>
    - <https://hypothesis.readthedocs.io/en/latest/>

## JVM (Java/Kotlin)

- Core test framework: `JUnit` (current docs at docs.junit.org)
- Mutation testing: `PIT` (pitest)
- Notes:
  - Prefer Jupiter/modern platform APIs over legacy Vintage usage.
  - Treat mutation score as a quality signal, not a vanity metric.
  - Source:
    - <https://docs.junit.org/current/user-guide/>
    - <https://pitest.org/>

## .NET

- Core frameworks: xUnit/NUnit/MSTest (team standard)
- Guidance highlights:
  - fast, isolated, repeatable, self-checking, timely tests
  - test behavior via public methods
  - avoid infrastructure dependencies in unit tests
  - Source:
    - <https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices>

## TypeScript / JavaScript

- Node/library focus: `Vitest` (especially in Vite ecosystems)
- Alternative (non-Vite or existing stack): `Jest`
- Browser E2E: `Playwright`
- Mutation testing: `Stryker`
- Notes:
  - Prefer resilient locators and framework-native waiting over sleeps.
  - Keep E2E coverage targeted to critical user journeys.
  - Source:
    - <https://vitest.dev/guide/>
    - <https://jestjs.io/docs/getting-started>
    - <https://playwright.dev/docs/best-practices>
    - <https://stryker-mutator.io/docs/>

## Services / Microservices (Language-Agnostic)

- Integration confidence strategy:
  1. fast unit/component tests
  2. boundary integration tests
  3. consumer/provider contract tests
  4. few critical end-to-end tests
- Contract testing reference:
  - <https://docs.pact.io/>

## Selection Rules

- Match framework to existing ecosystem and build toolchain.
- Avoid introducing cross-ecosystem tooling unless there is a clear migration plan.
- Standardize one primary framework per language in a repository to reduce cognitive load.
