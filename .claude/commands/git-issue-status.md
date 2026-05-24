# GitHub Issue Status

Show the current status of an issue: state, linked branch, linked PRs, and recent activity.

## Usage

```
/git-issue-status <issue-number>
```

**Example:** `/git-issue-status 123`

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`.

---

## Step 1: Fetch Issue State

Run in parallel:

```bash
# Issue details
gh issue view $ARGUMENTS \
  --json number,title,state,labels,assignees,comments,createdAt,updatedAt,url \
  --repo <github.owner>/<github.repo>

# Linked PRs
gh pr list \
  --search "closes #$ARGUMENTS OR fixes #$ARGUMENTS OR resolves #$ARGUMENTS" \
  --repo <github.owner>/<github.repo> \
  --json number,title,state,headRefName,url,isDraft

# Current branch (to detect if working branch matches)
git branch --show-current
```

---

## Step 2: Detect Work Status

Based on the data:

- **No branch, no PR**: Work has not started
- **Branch exists matching issue number**: Work is in progress locally
- **PR open (draft)**: Work in progress, PR draft open
- **PR open (ready)**: PR is ready for review
- **PR merged**: Issue work is merged
- **Issue closed**: Issue is resolved

---

## Step 3: Display Output

```
## Issue #$ARGUMENTS Status

**Title**: [title]
**State**: [open / closed]
**Labels**: [labels or none]
**Assignees**: [assignees or unassigned]
**Last updated**: [updatedAt]
**URL**: [url]

---

### Work Status: [detected status label]

| Item              | Detail                        |
|-------------------|-------------------------------|
| Local branch      | [branch name or "not found"]  |
| Pull Request      | [PR #N — title — state or "none"] |
| PR URL            | [url or —]                    |

---

### Recent Comments ([count])

[Last 3 comments: author, date, first 200 chars of body]
```

---

## Error Handling

### Issue not found:
```
Error: Issue #$ARGUMENTS not found.
```
