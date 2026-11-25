---
name: audit-remediation-loop
description: Runs the full audit, remediation and review loop. A read-only audit with numbered, evidenced findings, a remediation plan with one executable item per finding, a completeness check against the audit, execution as one commit per item, an item-by-item conformance review, and gap-closing rounds until convergence. Includes the relay between an implementer session and a read-only reviewer session. Use when asked to audit then remediate, write a remediation plan, run a conformance review, close audit gaps, or relay a remediation prompt between sessions. Do NOT use for a one-shot code review or red-team read, for root-cause analysis of a single failure (use root-cause-analysis), or for a fix with no audit behind it.
license: Apache-2.0
metadata:
  version: "1.0.0"
  tags: "audit, remediation, conformance-review, workflow, multi-session, gap-closing"
---

# Audit remediation loop

Take a codebase, configuration or document set through one closed loop: find the defects with evidence, plan every fix so that nothing is silently dropped, execute the plan one commit at a time, check the result against the plan, and repeat on the gaps until a reviewer finds nothing new.

The loop is deliberately bureaucratic. Its failure modes are quiet: a finding that never reaches the plan, a "done" with no evidence, a reviewer that edits the thing it was reviewing. Each rule below exists to make one of those visible.

## Critical rules

1. **The audit is read-only.** The auditor edits nothing in the target, including release and output folders.
2. **Every finding ID appears in the plan, or in the deferral table with a reason.** A plan that drops an audit finding without a record has failed, whatever else it does well.
3. **A plan item executes without judgement.** Any decision an item needs is settled in the plan's decision log before execution starts.
4. **"Done" needs evidence:** a commit SHA, the acceptance command, its exit status and its output. A row without these is not done.
5. **One atomic commit per plan item.** Stage explicit paths only. Never `git add .` or `git add -A`.
6. **Finding IDs are permanent.** A carried-over finding keeps its original ID across rounds. Nothing is renumbered.

## Fixed paths

Paths are relative to the repository root. In a hand-off, state them in absolute form. `<date>` is `YYYY-MM-DD`, `<slug>` is the topic in lower-case hyphens, `N` is the round number.

| Artefact | Path |
|---|---|
| Audit report | `docs/audits/<date>-<slug>.md` |
| Remediation plan, round N | `docs/remediation/<date>-<slug>-plan-rN.md` |
| Implementer summary, round N | `docs/remediation/summaries/<date>-<slug>-rN.md` |
| Conformance review, round N | `docs/remediation/reviews/<date>-<slug>-rN.md` |
| Ledger of finding IDs and status | `docs/remediation/LEDGER.md` |
| Relay prompt (always this path, overwritten each round) | `docs/remediation/NEXT-PROMPT.md` |
| Pre-work tag | `pre-remediation-<date>` (round 2 onwards: `pre-remediation-<date>-rN`) |
| Working branch | `remediation/<slug>-<date>` |

If a path already exists for the same date and slug, use the next free `-rN` suffix and say so in the file header. Do not overwrite an earlier round's record.

## Situation to reference

| Situation | Load |
|---|---|
| Writing an audit report, or checking its evidence | `references/audit-report.md` |
| Writing the remediation plan, or running its completeness check | `references/remediation-plan.md` |
| Executing the plan, or reviewing the result against it | `references/execution-and-review.md` |
| Running the loop across two sessions, or writing a relay prompt | `references/relay-protocol.md` |

## Procedure

### 1. Audit (read-only)

1. Record the state being audited:
   ```bash
   git rev-parse HEAD
   git status --porcelain
   ```
   Put both outputs in the report header. A dirty tree is recorded, not hidden. Do not start remediation on a dirty tree until it is clean or the human has accepted it in writing.
2. State the scope in the report: included paths, excluded paths with the reason for each (generated output, vendored code, a release folder), and anything the audit did not read.
3. Write the report to `docs/audits/<date>-<slug>.md` using `references/audit-report.md`. Each finding is a heading `### <slug>-NNN: <title>` with severity, evidence as `path:line`, the claim, the impact and a direction (not a fix).
4. Re-check every evidence citation against the file, one command per citation:
   ```bash
   sed -n '<line>p' <path>
   ```
   The quoted text must match. Record in the report how many citations were checked and how many failed. A failed citation is corrected or removed before the audit is committed.
5. Make no change to the target. Commit the audit report alone:
   ```bash
   git add docs/audits/<date>-<slug>.md
   git commit -m "docs(audit): <slug> audit at <short-sha>"
   ```

### 2. Plan

1. Write `docs/remediation/<date>-<slug>-plan-r1.md` using `references/remediation-plan.md`. There is one item per finding, and the item ID equals the finding ID.
2. Each item names exact files, the exact change, an acceptance check (a command, an expected exit status and expected output), its dependencies and its commit message.
3. Settle every decision before the items are written. Record each in the decision log with the option chosen and who chose it. An item containing "decide", "consider", "as appropriate" or "if needed" is not executable; rewrite it or settle the decision first.
4. Order the items by dependency first, then by severity.
5. Any finding not being fixed this round goes in the deferral table with a reason, an owner and a revisit trigger. A deferral is a decision: the human signs it off before execution starts.
6. Run the completeness check in `references/remediation-plan.md`. It must pass before execution. Commit the plan alone.

