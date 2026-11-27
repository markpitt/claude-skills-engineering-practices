---
name: root-cause-analysis
description: "Structured root cause analysis using named methods: Toyota's 5 Whys, Ishikawa fishbone, Pareto analysis, fault tree and barrier analysis. Use when asked for \"the root cause\", \"why does this keep happening\", \"5 whys\", \"a fishbone\", \"a post-mortem\" or \"an incident analysis\"; when a problem has come back after being fixed once already; or when a symptom has several candidate causes that need separating. Applies to software, hardware, process failures and practical problems such as missed deadlines or a habit that will not stick. Do NOT use for ordinary debugging where the cause can be found by reading the code or the logs, since a 5 Whys on a typo wastes everyone's time. Do NOT use for emotional, mental health or wellbeing support."
license: Apache-2.0
metadata:
  version: "3.0.0"
  source: Toyota Production System, plus standard quality-engineering methods
  author: Taiichi Ohno (5 Whys), Kaoru Ishikawa (fishbone diagrams)
  attribution: Methods restated with original worked examples.
---

# Root cause analysis

The point of root cause analysis is to stop the problem coming back. Fixing the symptom makes today better; finding the cause makes next month better. Taiichi Ohno, who built the Toyota Production System, put the method in one line in *Toyota Production System: Beyond Large-Scale Production* (1988): "By repeating why five times, the nature of the problem as well as its solution becomes clear."

Five is a habit, not a rule. Stop when you reach a cause you can change and that, once changed, would have prevented the problem. Stop earlier if the chain has reached something outside anyone's control, such as the weather, and work back to the last cause you can act on.

## Choosing a method

| Situation | Method | Load |
|---|---|---|
| One clear failure, one likely chain | 5 Whys | `resources/rca-methodologies.md` |
| Several plausible causes, scope unclear | Fishbone, then 5 Whys on the strongest branch | `resources/rca-methodologies.md` |
| Many problems competing for attention | Pareto first, then 5 Whys on the top one or two | `resources/rca-methodologies.md` |
| Safety-critical or high-stakes | Fault tree | `resources/advanced-techniques.md` |
| A control or check that should have caught it did not | Barrier analysis | `resources/advanced-techniques.md` |
| You suspect a familiar cause | Check the catalogue, then confirm with 5 Whys | `resources/common-root-causes.md` |

`resources/example-analyses.md` has four worked examples: a JWT authentication bug, an overheating car, an e-commerce checkout failure, and a run of missed deadlines.

## Define the problem first

Most bad analyses start with a vague problem statement. Before asking why, write down:

- **What** is observed, as a symptom, not a guessed cause.
- **Where** it happens: which system, component or place.
- **When**: when it started, how often, and any pattern.
- **Impact**: who or what is affected, and how badly.

For example: "Users in the EU region see 3 to 5 second dashboard load times between 09:00 and 11:00 UTC, affecting about 2,000 daily users. It started after the v2.4 deployment on 18 November."

## Evidence before answers

Go and see. Each "why" should be answered from evidence: logs, metrics, the timeline of changes, reproduction steps, the state of the system before and after. An answer that is a plausible story rather than an observed fact is a hypothesis, and should be labelled as one until something confirms it.

The most useful single question is usually "what changed?" Deployments, configuration, dependencies, load, people and season all count.

## Checking the cause

A root cause should pass two tests:

- **Forward:** if this cause is present, does it produce the symptom? Can you reproduce it?
- **Backward:** if you remove this cause, does the symptom stop, and would it have stopped this time?

Then check it is something you can act on. "Human error" is almost never the root cause: ask why the system allowed the error, or why nothing caught it. `resources/common-root-causes.md` lists other red flags, and the verification section of `resources/advanced-techniques.md` covers testing a cause more formally.

## A quick pass

For a small problem where a full analysis would be overkill:

1. Write the problem statement.
2. Ask why, and answer from evidence. Repeat until the answer is something you can change. That is often three or four rounds, sometimes five or more.
3. Run the forward and backward tests on that answer.
4. Fix it, and say what would show the fix worked.

## What to hand back

- The problem statement.
- The chain of reasoning, with the evidence for each step and anything still unconfirmed marked as such.
- The root cause, and how it passed the forward and backward tests.
- Actions that address the cause, with an owner where there is one, and any containment needed while the real fix lands.
- What would show the fix worked, and when to check.

For incidents with several people involved, keep the write-up blameless: describe what the system allowed, not who slipped. `resources/advanced-techniques.md` has the post-mortem structure.
