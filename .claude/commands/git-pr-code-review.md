# GitHub PR Code Review

Review a pull request's diff against general coding standards and best practices. Optionally post findings as PR comments.

## Usage

```
/git-pr-code-review <pr-number>
```

**Example:** `/git-pr-code-review 45`

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`.

---

## Step 1: Fetch PR Diff and Metadata

Run in parallel:

```bash
# PR metadata
gh pr view $ARGUMENTS \
  --json number,title,body,baseRefName,headRefName,state,files \
  --repo <github.owner>/<github.repo>

# Full diff
gh pr diff $ARGUMENTS \
  --repo <github.owner>/<github.repo>
```

---

## Step 2: Analyze the Diff

Review each changed file for the following categories. Only report genuine issues — do not flag style preferences as violations.

### Correctness
- Logic errors or off-by-one mistakes
- Null/undefined not handled where expected
- Async operations not properly awaited
- Incorrect error propagation

### Security
- User input used without validation or sanitization
- Hardcoded secrets, tokens, or passwords
- SQL/command injection vectors
- Insecure direct object references

### Code Quality
- Functions exceeding ~40 lines (unit size)
- Deeply nested conditionals (complexity > 4)
- Duplicated logic that should be extracted
- Dead code or unreachable branches
- Magic numbers or strings that should be constants

### Test Coverage
- New public functions with no tests added
- Changed logic with no test update

### Documentation
- Public API or exported function with no description (only flag if project shows existing JSDoc/docstring convention)

---

## Step 3: Format Findings

For each finding:

```
### [SEVERITY] filename.ext (line N)

**Category**: [Correctness / Security / Code Quality / Test Coverage / Documentation]
**Finding**: [1-2 sentence description of the issue]
**Suggestion**: [concrete fix or approach]
```

Severity levels:
- **Critical** — security vulnerabilities, data loss risk
- **High** — bugs that will cause incorrect behavior
- **Medium** — code quality issues that increase maintenance risk
- **Low** — minor improvements

---

## Step 4: Ask to Post Comments

Ask the user:

> **Post these findings as PR review comments?**
>
> - Yes — post to PR
> - No — display only

If **Yes**, post a single review with all findings using:

```bash
gh pr review $ARGUMENTS \
  --comment \
  --body "<full review text>" \
  --repo <github.owner>/<github.repo>
```

---

## Step 5: Output Report

```
## Code Review — PR #$ARGUMENTS

| Severity | Count |
|----------|-------|
| Critical | N     |
| High     | N     |
| Medium   | N     |
| Low      | N     |

---

[findings formatted as above]

---

### Summary

[1-3 sentence overall assessment: what's done well, what must be fixed before merge]
```

If no findings:
```
No issues found. PR looks good to merge.
```

---

## Error Handling

### PR not found:
```
Error: PR #$ARGUMENTS not found in <github.owner>/<github.repo>.
```

### PR is already closed/merged:
Warn the user but continue — the review is still useful for learning.
