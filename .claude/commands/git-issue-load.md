# Load GitHub Issue

Fetch and display a GitHub issue with full context: title, body, labels, assignees, and comments.

## Usage

```
/git-issue-load <issue-number>
```

**Example:** `/git-issue-load 123`

`$ARGUMENTS` must be a GitHub issue number (e.g., `123`).

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`.

---

## Step 1: Fetch Issue

```bash
gh issue view $ARGUMENTS --json number,title,body,state,labels,assignees,milestone,comments,url \
  --repo <github.owner>/<github.repo>
```

---

## Step 2: Fetch Linked PRs

Search for PRs that reference this issue:

```bash
gh pr list --search "closes #$ARGUMENTS OR fixes #$ARGUMENTS OR resolves #$ARGUMENTS" \
  --repo <github.owner>/<github.repo> \
  --json number,title,state,url
```

---

## Step 3: Display Output

```
## Issue #$ARGUMENTS — [title]

**State**: [state]
**Labels**: [labels comma-separated, or "none"]
**Assignees**: [assignees comma-separated, or "unassigned"]
**Milestone**: [milestone or "none"]
**URL**: [url]

---

### Description

[body — render markdown as-is]

---

### Linked PRs

| PR | Title | State |
|----|-------|-------|
| #N | ...   | open  |

(show "No linked PRs found" if empty)

---

### Comments ([count])

[For each comment show: author, timestamp, body — separated by ---]
```

---

## Error Handling

### Issue not found:
```
Error: Issue #$ARGUMENTS not found in <github.owner>/<github.repo>.
Verify the issue number and that config.json has the correct owner/repo.
```

### Not authenticated:
```
Error: Not authenticated with GitHub.
Run: gh auth login
```
