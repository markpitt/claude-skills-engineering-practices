# Decision Frameworks: Choosing Between Simple and Complex

Simplicity is not always correct. Some problems are genuinely hard, and a
solution simpler than the problem will fail in production. These frameworks
exist to make the choice deliberate rather than reflexive in either direction.

## Table of Contents

- [Simple vs Complex Evaluation Framework](#simple-vs-complex-evaluation-framework)
- [When Complexity Is Justified](#when-complexity-is-justified)
- [Cost-Benefit Analysis for Features](#cost-benefit-analysis-for-features)
- [Decision Trees for Architectural Choices](#decision-trees-for-architectural-choices)
- [Measuring ROI of Simplification](#measuring-roi-of-simplification)
- [Decision Framework Template](#decision-framework-template)

---

## Simple vs Complex Evaluation Framework

Score each option against six dimensions. The lowest total wins unless a
dimension is a hard blocker, in which case that option is out regardless of
score.

| Dimension | Question | 1 (best) | 5 (worst) |
|---|---|---|---|
| **Comprehension** | How long before a new engineer can safely change it? | Under an hour | Over a week |
| **Failure surface** | How many ways can it break? | One obvious path | Many interacting paths |
| **Dependencies** | What must exist for this to run? | Standard library | Several services |
| **Reversibility** | What does undoing it cost? | Delete a function | Data migration |
| **Operational load** | What does it need at 3am? | Nothing | Dashboards and a runbook |
| **Fit** | How much of it is used? | All of it | A fraction |

### Worked comparison

Problem: deduplicate records in a nightly batch of roughly 50,000 rows.

```python
# Option A: sort and compare adjacent
def dedupe(records):
    seen = set()
    out = []
    for r in sorted(records, key=lambda r: r.id):
        if r.id not in seen:
            seen.add(r.id)
            out.append(r)
    return out

# Option B: Redis-backed distributed dedupe with TTL windows,
# consumer groups, and an idempotency ledger
```

| Dimension | A | B |
|---|---|---|
| Comprehension | 1 | 4 |
| Failure surface | 1 | 4 |
| Dependencies | 1 | 5 |
| Reversibility | 1 | 5 |
| Operational load | 1 | 4 |
| Fit | 1 | 2 |
| **Total** | **6** | **24** |

B is the right answer at 50 million rows across concurrent workers. At 50,000
rows in a single nightly job, it is a service dependency, an eviction policy and
a pager rotation bought to solve a problem a `set()` already solved.

**The framework's real job is forcing you to state the scale.** Most
simple-versus-complex arguments are actually unstated disagreements about
expected load.

---

## When Complexity Is Justified

Complexity is justified when it is **essential** rather than **accidental**:
when it reflects something genuinely hard about the problem rather than
something incidental about your solution. See `complexity-analysis.md` for the
full distinction.

### The five legitimate reasons

1. **The domain is irreducibly complex.** Tax rules, medical coding and
   airline fares are complicated because the world is. Simplifying the model
   below the domain's complexity produces wrong answers, not simple ones.

2. **Measured performance requires it.** You profiled, found the bottleneck,
   and the simple version misses a hard requirement. Note the order: measure,
   then complicate. Never the reverse.

3. **A regulatory or safety obligation demands it.** Audit trails, retention
   rules and access controls are not features you can simplify away because
   they are inconvenient.

4. **Scale has actually arrived.** Not "will arrive", not "the roadmap says".
   Present-tense load you can point at in a graph.

5. **The simple version has failed in production.** The most persuasive
   evidence there is, provided you diagnosed the failure rather than guessed.

### Test before accepting

Ask each of these. Any "no" means the complexity is not yet justified:

- Can you state the requirement the simple version fails to meet?
- Can you measure whether the complex version meets it?
- Would you notice within a week if it stopped being needed?
- Can you name the person who will maintain it in a year?

### Anti-justifications

These sound like reasons and are not:

- *"It's more flexible"*: flexibility unused is complexity paid for and not consumed
- *"We might need it later"*: build it later, when "might" becomes "do"
- *"It's the industry standard"*: for whose industry, at what scale?
- *"It's more elegant"*: elegance is a property of fit, not of machinery
- *"It's how the framework wants it"*: frameworks optimise for their median user

---

## Cost-Benefit Analysis for Features

Every feature carries a cost that continues after the ticket closes.

### The full cost

| Cost | Paid | Frequency |
|---|---|---|
| Build | Once | Once |
| Test | Once, then forever | Every CI run |
| Document | Once, then on every change | Ongoing |
| Support | Every user question | Ongoing |
| Maintain | Every dependency bump, every refactor | Ongoing |
| Constrain | Every future design that must accommodate it | Ongoing |
| Remove | Once, if ever | Rarely paid, often deferred |

Build cost is the smallest and the only one estimated up front. That asymmetry
is why feature sets grow past the point of usefulness.

### The evaluation

```python
# Score 1-5 for each. Build if benefit clearly exceeds cost,
# not if they are close: ties should fail, because the costs
# below are systematically underestimated and the benefit is
# systematically overestimated.

benefit = (
    users_affected        # how many people, honestly
    + frequency_of_use    # daily beats annually
    + severity_if_absent  # blocked work beats mild annoyance
)

cost = (
    build_effort
    + ongoing_maintenance * 3   # weight the recurring costs
    + constraint_on_future * 2  # what this forecloses
)
```

### Questions that settle most cases

- **Who asked?** One loud user is not a user base.
- **What do they do today?** If there is a workaround, the feature competes with it.
- **What happens if we say no?** Often less than expected.
- **What would we remove to make room?** If nothing, the system only grows.
- **How will we know it worked?** No measure means no way to remove it later.

---

## Decision Trees for Architectural Choices

### Should this be a separate service?

```text
Does it need independent scaling or deployment?
├─ No  → Keep it a module. A function call is cheaper than a network hop.
└─ Yes → Does it have a genuinely separate data model?
         ├─ No  → Keep it a module. Shared data across services is the
         │        expensive mistake, and it is hard to undo.
         └─ Yes → Is there a team that will own it?
                  ├─ No  → Keep it a module. Unowned services rot.
                  └─ Yes → Separate service is justified.
```

### Should this be abstracted?

```text
How many concrete cases exist today?
├─ 1  → No abstraction. You cannot see the shape from one example.
├─ 2  → Duplicate it. Two cases rarely reveal the right seam.
└─ 3+ → Do they vary along one axis or several?
         ├─ One    → Abstract along that axis.
         └─ Several→ Abstract the stable axis only, leave the rest concrete.
```

### Should this be configurable?

```text
Will different deployments genuinely need different values?
├─ No  → Hardcode it. A constant is readable; a config key is a search.
└─ Yes → Can a wrong value break production?
         ├─ No  → Config with a sensible default.
         └─ Yes → Config with validation, and fail closed at startup.
```

### Should we adopt this dependency?

```text
Can you write the part you need in under a day?
├─ Yes → Write it. You own the bugs either way; this way you can fix them.
└─ No  → Is it actively maintained and widely used?
         ├─ No  → Do not adopt. An abandoned dependency is your code
         │        with none of the understanding.
         └─ Yes → Does it pull a large transitive tree?
                  ├─ Yes → Weigh the tree, not the package.
                  └─ No  → Adopt.
```

---

## Measuring ROI of Simplification

Simplification competes with feature work for time. It needs a number.

### What to measure

| Metric | Before | After | How to capture |
|---|---|---|---|
| Time to first safe change | | | Onboard someone and time it |
| Lines in the affected path | | | `git diff --stat` |
| Cyclomatic complexity | | | See `complexity-analysis.md` |
| Build or test duration | | | CI timings |
| Defects per quarter in this area | | | Issue tracker, filtered by label |
| Deploys blocked or rolled back | | | Deploy log |

### The calculation

```python
# Payback period in weeks. Under ~12 weeks is usually worth doing;
# over ~26 weeks needs a reason beyond tidiness.

hours_saved_per_week = (
    fewer_defects * hours_per_defect
    + faster_changes * changes_per_week
    + less_onboarding_time / weeks_between_new_joiners
)

payback_weeks = refactor_hours / hours_saved_per_week
```

### Honesty rules

- **Measure before you start.** A before-figure reconstructed afterwards is a
  guess shaped by the outcome you want.
- **Count the risk.** Refactoring working code can introduce defects. If the
  area has no test coverage, that cost is real and belongs in the sum.
- **Do not claim savings you cannot point at.** "Improved maintainability" with
  no metric behind it is a preference, not a return.
- **A negative result is a result.** Concluding that a messy area is not worth
  simplifying is a legitimate and useful answer.

---

## Decision Framework Template

Copy this for any non-trivial simple-versus-complex decision.

```markdown
## Decision: [what is being decided]

**Date:** DD/MM/YYYY
**Decider:** [name]
**Status:** proposed | accepted | superseded by [link]

### Problem
[What needs solving. Include the scale: volumes, users, frequency.
Most disagreements here are really disagreements about scale.]

### Options

**Option A: [simplest thing that could work]**
- How it works:
- Cost to build:
- Cost to run:
- What it cannot do:

**Option B: [the more complex alternative]**
- How it works:
- Cost to build:
- Cost to run:
- What it buys over A:

### Evaluation

| Dimension | A | B |
|---|---|---|
| Comprehension | | |
| Failure surface | | |
| Dependencies | | |
| Reversibility | | |
| Operational load | | |
| Fit | | |
| **Total** | | |

### Decision
[Which, and why. If choosing the more complex option, name which of the
five legitimate reasons applies and cite the evidence.]

### Revisit when
[The condition that would change this answer. A decision with no
revisit trigger becomes permanent by default.]
```

---

## See Also

- `complexity-analysis.md`: essential versus accidental complexity, and metrics
- `simplification-strategies.md`: how to act on a decision to simplify

## Next Steps

1. **State the scale.** Volumes, users, frequency. Do this before comparing options.
2. **Write both options down.** The simple one first, in enough detail to be fair to it.
3. **Score them** against the six dimensions above.
4. **Check justification.** Choosing complexity? Name which of the five reasons applies.
5. **Record the revisit condition.** What would make you change your mind?
6. **Measure afterwards** if you simplified, so the next decision has evidence.
