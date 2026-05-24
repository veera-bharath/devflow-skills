# Create Feature Branch from GitHub Issue

Create and push a new branch named from the issue number and title.

## Usage

```
/git-branch <issue-number>
```

**Example:** `/git-branch 123`

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`, `git`.

---

## Step 1: Load Issue Title

```bash
gh issue view $ARGUMENTS --json number,title \
  --repo <github.owner>/<github.repo>
```

Extract the **title** to use in the branch name.

---

## Step 2: Checkout Default Branch and Pull

```bash
git checkout <github.defaultMergeTarget from config>
git pull
```

---

## Step 3: Build Branch Name

Convention:

```
feature/<issue-number>-<slug>
```

Where `<slug>` is the issue title converted to a URL-friendly slug:
- Lowercase
- Replace spaces and special characters with hyphens
- Remove consecutive hyphens
- Truncate at ~50 characters, breaking at a word boundary

**Example:** Issue #123 "Add dark mode toggle for settings page"
```
feature/123-add-dark-mode-toggle-for-settings-page
```

For bug fix issues (label contains `bug` or `fix`), use `fix/` prefix instead of `feature/`.

---

## Step 4: Create and Push Branch

```bash
git checkout -b <branch-name>
git push -u origin <branch-name>
```

---

## Step 5: Output

```
## Branch Created

| Field    | Value                     |
|----------|---------------------------|
| Branch   | [branch-name]             |
| Based on | [github.defaultMergeTarget] |
| Issue    | #$ARGUMENTS — [title]     |
| Remote   | pushed to origin          |
```

---

## Error Handling

### Branch already exists:
```
Warning: Branch already exists. Checking out existing branch.
```
Checkout the existing branch and pull latest.

### Issue not found:
```
Error: Issue #$ARGUMENTS not found.
```

### Push fails:
```
Error: Failed to push branch to origin.
Verify you have push access and your git credentials are configured.
```
