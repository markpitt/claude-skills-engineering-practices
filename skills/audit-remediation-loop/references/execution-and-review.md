# Execution summary and conformance review

Two artefacts: the implementer's summary, written after execution, and the reviewer's conformance
review, written after that. The reviewer checks the summary's claims against the commits; it does
not take them on trust.

## Execution rules

1. Take the items in the plan's order. Do not start item N+1 while item N is red.
2. Make only the change the item lists. Its "Not in this item" line is binding.
3. Run the acceptance check and read its output. A check that printed nothing where output was
   expected is not a pass until you have confirmed it ran (see the shell note at the end of this
   file).
4. Stage explicit paths:
   ```bash
   git add path/one.ext path/two.test.ext
   git commit -m "fix(<scope>): <summary> (<slug>-001)"
   ```
5. Record the SHA straight after the commit:
   ```bash
   git rev-parse --short HEAD
   ```
6. If an item is blocked (its check cannot pass without a change outside it), stop that item, leave
   it uncommitted, and record the blocker. Do not commit a half-change.

## Implementer summary template

Write to `docs/remediation/summaries/<date>-<slug>-rN.md`.

````markdown
# Summary: <topic>, round N

- Plan: `docs/remediation/<date>-<slug>-plan-rN.md`
- Branch: `remediation/<slug>-<date>`
- HEAD after round: <short SHA>
- Tree state at end: <output of `git status --porcelain`, or "clean">

## Items

| ID | Status | Commit | Acceptance command | Exit | First line of output |
|---|---|---|---|---|---|
| <slug>-001 | committed | `a1b2c3d` | `mise exec -- pytest tests/test_parse.py::test_null` | 0 | `1 passed in 0.12s` |
| <slug>-002 | blocked | none | `<command>` | 1 | `<first line>`; blocker: <reason> |

Status values: committed, blocked, not started.

## Out-of-plan changes

| Commit | Paths | Reason |
|---|---|---|
| none | | |

## Notes

<anything the reviewer must know that is not in the table. Keep it to facts.>
````

A row is not evidence unless its SHA and exit status are present. The reviewer treats a row
without them as not done.

## Conformance review template

Write to `docs/remediation/reviews/<date>-<slug>-rN.md`.

````markdown
# Conformance review: <topic>, round N

- Reviewer: <session, one line>
- Reviewed: branch `remediation/<slug>-<date>` at <full SHA>
- Plan: `<path>`. Summary: `<path>`. Audit: `<path>`.
- Scope of review: the plan's items, every commit since `pre-remediation-<date>`, and every path
  changed since that tag. Not reviewed: <anything else>.
- Checks re-run: <n of n>, in a throwaway clone at <SHA>.

## Item-by-item conformance

| ID | Status | Commit(s) | Evidence | Note |
|---|---|---|---|---|
| <slug>-001 | done | `a1b2c3d` | `path/one.ext:42`; check exit 0 | |
| <slug>-002 | partial | `d4e5f6a` | `path/two.ext:17`; check exit 1 | Fix covers the first case only |
| <slug>-003 | changed scope | `b7c8d9e` | `path/three.ext:9` | Plan said X, commit did Y |
| <slug>-004 | not done | none | none | Blocked in summary; no commit |

Status values:
- **done**: the change is in the commit, the acceptance check passes on the branch tip, and nothing
  the item forbade is present.
- **partial**: some of the change is present, or the check passes on one case and not another.
- **not done**: no committed change, or the change is absent from the tip.
- **changed scope**: a change was made, but it differs from the item. Quote both the item and the
  commit.

## Out-of-plan changes

Paths changed since the tag that no plan item names:

| Path | Commit | Assessment |
|---|---|---|
| `path/unplanned.ext` | `e0f1a2b` | Needed by `<slug>-002`, flagged, not a fault |

## New findings

| New ID | Severity | Evidence | Claim |
|---|---|---|---|
| <slug>-009 | High | `path/x.ext:5` | <claim> |

New IDs continue the slug's numbering. Nothing already issued is reused or renumbered.

## Verdict

`NEXT ROUND: <count> open` or `CONVERGED`. Give the count of items that are partial, not done or
changed scope, plus new findings. Name them by ID.
````

## Reviewer checklist

1. Check the summary's rows against the commits: each SHA exists on the branch, and each commit's
   paths match the item's "Files" line.
2. Re-run each acceptance check in a throwaway clone of the branch tip. Record the exit status you
   saw, not the one the summary gave.
3. Derive the list of planned paths from the plan's "Files" lines, and compare it with the paths
   changed since the tag:
   ```bash
   plan=docs/remediation/<date>-<slug>-plan-rN.md
   grep -E '^- Files:' "$plan" | grep -oE '`[^`]+`' | tr -d '`' | sort -u > /tmp/planned-paths.txt
   git diff --name-only pre-remediation-<date> remediation/<slug>-<date> | sort -u > /tmp/changed-paths.txt
   wc -l /tmp/planned-paths.txt /tmp/changed-paths.txt
   echo "changed but not planned:"; comm -13 /tmp/planned-paths.txt /tmp/changed-paths.txt
   echo "planned but not changed:"; comm -23 /tmp/planned-paths.txt /tmp/changed-paths.txt
   ```
   Read the two lists. An empty "changed but not planned" list is the pass; a non-empty one is a
   finding, or an out-of-plan row the summary should have declared.
4. Confirm the target's release and output folders are unchanged: `git diff --name-only
   pre-remediation-<date> remediation/<slug>-<date> -- <release-folder>` prints nothing.
5. Write the review and update `docs/remediation/LEDGER.md`. The reviewer's own writes stay inside
   `docs/remediation/`.

## LEDGER.md

One table, one row per finding ID, across all rounds. It is the only place the cross-round history
lives, so keep it current.

```markdown
| ID | Severity | First seen | Status | Last round | Evidence of status |
|---|---|---|---|---|---|
| <slug>-001 | High | audit | done | r1 | `a1b2c3d`, review r1 |
| <slug>-002 | High | audit | partial | r1 | review r1, item 2 |
| <slug>-009 | High | review r1 | open | r1 | new finding, review r1 |
```

Status values: open, partial, done, deferred, withdrawn. A withdrawn finding keeps its row and gets
a reason; it is not deleted.

## Note on the shell and silent checks

Do not assume which shell runs the checks: an agent's shell tool is often zsh rather than bash.
Two traps matter here. First, do not rely on `set -e` to stop at a failing command, so gate the
next step with `&&` or test `$?`. Second, in zsh an unquoted `$var` does not word-split, so build
lists in files, as above, and not in a shell variable. In either case an empty
or quiet result is evidence only after the count and the exit status have been read.
