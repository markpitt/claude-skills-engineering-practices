# Relay protocol: implementer and reviewer in separate sessions

Use this when the audit and the review are run by one session and the execution by another, so
that the reviewer never writes to the code it is judging. The two sessions talk only through the
relay: the reviewer's reply, the implementer's reply, and the files at the fixed paths.

## Roles

| Role | Writes to | Never writes to |
|---|---|---|
| Implementer | The working branch, `docs/remediation/summaries/` | `docs/remediation/reviews/`, `NEXT-PROMPT.md`, `LEDGER.md` |
| Reviewer | `docs/remediation/reviews/`, `docs/remediation/NEXT-PROMPT.md`, `docs/remediation/LEDGER.md` | The target tree, any release or output folder, the branch |

The reviewer is read-only on the target. Its only writes are the three paths above, and the check
for that is `git status --porcelain` before and after its pass. Any other path that changes is a
fault, reported in the review, and the review is not used until the tree is restored by the human.

The implementer does not edit the ledger or the reviews. Keeping that split stops the two sessions
overwriting each other's record.

## The relay messages

### Reviewer to human: the reply is always one of two things

1. **The next prompt.** The reply is the header line below followed by the exact contents of
   `docs/remediation/NEXT-PROMPT.md`.

   ```text
   Written to: <absolute path of NEXT-PROMPT.md in the reviewer's checkout>
   SHA-256: <output of sha256sum on that file>
   ```

2. **The verdict.** When the review finds nothing new, the reply is exactly:

   ```text
   CONVERGED
   Review: <absolute path of the review file>
   Branch: remediation/<slug>-<date> at <full SHA>
   ```

A chat summary is never the reply. The reviewer's findings live in the review file; the reply
carries only what the implementer needs to act, or the verdict.

### Human to implementer

The human pastes the reviewer's reply. If the reply is a prompt, the implementer writes the
prompt text to the fixed path in its own checkout, so the path is always `docs/remediation/NEXT-PROMPT.md`
relative to its own repository root.

### Implementer: first act on a prompt

1. Write the prompt text to `docs/remediation/NEXT-PROMPT.md` in its own checkout.
2. Check the hash against the one in the reply:
   ```bash
   sha256sum docs/remediation/NEXT-PROMPT.md
   ```
   The digest must equal the SHA-256 in the reply. A mismatch means the prompt was saved from the
   wrong place, or pasted with changes. Stop and report the mismatch to the human; do not start.
3. Confirm the branch and tag named in the prompt exist: `git rev-parse --verify pre-remediation-<date>`
   and `git rev-parse --verify remediation/<slug>-<date>`. Missing means stop and report.
4. Execute as `execution-and-review.md` describes.

### Implementer to human: the reply is the summary

The reply is the summary's item table, the HEAD SHA, the path of the summary file and the output of
`git status --porcelain`. Do not reply "done". A round with a blocked item is reported with the
item named, so the reviewer can carry it.

### Reviewer: receiving a summary

Run the review as `execution-and-review.md` describes, then write the next prompt or the verdict.

## The next prompt

`docs/remediation/NEXT-PROMPT.md` is written in full each round and overwritten. The previous round's
prompt is recorded in its own summary and review, so nothing is lost by the overwrite. The prompt
must stand on its own, because the implementer session may not inherit any rule from the reviewer's
session.

````markdown
# Remediation round N+1: <topic>

Repository root: the directory containing this file's `docs/` folder.
Branch: `remediation/<slug>-<date>`. Start from the branch tip, not from the tag.
Plan: `docs/remediation/<date>-<slug>-plan-r<N+1>.md` (read it first, in full).
Carried-over IDs: <list>. Each is an item in the plan, with its earlier status in `LEDGER.md`.

## Standing rules (restated here; you do not inherit them)

- Execute items in the plan's order. Make only the change each item lists.
- One atomic commit per item. Stage explicit paths. Never `git add .` or `git add -A`.
- Never commit to, push or merge `main`.
- Do not change anything under `<release or output folders, named explicitly>`.
- Do not renumber or rename any finding ID.
- Report each item with its commit SHA, the acceptance command, its exit status and the first line of
  its output. A row without these is not done.
- Write your summary to `docs/remediation/summaries/<date>-<slug>-rN+1.md` and commit it.
- Your final reply is the summary's item table, the HEAD SHA, the summary path, and the output of
  `git status --porcelain`. Do not reply "done".

## Items this round

<The plan's items for this round, copied in full, not summarised. Each keeps its acceptance check.>

## Stop conditions

- A prompt hash that does not match the reply: stop before starting.
- A branch or tag that does not exist: stop.
- An acceptance check that cannot pass without a change outside its item: stop that item, record
  the blocker, and continue with independent items only.
````

Quote the items in full. The implementer does not have the plan in its context, and a summary of an
item is where the acceptance check gets lost.

## Common failures

| Failure | Cause | Check |
|---|---|---|
| The prompt is saved to the wrong relative path | The session writes to its current working directory, not the repository root | The hash in the reply must match `sha256sum docs/remediation/NEXT-PROMPT.md` in the implementer's checkout |
| The reviewer edits the release or output folder | A fix was made in the review pass, rather than recorded | `git status --porcelain` before and after the pass; the paths must all sit under `docs/remediation/` |
| The implementer claims done without evidence | The summary is a prose sentence, not a table | A row with no SHA and no exit status is not done |
| The reviewer replies with a chat summary | The reply was written as a report | Only the two forms above are accepted |
| A carried-over finding loses its ID | The next plan was renumbered | IDs are copied from `LEDGER.md`, never reissued |
| The loop converges early | One clean review was taken as convergence | Two consecutive clean reviews are needed |
| The implementer adds unplanned work | "While I was there" | Out-of-plan rows and commits are reported and the reviewer lists them |

## When the two sessions disagree about a finding

The reviewer records the disagreement in the review as an open point, with the evidence each side
gave, and carries the finding as open. It does not settle the point by re-running the audit, and it
does not drop the finding. The human decides whether it becomes a deferral.
