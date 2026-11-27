# Web-Verified Baseline (2026-02)

This file captures the primary claims used by `tdd-guru` and the source used to
verify each claim.

## Core TDD

- **Red-Green-Refactor is canonical TDD**
  - Source: Martin Fowler `TestDrivenDevelopment` (updated 2023-12-11)
  - URL: <https://martinfowler.com/bliki/TestDrivenDevelopment.html>

- **Initial test-case list + iterative selection improves flow**
  - Source: Martin Fowler `TestDrivenDevelopment`
  - URL: <https://martinfowler.com/bliki/TestDrivenDevelopment.html>

## Test Portfolio Shape

- **Prefer many lower-level tests and fewer broad high-level tests**
  - Source: Fowler `TestPyramid`; Fowler `Practical Test Pyramid`; Google SWE Book ch11/ch12
  - URLs:
    - <https://martinfowler.com/bliki/TestPyramid.html>
    - <https://martinfowler.com/articles/practical-test-pyramid.html>
    - <https://abseil.io/resources/swe-book/html/ch11.html>
    - <https://abseil.io/resources/swe-book/html/ch12.html>

- **Avoid E2E-heavy “ice cream cone” portfolios**
  - Source: Fowler `TestPyramid`; Google SWE Book ch11
  - URLs:
    - <https://martinfowler.com/bliki/TestPyramid.html>
    - <https://abseil.io/resources/swe-book/html/ch11.html>

## Determinism and Flake Control

- **Nondeterministic tests erode trust and should be quarantined + fixed**
  - Source: Fowler `Eradicating Non-Determinism in Tests`
  - URL: <https://martinfowler.com/articles/nonDeterminism.html>

- **Avoid bare sleeps; use callbacks/polling with explicit timeout strategy**
  - Source: Fowler `Eradicating Non-Determinism in Tests`
  - URL: <https://martinfowler.com/articles/nonDeterminism.html>

- **Isolated tests and controlled dependencies improve reproducibility**
  - Source: Playwright best practices; Google SWE Book ch11/ch12
  - URLs:
    - <https://playwright.dev/docs/best-practices>
    - <https://abseil.io/resources/swe-book/html/ch11.html>
    - <https://abseil.io/resources/swe-book/html/ch12.html>

## Behaviour vs Implementation Coupling

- **Prefer behavior/state verification over brittle interaction coupling by default**
  - Sources: Fowler `Mocks Aren't Stubs`; Google SWE Book ch12
  - URLs:
    - <https://martinfowler.com/articles/mocksArentStubs.html>
    - <https://abseil.io/resources/swe-book/html/ch12.html>

- **Test via public API/contracts rather than private internals**
  - Source: Google SWE Book ch12; .NET unit testing best practices
  - URLs:
    - <https://abseil.io/resources/swe-book/html/ch12.html>
    - <https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices>

## Quality Signals Beyond Coverage

- **Coverage is useful but insufficient for test effectiveness**
  - Sources: .NET unit testing best practices; PIT; Stryker
  - URLs:
    - <https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices>
    - <https://pitest.org/>
    - <https://stryker-mutator.io/docs/>

- **Mutation testing evaluates whether tests actually detect defects**
  - Sources: PIT; Stryker
  - URLs:
    - <https://pitest.org/>
    - <https://stryker-mutator.io/docs/>

- **Property-based testing improves invariant/edge-case confidence**
  - Source: Hypothesis docs
  - URL: <https://hypothesis.readthedocs.io/en/latest/>

## Contract Testing

- **Consumer/provider contracts validate integration compatibility in isolation**
  - Sources: Pact docs; Fowler contract-testing concepts in testing writings
  - URLs:
    - <https://docs.pact.io/>
    - <https://martinfowler.com/articles/practical-test-pyramid.html>

## Framework/Tooling Recency Checks

- **JUnit current docs now centred at docs.junit.org (current user guide)**
  - Source: JUnit docs redirect/current page
  - URL: <https://docs.junit.org/current/user-guide/>

- **Vitest guidance and requirements should come from current guide**
  - Source: Vitest guide
  - URL: <https://vitest.dev/guide/>

- **Pytest modern integration guidance includes strict-mode capabilities**
  - Source: Pytest good practices
  - URL: <https://docs.pytest.org/en/stable/explanation/goodpractices.html>

## Test Desiderata Lens

- Useful quality dimensions for trade-off conversations:
  deterministic, isolated, fast, specific, predictive, readable, writable,
  structure-insensitive.
  - Source: Test Desiderata
  - URL: <https://testdesiderata.com/>
