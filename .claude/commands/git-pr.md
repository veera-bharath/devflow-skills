# Create GitHub Pull Request

Create a PR from the current branch to the default merge target, with a generated description from commits and linked issue.

## Usage

```
/git-pr
```

Or with an explicit issue number:

```
/git-pr 123
```

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`, `git`.

---

## Step 1: Gather Context

Run in parallel:

```bash
# Uncommitted changes check
git status --short

# Current branch
git branch --show-current

# Commits ahead of merge target
git log <github.defaultMergeTarget>..HEAD --oneline

# Files changed
git diff <github.defaultMergeTarget>...HEAD --stat
```

If there are uncommitted changes, warn the user and ask if they want to run `/git-commit` first.

---

## Step 2: Detect Issue Number

- From `$ARGUMENTS` if provided
- Else extract from branch name (e.g., `feature/123-slug` → `123`)
- Else ask the user

---

## Step 3: Load Issue for PR Title

```bash
gh issue view <issue-number> --json number,title \
  --repo <github.owner>/<github.repo>
```

PR title format:
```
#<issue-number> <issue-title>
```

---

## Step 4: Ensure Branch is Pushed

```bash
git rev-parse --abbrev-ref @{upstream} 2>/dev/null
```

If no upstream set:
```bash
git push -u origin <branch-name>
```

---

## Step 5: Generate PR Description

Analyze `git log <github.defaultMergeTarget>..HEAD --oneline` and `git diff --stat` to write:

```markdown
## Summary
- <bullet 1>
- <bullet 2>
- <bullet 3>

## Changes
- <file or module> — <what changed>
- <file or module> — <what changed>

## Testing
- <how to test scenario 1>
- <how to test scenario 2>

Closes #<issue-number>
```

---

## Step 6: Create PR

```bash
gh pr create \
  --title "#<issue-number> <issue-title>" \
  --body "<generated description>" \
  --base <github.defaultMergeTarget> \
  --repo <github.owner>/<github.repo>
```

---

## Step 7: Output

```
## Pull Request Created

| Field         | Value             |
|---------------|-------------------|
| PR Number     | #[pr-number]      |
| Title         | [title]           |
| Source Branch | [branch-name]     |
| Target Branch | [defaultMergeTarget] |
| URL           | [url]             |

Next steps:
- Code review: /git-pr-code-review [pr-number]
```

---

## Error Handling

### PR already exists:
```
A PR already exists for this branch: #[number] — [url]
```
Display existing PR info and stop.

### No commits ahead of target:
```
No commits found ahead of <defaultMergeTarget>. Nothing to PR.
```

### Not authenticated:
```
Error: Not authenticated. Run: gh auth login
```
