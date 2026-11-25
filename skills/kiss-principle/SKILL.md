---
name: kiss-principle
description: Apply the KISS principle to reduce unnecessary complexity. Use ONLY when the user explicitly mentions "KISS", "simplify", "over-engineered", "too complex", or asks to review code/design specifically for unnecessary complexity. Do NOT trigger for general coding tasks, feature work, or architecture discussions unless simplicity is the named concern.
license: Apache-2.0
metadata:
  version: "2.0"
  tags: "simplicity, architecture, refactoring, maintainability"
---

# K.I.S.S Principle

**Goal:** Solve the problem with the minimum necessary complexity while preserving correctness and user value.

Everything needed for a routine simplification review is below. Load a resource
only when the situation in the left column actually applies.

| Situation | Load |
|---|---|
| Deciding whether complexity is justified, or arguing simple against complex | `resources/decision-frameworks.md` |
| Need a named technique and a worked before/after for a specific smell | `resources/simplification-strategies.md` |
| Need to measure complexity or separate essential from accidental | `resources/complexity-analysis.md` |

## Complexity red flags

- Can't explain it in 2–3 sentences
- Deeply nested conditionals (>3 levels)
- Multiple abstractions stacked on each other
- Many interdependencies and side effects
- Over-engineered for current and foreseeable needs

## Green flags (appropriate simplicity)

- Does one thing well (single responsibility)
- New developers understand it quickly
- Dependencies are explicit and minimal
- Readable and self-documenting

## Core techniques

| Situation | Technique |
|---|---|
| Too many layers | Flatten architecture, remove intermediate abstractions |
| Speculative code | Delete features not serving a current need |
| Clever code | Rewrite for clarity; clear beats clever |
| Over-generalisation | Solve the specific problem first |
| Complex conditionals | Extract named predicates or simplify to table/data |
| Premature optimisation | Measure first, then optimise the proven bottleneck |

## Quick check (5 questions)

1. Can you explain this in 2 sentences?
2. What is the simplest version that still works?
3. What complexity is essential vs optional?
4. Can you remove one component without breaking anything?
5. What would a new developer struggle with first?

## Simplifying is not always right

Complexity is sometimes the correct answer, and a KISS review that only ever
removes things is a bad review. Before recommending a removal, check that the
complexity is not carrying a real requirement: scale, a regulatory or security
constraint, a genuine domain irregularity, or a failure mode already paid for
once. `resources/decision-frameworks.md` covers how to judge that and how to
record the condition that would change the answer.

Apply findings incrementally. Test after each removal. Document why complexity was removed, not just that it was.
