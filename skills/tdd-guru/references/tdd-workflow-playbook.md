# TDD Workflow Playbook

Use this process when the user asks for practical TDD execution.

## 1) Define the Smallest Next Behavior

- Phrase the next increment as an observable behavior.
- Prefer wording that can be asserted from the public interface.
- Keep each cycle narrow enough to finish in minutes.

## 2) Red (Failing Test)

- Write one failing test for one behavior.
- Name the test after behavior + expected outcome.
- Keep setup explicit and minimal.
- Avoid adding production logic to test helpers.

## 3) Green (Minimal Implementation)

- Add the smallest production change required to pass.
- Avoid broad refactors in this stage.
- Keep feedback loop short by running focused tests first.

## 4) Refactor (Design Improvement)

- Improve naming, extraction, cohesion, and duplication.
- Keep behavior unchanged; tests remain green.
- If refactor breaks tests, evaluate whether tests are over-coupled to structure.

## 5) Expand Coverage Intentionally

- Add edge-case tests only when behavior or risk demands it.
- Push checks downward (unit/component) when possible.
- Keep high-level tests for critical journey confidence, not exhaustive permutations.

## 6) Reliability Gate Before Merge

- Verify deterministic execution locally and in CI.
- Remove/repair flaky tests before they accumulate.
- If temporary quarantine is used, assign explicit owner + deadline.

## 7) Quality Amplifiers

- Add property-based tests for invariants and broad input spaces.
- Add mutation testing on critical modules to assess assertion strength.
- Use contract tests for service boundaries where teams deploy independently.

## Lightweight Prompt Template

When asked for a TDD plan, respond in this structure:

1. Behavior statement
2. First failing test
3. Minimal production step
4. Refactor targets
5. Next test in sequence
