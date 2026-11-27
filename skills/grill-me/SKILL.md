---
name: grill-me
description: Interview the user one decision at a time down a dependency-ordered decision tree, asking exactly one question per turn with a recommended option, exploring the codebase before asking, and finish with a decision record. Use when the user says "grill me", asks to be interviewed or stress-tested on a plan, design or task, or has a set of open decisions to settle before work starts. Do NOT use for a single question, for open-ended brainstorming, or when the user has already decided and wants the work done.
license: Apache-2.0
metadata:
  version: "1.1.0"
  tags: "decisions, interview, planning, design, requirements"
---

# Grill me

Settle a task's open decisions by interviewing the user, one decision at a time, in dependency order, with a recommended answer for each. Answer from the codebase wherever it can be answered there, and ask only what is genuinely the user's to decide. The output is a decision record, not a plan or a design document.

The interview pattern follows the `grill-me` skill popularised by Matt Pocock (github.com/mattpocock/skills). This skill is written from the method, not copied: it asks one question per turn where the original asks a round of questions, and adds a context-discovery step before the first question.

## The one-question rule

**Every turn asks exactly one question, then stops and waits for the answer.** This holds from the first turn to the last, including the opening turn when you know almost nothing yet.

- No second question, no follow-up "and also", no "while you're at it".
- No numbered or bulleted list of questions. A list of answer options for the one question is fine; a list of things to tell you is not.
- No compound question. "What is it, who is it for, and when does it ship?" is three questions. Pick the one the others depend on.
- No open request for a brain dump ("describe the task, mention any constraints, paste the plan"). Turn it into the single decision that matters most first, with options.
- The question offers a recommended option first, marked `(Recommended)`, and two or three real alternatives.
- Do not ask the next question until the user has answered this one. Their answer usually changes what the next question should be.

Bad (three questions in one turn, no options):

```text
What is the billing service meant to do? What have you already decided,
and are there any constraints such as the payment provider or data store?
```

Good (one decision, options, recommendation first):

```text
I'll take the decisions one at a time and finish with a decision record.
First, the one the rest depend on.

Decision 1: Who owns the source of truth for invoices?
- The billing service owns it (Recommended): other services read through its API,
  so there is one place to reconcile.
- The existing orders database stays the owner and billing derives from it.
- A shared ledger that both write to.
```

## When to use

Use it when the user names a task, plan or feature and wants its decisions forced out before work starts, or when they hand over a list of decisions to resolve.

Do not use it for a single, self-contained question (ask it), for brainstorming where no decisions exist yet, or when the user has said what they want and only needs it built.

## Process

### 1. Discover context first

Before any question, spend a small, fixed budget finding out where you are. Three shell calls is enough:

1. Working directory, repository root and current branch.
2. Whether the working directory is itself a repository root, what project configuration directory exists, and whether it is a container of nested repositories.
3. Working-tree state and which toolchain files are present (`package.json`, `pyproject.toml`, `*.csproj`, `Cargo.toml`, `go.mod`, `mise.toml`).

Then stop discovering. The budget exists so discovery cannot grow into the interview.

State what was found, and what was not, in one short paragraph. When a signal is absent, say so and do not guess:

- No repository: do not ask repository-shaped questions about branches or commits.
- No project configuration: do not assume conventions that exist only elsewhere.
- No toolchain file: presence was checked, not use. Do not assert a stack from silence; if the task needs one, make it a question.
- No tools at all: discovery still happens, over what you can see. Read the conversation and anything pasted into it, and state in one line what it settles and what it leaves open (for example "From your message: a task exists, but not what it is or which system it touches. No codebase is visible to me, so facts will come from your answers."). Then carry on to the first decision.

### 2. Build the tree, show only the first decision

List every open decision the task actually has, given what discovery already settled. Order it so that no decision sits above one whose answer would change its options or make it moot.

Keep the tree as your working notes. Do not print it as a numbered list to the user: a list of decisions reads as a list of questions. Instead, in the first turn, show the order in one sentence: roughly how many decisions there are, which one comes first, and which later ones depend on it, named as topics rather than questions (for example "About four decisions; scope comes first because the cutover, data migration and rollback choices all depend on it."). That sentence is required even when the task is thin.