### 3. Pre-work

1. Confirm the tree is clean: `git status --porcelain` prints nothing.
2. Tag the audited commit, not whatever HEAD has moved to, unless the plan says otherwise:
   ```bash
   git tag pre-remediation-<date>
   ```
3. Create the working branch from that tag, or switch to it if it exists:
   ```bash
   git switch -c remediation/<slug>-<date> pre-remediation-<date>
   ```
4. Push the tag and branch only where the repository's policy allows it. Never push or commit to `main`.

### 4. Execute

1. For each item, in plan order:
   1. Make only the change the item lists.
   2. Run the acceptance check and record its exit status and output.
   3. If it is red, fix it within the item, or stop and record the item as blocked. Do not widen the item to make it pass.
   4. Stage the item's explicit paths and commit with the message the item gives.
2. Do not make an out-of-plan change. If one is unavoidable, for example a test needs an import, record it as an out-of-plan row with the reason and commit it separately with `(out-of-plan)` in the message.
3. Write `docs/remediation/summaries/<date>-<slug>-rN.md` using `references/execution-and-review.md`: one row per item with the SHA, the acceptance command, its exit status and the first line of its output. Commit it.
4. Do not claim the round is done. Report the rows, the HEAD SHA and the summary path, and let the reviewer decide.

### 5. Conformance review

Run this as a separate pass, by a reviewer that did not do the execution. It is read-only on the target.

1. Read the audit, the plan, the summary and the committed history:
   ```bash
   git log --oneline pre-remediation-<date>..remediation/<slug>-<date>
   git diff --stat pre-remediation-<date> remediation/<slug>-<date>
   ```
   Read committed content with `git show <sha>:<path>`. Do not check the branch out in the target.
2. Re-run every acceptance check on the branch tip, in a throwaway clone of the branch, never in the target.
3. Mark each item: **done**, **partial**, **not done** or **changed scope**, with evidence (SHA, `path:line`, check exit status). The template is in `references/execution-and-review.md`.
4. List every changed path that no plan item names. Derive the planned paths from the plan's "Files" lines, then compare the two lists with `comm`.
5. Record each new defect as a new finding under the same slug with the next free number. Do not renumber.
6. Write `docs/remediation/reviews/<date>-<slug>-rN.md`, and update `docs/remediation/LEDGER.md` with each ID's status.
7. End with one of two verdicts: `NEXT ROUND: <count> open` or `CONVERGED`.

### 6. Gap-closing round

1. Carry into the next plan, by ID, every item that is partial, not done or changed scope, and every new finding.
2. Write `docs/remediation/<date>-<slug>-plan-rN+1.md`, then return to step 2 (plan), then step 3 (pre-work) only if the branch has moved and a fresh tag is needed, then step 4.
3. Keep the same branch unless the human asks for a new one.

### 7. Stop

- **Converged:** two consecutive conformance reviews find nothing new, and every open item is either done or in the deferral table with sign-off. The second review records `CONVERGED`.
- **Called by the human:** record the open items in `LEDGER.md` as open, with the reason the loop stopped.
- **Never merge.** Merging is the human's decision. Report the branch and the final review path.

## Failure modes

| Failure | Guard |
|---|---|
| The relay prompt is saved to the wrong path | Always `docs/remediation/NEXT-PROMPT.md`. The hand-off states the absolute path and the SHA-256, and the implementer checks both before starting. |
| The reviewer edits the release or output folder | The reviewer's writes are limited to `docs/remediation/`. Run `git status --porcelain` before and after its pass. Any change outside that folder is a fault and is reported. |
| The implementer claims done without evidence | A row with no SHA and no exit status counts as not done. |
| The plan silently drops a finding | The completeness check must print nothing in either direction. |
| Scope creeps during execution | Out-of-plan rows and commits are listed by the reviewer, every round. |
| Premature convergence | Two consecutive clean reviews are required, not one. |
| Carried-over findings lose their IDs | IDs are immutable. The ledger is the cross-round record. |

## Two-session mode

When the reviewer and the implementer are different sessions, follow `references/relay-protocol.md`.
The reviewer is read-only on the target and writes only under `docs/remediation/`. Its reply is
always the next prompt, with its SHA-256, or a `CONVERGED` verdict; never a chat summary. The
implementer checks the prompt's hash before it starts, and replies with the summary's item table,
not with "done".

## Out of scope

A one-shot review, such as a code review or a hostile red-team read, is not this loop. It can supply evidence for an audit, but it does not run remediation or convergence. A single failure with an unknown cause belongs to `root-cause-analysis`, and its output can become one audit finding.
