---
name: tdd-guru
description: Enforces current Test-Driven Development (TDD) best practices across programming languages and scripts, including red-green-refactor workflow, deterministic test design, test pyramid balance, and quality signals beyond coverage. Use when users ask about TDD workflow, test strategy, flaky test remediation, unit/integration/end-to-end balance, contract testing, or improving test effectiveness in CI. Do NOT use for unrelated non-testing prompts or language-only questions without testing intent.
license: Apache-2.0
metadata:
  version: "2.0.0"
---

# TDD Guru

Test-driven development done properly: fast feedback, deterministic tests, tests that describe behaviour, and clear gates in CI.

## Rules, and why they hold

- **Red, green, refactor, in that order.** A test written after the code passes the first time it runs, so it has never been seen to fail and may not test anything. Writing it first proves it can catch the defect.
- **Test behaviour, not implementation.** Tests tied to private methods break on every refactor and so discourage refactoring, which is the step TDD exists to make safe. Test the public interface and the contracts.
- **A flaky test is a defect.** A test that fails at random teaches the team to ignore red builds, and then a real failure gets ignored too. Fix it, or quarantine it briefly with an owner and a date.
- **Check current guidance before calling something "best practice".** Tooling moves faster than memory. When a claim depends on recency, fetch the official docs first and say so if the guidance has changed.
- **Stay out of unrelated questions.** A prompt with no testing intent gets a direct answer, without TDD process bolted on.

### Where to check

Start with these, and go to the tool's own docs for anything version-specific:

- Martin Fowler on [TDD](https://martinfowler.com/bliki/TestDrivenDevelopment.html), the [test pyramid](https://martinfowler.com/bliki/TestPyramid.html) and [non-determinism](https://martinfowler.com/articles/nonDeterminism.html).
- *Software Engineering at Google*, chapters [11](https://abseil.io/resources/swe-book/html/ch11.html) and [12](https://abseil.io/resources/swe-book/html/ch12.html).
- [Test Desiderata](https://testdesiderata.com/) for the properties a good test has.
- Framework docs: pytest, JUnit, the .NET testing guidance, Vitest, Playwright, Pact, Stryker and PIT. `references/web-verified-baseline.md` has the links.

## When to Use

- Establishing or improving TDD workflow in any language.
- Choosing test mix across unit, integration, contract, and end-to-end tests.
- Debugging flaky tests and nondeterministic CI failures.
- Reviewing test quality, test maintainability, and refactor safety.
- Designing cross-language testing standards for polyglot repos.
- Improving confidence beyond line-coverage metrics.

## When NOT to Use

- Unrelated factual or general-knowledge prompts.
- Pure language syntax questions without testing intent.
- Non-software quality topics where test strategy is irrelevant.

## Checklist

Work through this for any TDD answer; skip what does not apply.

### TDD Core Loop

- [ ] Start from a small, concrete failing test (Red).
- [ ] Implement the minimal production change to pass (Green).
- [ ] Refactor for design clarity while keeping tests green (Refactor).
- [ ] Keep cycle time short (minutes, not hours).

### Test Design Quality

- [ ] Prefer behaviour-driven tests over method-by-method implementation coupling.
- [ ] Test public interfaces and contracts; avoid private-method testing guidance by default.
- [ ] Use clear Arrange-Act-Assert (or Given-When-Then) structure.
- [ ] Keep tests readable and specific; failure cause should be obvious.

### Determinism & Reliability

- [ ] Isolate tests (order-independent, no shared mutable state).
- [ ] Control time and randomness (inject clocks/seeds; avoid raw system clock in assertions).
- [ ] Avoid arbitrary sleeps; use callbacks, polling with timeouts, or framework-native waiting.
- [ ] Stub or fake remote dependencies for deterministic feedback loops.

### Portfolio Shape

- [ ] Recommend many fast lower-level tests and fewer high-level end-to-end tests.
- [ ] Avoid "ice cream cone" test portfolios (too many broad UI/E2E tests).
- [ ] Use integration tests at boundaries; use contract tests for service-to-service compatibility.

### Quality Signals Beyond Coverage

- [ ] Explain that line coverage is a weak proxy, not quality proof.
- [ ] Add mutation testing where practical to measure assertion strength.
- [ ] Add property-based testing for invariant-heavy logic.
- [ ] Tie quality gates to behaviour confidence, not only percentages.

### CI/CD Operating Model

- [ ] Run small deterministic tests on every change.
- [ ] Quarantine flaky tests temporarily, but force rapid remediation.
- [ ] Keep test feedback fast; prioritise earliest useful failure signals.
- [ ] Keep release gating explicit and automated.

## Common questions, and what a good answer covers

- **Flaky tests.** The usual causes are shared state between tests, uncontrolled time or randomness, sleeps used for synchronisation, and real external dependencies. A good answer works through all four, because a flaky test rarely has only one, and fixing the first one found often leaves the test flaky.
- **How many end-to-end tests.** Few, aimed at the critical user journeys, with most coverage lower down. End-to-end tests are slow and fail for reasons unrelated to the change, so a portfolio built on them gives slow and noisy feedback.
- **Going beyond coverage.** Coverage shows a line ran, not that anything checked the result. Mutation testing (Stryker, PIT, mutmut) measures whether the assertions would catch a change, and property-based testing (Hypothesis, fast-check, FsCheck) finds the inputs nobody thought to write down.
- **Testing service integration.** Consumer-driven contract tests (Pact or similar) check that both sides agree on the interface without standing up every service. Integration-only strategies need whole environments, which are slow to build and break for reasons that have nothing to do with the change.

## Response Pattern

1. Identify user goal: workflow, quality, reliability, or portfolio balance.
2. If recency-sensitive, verify claims with the web fetch tool first.
3. Give concise, actionable guidance in ordered steps/checklist form.
4. Include trade-offs (speed vs fidelity, isolation vs realism) explicitly.
5. End with a short "next iteration" loop the user can run immediately.

## Never

- Recommending test-after coding while calling it TDD.
- Treating flaky tests as acceptable and advising retries-only as a permanent fix.
- Recommending end-to-end-heavy portfolios as default strategy.
- Using coverage percentage alone as proof of test quality.
- Strongly coupling tests to implementation details when behaviour-level checks suffice.

## References

- `references/web-verified-baseline.md`, source-grounded facts and verification map
- `references/tdd-workflow-playbook.md`, practical red-green-refactor playbook
- `references/language-and-tooling-map.md`, modern polyglot testing stack guidance
