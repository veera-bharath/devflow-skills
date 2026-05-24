# Full Feature Workflow

End-to-end workflow: from GitHub Issue to implemented, tested, committed, and reviewed Pull Request.

## Usage

```
/workflow <issue-number>
```

**Example:** `/workflow 123`

`$ARGUMENTS` must be a GitHub issue number.

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`, `git`.

---

## Step 1: Load Issue

Run `/git-issue-load $ARGUMENTS`.

Display the issue summary. Confirm the issue is **open** before proceeding. If it is closed, warn the user and ask if they want to continue anyway.

---

## Step 2: Plan Implementation

Run `/git-issue-plan $ARGUMENTS`.

Present the implementation plan and wait for user approval. If the user requests changes, update the plan and ask again. Do not proceed to code until the plan is approved.

---

## Step 3: Create Feature Branch

Run `/git-branch $ARGUMENTS`.

Display the created branch name and confirm push to origin succeeded.

---

## Step 4: Implement

Execute the approved plan from Step 2 task by task.

Follow these principles:
- Reuse existing patterns, components, and utilities found in Step 2 — do not invent new abstractions
- Keep changes minimal and scoped to the issue
- No inline styles, no magic strings/numbers, no commented-out code
- Validate at system boundaries (user input, external API responses)
- Do not add error handling for scenarios that cannot happen

After each task, confirm the file was written correctly before moving to the next.

---

## Step 5: Generate Unit Tests

Run `/unit-test-build`.

Tests are generated for all files changed in Step 4. If the issue is a pure config or documentation change, skip this step and note it in the summary.

---

## Step 6: Commit Changes

Run `/git-commit`.

Proceed with all grouped commits directly — do not ask for commit-by-commit confirmation. The issue number is `$ARGUMENTS`.

---

## Step 7: Create Pull Request

Run `/git-pr $ARGUMENTS`.

Display the PR URL and number. Store the PR number for the next step.

---

## Step 8: Code Review

Run `/git-pr-code-review <pr-number from Step 7>`.

After the review report is shown, ask the user:

> **Post these findings to the PR?**
> - Yes — post comments to PR
> - No — skip posting

---

## Step 9: Fix Review Findings

If there are **High** or **Critical** findings from Step 8:

1. Fix each finding in the relevant files
2. Run `/git-commit` for the fixes (commit type: `fix` or `refactor`)
3. Push:
   ```bash
   git push
   ```

Display:
```
## Fixes Applied

| Finding           | File          | Status |
|-------------------|---------------|--------|
| [finding summary] | [filename]    | Fixed  |
```

Skip this step if no High/Critical findings.

---

## Workflow Summary

After all steps complete:

```
## Workflow Complete

| Step                    | Status  | Details                              |
|-------------------------|---------|--------------------------------------|
| 1. Load Issue           | Done    | #$ARGUMENTS — [title]               |
| 2. Plan                 | Done    | [N] tasks planned                    |
| 3. Create Branch        | Done    | [branch-name]                        |
| 4. Implement            | Done    | [N] files changed                    |
| 5. Unit Tests           | Done    | [N] test files generated             |
| 6. Commit               | Done    | [N] commits                          |
| 7. Pull Request         | Done    | PR #[number]                         |
| 8. Code Review          | Done    | [N] findings ([N] critical/high)     |
| 9. Fix Findings         | Done    | [N] fixes applied                    |

PR URL:    [url]
Issue URL: https://github.com/<github.owner>/<github.repo>/issues/$ARGUMENTS
```

---

## Error Handling

- If any step fails, display the error clearly and ask the user how to proceed — do not silently skip
- If the issue is not found, stop immediately and report it
- If `gh` is not authenticated, prompt: `Run: gh auth login`
- If there are push conflicts, resolve them before continuing — do not force push
- If the plan is rejected twice, ask the user to describe what they want directly
