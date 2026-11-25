# Audit report template and checks

Use this for `docs/audits/<date>-<slug>.md`. The structure is fixed because the completeness check
in `remediation-plan.md` reads the finding headings. Do not rename them.

## Template

````markdown
# Audit: <topic>

- Date: <date>
- Audited commit: <full SHA> (`git rev-parse HEAD`)
- Branch: <name>
- Tree state: <clean | dirty: list the paths from `git status --porcelain`>
- Auditor: <session or person, one line>

## Scope

- Included: <paths or globs>
- Excluded, with reason: <path>, <reason>. Examples: generated output, vendored code, release folders.
- Not read: <anything the audit could not or did not open, and why>

## Summary

| Severity | Count |
|---|---|
| Critical | 0 |
| High | 0 |
| Medium | 0 |
| Low | 0 |

Total findings: 0. Checked against: <number of files in scope> files.

## Findings

### <slug>-001: <one-line title>

- Severity: Critical | High | Medium | Low
- Confidence: HIGH | MEDIUM | LOW
- Evidence: `<path>:<line>`, quoted: `<the exact line>`
- Claim: <one sentence stating the defect as fact the evidence shows>
- Impact: <what goes wrong, for whom, and when>
- Direction: <the kind of change that would close it. Not the fix itself; the remediation plan writes that.>

### <slug>-002: <title>

(same fields)

## Negatives

Checks that were run and found nothing. A clean result is a result, so state it.

- <check name>: <what was examined, the scope, and the result>

## Verification

- Evidence citations checked: <n> of <n>. Method: `sed -n '<line>p' <path>` per citation.
- Citations that failed and how they were resolved: <none | list>
````

## Severity scale

| Severity | Meaning |
|---|---|
| Critical | Data loss, security exposure, or a defect that breaks the primary use. Blocks release. |
| High | A defect that produces wrong results in normal use, or a control that is missing where one is required. |
| Medium | A defect with a workaround, a maintainability cost that is already biting, or a missing check. |
| Low | Style, naming, dead code or documentation drift with no present effect. |

Severity describes the impact of the defect, not the effort to fix it. Effort belongs in the plan.

## Writing rules

- Every finding has evidence. A finding with no `path:line` is an opinion and goes in Negatives or
  is dropped.
- Quote the line. A reviewer must be able to confirm the claim without opening a second file, but
  must still be able to open the file.
- State the claim as what the evidence shows. "The function does not check for null at
  `src/parse.ts:88`" is a claim. "The parser is fragile" is not.
- Keep the direction short. If the direction is a full design, the finding is a design task and
  the plan must say so.
- Do not fix anything in the audit. A fix written into an audit is an unplanned change.
- One finding per defect. Two defects in one function are two findings, because they will be
  closed by separate items and verified separately.

## Citation check

Run one command per citation. Do not batch them into a script that prints only a count.

```bash
sed -n '88p' src/parse.ts
```

Compare the output with the quoted text. Where the line has moved, correct the citation to the new
line; do not keep a stale line number with a correct quote. Record the count in the Verification
section, and state the number checked out of the total, not just "all checked".
