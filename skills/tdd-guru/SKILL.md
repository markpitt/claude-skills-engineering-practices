---
name: tdd-guru
description: Enforces current Test-Driven Development (TDD) best practices across programming languages and scripts, including red-green-refactor workflow, deterministic test design, test pyramid balance, and quality signals beyond coverage. Use when users ask about TDD workflow, test strategy, flaky test remediation, unit/integration/end-to-end balance, contract testing, or improving test effectiveness in CI. Do NOT use for unrelated non-testing prompts or language-only questions without testing intent.
license: Apache-2.0
metadata:
  version: "1.0.0"
---

# TDD Guru

Enforce modern, evidence-grounded TDD practices for software and scripting work.
Prioritise fast feedback, deterministic tests, behaviour-focused design, and
clear delivery gates.

## Critical

- Ground “latest” or “best practice” claims with a web fetch tool (WebFetch in Claude Code) before finalising guidance.
- Prefer official docs and primary references over secondary blog summaries.
- If web evidence conflicts with existing memory, prefer current official docs and state that guidance changed.
- Enforce the canonical TDD loop: **red → green → refactor**.
- Keep recommendations behaviour-first: test externally observable behaviour and contracts, not private implementation details.
- Treat flaky tests as a priority defect in the delivery system, not as acceptable noise.
- For unrelated prompts, answer directly without injecting TDD process guidance.

### Web Verification Order

When recency or best-practice claims matter, verify in this order:

1. `https://martinfowler.com/bliki/TestDrivenDevelopment.html`
2. `https://martinfowler.com/bliki/TestPyramid.html`
3. `https://abseil.io/resources/swe-book/html/ch11.html`
4. `https://abseil.io/resources/swe-book/html/ch12.html`
5. `https://martinfowler.com/articles/nonDeterminism.html`
6. `https://testdesiderata.com/`
7. `https://docs.pytest.org/en/stable/explanation/goodpractices.html`
8. `https://docs.junit.org/current/user-guide/`
9. `https://learn.microsoft.com/en-us/dotnet/core/testing/unit-testing-best-practices`
10. `https://vitest.dev/guide/`
11. `https://playwright.dev/docs/best-practices`
12. `https://docs.pact.io/`
13. `https://stryker-mutator.io/docs/`
14. `https://pitest.org/`

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

## Enforcement Checklist

Apply this checklist to every TDD-related response.

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
- [ ] Avoid “ice cream cone” test portfolios (too many broad UI/E2E tests).
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

## Intent-Specific Hard Requirements

### Flaky Test Prompt

For flaky-test prompts, include all of the following in guidance:

- isolation/order-independence
- time/randomness control
- no bare sleep-based synchronisation
- external dependency containment (fakes/stubs/contracts)

### “How many E2E tests?” Prompt

Must state that high-level E2E tests should be comparatively few and focused on
critical user journeys, with most coverage at lower levels.

### “Beyond Coverage” Prompt

Must mention at least one mutation-testing approach and at least one
property-based/invariant-testing approach.

### Service Integration Prompt

Must mention consumer/provider contract validation and explain why it reduces
brittle, environment-heavy integration-only strategies.

## Response Pattern

1. Identify user goal: workflow, quality, reliability, or portfolio balance.
2. If recency-sensitive, verify claims with the web fetch tool first.
3. Give concise, actionable guidance in ordered steps/checklist form.
4. Include trade-offs (speed vs fidelity, isolation vs realism) explicitly.
5. End with a short “next iteration” loop the user can run immediately.

## Anti-Patterns (Instant Fail)

- Recommending test-after coding while calling it TDD.
- Treating flaky tests as acceptable and advising retries-only as a permanent fix.
- Recommending end-to-end-heavy portfolios as default strategy.
- Using coverage percentage alone as proof of test quality.
- Strongly coupling tests to implementation details when behaviour-level checks suffice.

## References

- `references/web-verified-baseline.md`, source-grounded facts and verification map
- `references/tdd-workflow-playbook.md`, practical red-green-refactor playbook
- `references/language-and-tooling-map.md`, modern polyglot testing stack guidance
