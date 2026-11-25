# Remediation plan template and completeness check

Use this for `docs/remediation/<date>-<slug>-plan-rN.md`. The plan is executed by a model or a
person with no ability to exercise judgement, so every item must be complete on its own. The
test for an item: could a cheap model, given only this item and the files it names, make the change
and pass the acceptance check without asking a question?

## Template

````markdown
# Remediation plan: <topic>, round N

- Source audit: `docs/audits/<date>-<slug>.md` (audited commit <SHA>)
- Round: N. Prior round summary: `<path>` or none.
- Starts from: tag `pre-remediation-<date>` at <SHA>
- Branch: `remediation/<slug>-<date>`
- Standing rules: one atomic commit per item; stage explicit paths only; no out-of-plan changes;
  every row in the summary carries a SHA, the acceptance command, its exit status and output.

## Decision log

Decisions the items depend on. Settle every one before execution starts.

| ID | Question | Chosen option | Options considered | Settled by, date |
|---|---|---|---|---|
| D-1 | <question> | <option> | <a>, <b> | <name or role>, <date> |

## Order

1. <slug>-001 (no dependencies)
2. <slug>-003 (depends on <slug>-001)

## Items

### <slug>-001: <title, same as the audit heading>

- Severity (from audit): High
- Files: `path/one.ext`, `path/two.test.ext`
- Depends on: none
- Decisions: D-1 | none
- Change:
  1. In `path/one.ext`, find the text `<exact text>` and replace it with `<exact replacement>`.
  2. Add `<exact line>` immediately after the line `<exact anchor>`.
- Acceptance: `<exact command>` exits 0 and its output contains `<exact expected text>`.
- Commit: `fix(<scope>): <summary> (<slug>-001)`
- Not in this item: <anything a reader might be tempted to add>

## Deferred

Findings not fixed this round. Each needs a sign-off before execution.

| ID | Reason | Owner | Revisit when | Signed off by, date |
|---|---|---|---|---|
| <slug>-007 | <why> | <who> | <trigger> | <name>, <date> |
````

## Writing rules for items

- **The item ID is the finding ID.** Nothing else takes the form `<slug>-NNN`.
- **Exact files.** List every file the item touches. "The config files" is not a file. A reviewer
  compares this list with the diff, so an incomplete list produces a false out-of-plan finding.
- **Exact change.** Give the text to find and the text to replace it with, or the exact line and
  its anchor. Locate by content, never by line number alone, because earlier items move lines.
- **Exact acceptance.** A command, the expected exit status and the expected output. "Tests pass"
  is not an acceptance check. Name the test: `mise exec -- pytest tests/test_parse.py::test_null_input`
  exits 0.
- **No judgement words.** Remove "decide", "consider", "as appropriate", "if needed", "where
  sensible", "clean up". Each of these hands a decision to the executor. Either the decision is in
  the log, or the item says exactly what to do.
- **Test first where the defect is behavioural.** Give the failing test as the first change, its
  expected failure, and then the fix. For a destructive defect, do not reproduce it at runtime:
  assert on the shape of the call, using a mock or a path shim, and say so in the item.
- **Not-in-this-item.** Name the tempting neighbour change and forbid it. This is where scope
  creep is stopped before it starts.

## Completeness check

Run this before any execution. It must show that every audit finding is either in the plan or in
the deferral table, and that the plan names no finding the audit does not contain.

```bash
audit=docs/audits/<date>-<slug>.md
plan=docs/remediation/<date>-<slug>-plan-r1.md
grep -oE '^### [a-z0-9-]+-[0-9]{3}' "$audit" | sed 's/^### //' | sort -u > /tmp/audit-ids.txt
grep -oE '^### [a-z0-9-]+-[0-9]{3}' "$plan" | sed 's/^### //' | sort -u > /tmp/plan-ids.txt
grep -oE '^\| [a-z0-9-]+-[0-9]{3} \|' "$plan" | sed -E 's/^\| ([^ ]+) \|$/\1/' | sort -u > /tmp/deferred-ids.txt
wc -l /tmp/audit-ids.txt /tmp/plan-ids.txt /tmp/deferred-ids.txt
sort -u /tmp/plan-ids.txt /tmp/deferred-ids.txt > /tmp/covered-ids.txt
echo "missing from plan and deferrals:"; comm -23 /tmp/audit-ids.txt /tmp/covered-ids.txt
echo "named in plan but absent from audit:"; comm -13 /tmp/audit-ids.txt /tmp/covered-ids.txt
```

Read the output, do not just read the exit status. `comm` exits 0 whether or not it prints
anything, and `grep` exits 1 when it matches nothing, so a silent empty file looks like a pass. The
pass condition is:

1. Both `echo` headings are followed by no IDs.
2. The three `wc -l` counts are non-zero, and the audit count equals the number of finding headings
   in the audit. Count them separately with `grep -c '^### ' "$audit"`; a mismatch means the
   heading pattern missed some findings.

Record the counts in the plan header: audit findings N, planned items N, deferred N, with N + N =
N.

## Dependencies and order

- An item may depend only on items earlier in the order.
- Two items that touch the same file are ordered, never parallel, and the later one's "Change"
  quotes the text as it will be after the earlier item.
- An item that cannot be made to pass without another item's change is a dependency, not a reason
  to widen the item.

## Deferral rules

- A deferral is not a way to make the plan shorter. Give the real reason.
- "Out of scope" is not a reason unless the audit's own scope excluded the file.
- The revisit trigger is an observable event, such as "the parser is replaced" or "the next
  release", not "later".
- A deferred finding is still open in the ledger. It is closed only when fixed, or when its
  deferral is withdrawn and it is carried into a plan.