When the task is thin (a plan named but not shown, or no task at all), do not ask for a description. The root decision of that kind of task is still knowable: scope, goal, what is being replaced, who the users are. Ask that one as a decision with options. If there is genuinely no task, ask which kind of task it is, with options and a recommended default, as the one question for this turn.

Walk the tree top to bottom. When an answer removes or reshapes a later item, rewrite or cross it off before asking it, and say in one line what changed and why. Never ask a question the tree has already made moot.

### 3. Explore before asking

For each item, check whether the codebase, configuration, git history or discovery output settles it. If so, settle it silently and report the answer as a fact when you reach that item. Ask only what exploration leaves undetermined: a real preference, a trade-off with no objectively correct side, or something no file can answer.

### 4. Ask one decision, then stop

Apply the one-question rule above. Use the host's structured question tool where there is one (`AskUserQuestion` in Claude Code), with a single question per call. Otherwise ask in prose with the same shape.

- **One decision per turn, then wait.** Never batch, even when two decisions look independent: ask the first, take the answer, then ask the second.
- **Say once, in the first turn, that a decision record follows** when the interview ends.
- **Recommended option first, marked `(Recommended)`.** Put the reasoning in that option's description. The question text says what is being decided and does not argue for a side.
- **Two or three real alternatives after it.** Do not pad to four, and do not add an "Other" option when the tool adds one.
- **Ask names, numbers and paths in prose,** still as one question. A false multiple choice over an open value is worse than a plain question. State your recommended value first so the user is confirming rather than composing.
- **Use multi-select** where the options are not mutually exclusive, and say so in the question.
- **Say when an answer overturns an earlier assumption**, then re-walk from there instead of carrying a contradiction forward.

Before sending a turn, check it: count the questions. If there is more than one, delete all but the one the others depend on. On the first turn, also check that it states what discovery found, the order sentence and that a decision record follows.

### 5. Stop and record

Stop when every item has been settled by exploration or by an answer and the last answer opened no new branch. Do not keep looking for more to ask: the cap is the tree, not a turn count.

Write a decision record, as a flat list in the order resolved:

```markdown
## <n>. <decision>
Settled by: exploration | user
Answer: <the answer>
Why: <one line: the exploration finding, or the user's stated reason>
```

Save it as `decisions/<YYYY-MM-DD>_<slug>.md` if the repository has a `decisions/` directory or convention. Otherwise propose a location and confirm it once. Give the absolute path when done. Commit it only if the user has asked you to commit; an uncommitted record is the shape that gets lost, so say plainly that it is uncommitted.

## Examples

Short invocations that should trigger this skill:

- "Grill me on the migration to the new auth provider."
- "Interview me about this feature before you start."
- "Stress-test this plan with questions, one at a time."

A good opening, after discovery:

```text
Found: a single repository on branch `feature/export`, clean tree, a `pyproject.toml`
and no container of nested repos. No project configuration directory.

I count three open decisions and will take them one at a time, then write a
decision record. Format comes first, because streaming and storage depend on it.

Decision 1: Which export format?
- CSV (Recommended): every consumer in the repo already reads it.
- JSON Lines: keeps nested fields, at the cost of a new reader.
- Parquet: smallest files, but adds a dependency.
```

A good opening when the task has not been described:

```text
From your message: there is a task, but not what it is or which system it
touches. No codebase is visible to me, so facts will come from your answers.
I'll take the decisions one at a time and finish with a decision record.
The kind of change comes first, because scope, approach and how we prove
it worked all depend on it.

Decision 1: What kind of change is this?
- A new feature in an existing system (Recommended): the most common case,
  and it starts from scope.
- A migration or replacement of something that exists.
- A rewrite or restructure with no change in behaviour.
```

## Gotchas

- A recommendation is not a decision. Do not mark an item settled until the user has answered or exploration has shown the answer.
- Do not write a plan or design here. The record is the hand-off; planning is the next step.
- Do not re-ask what the record already holds in a later session. Read it first.
- "Tell me about X" is not a question with an answer you can record. Ask the decision instead.
