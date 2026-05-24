# Smart Commit by Feature Groups

Analyze uncommitted changes, group them by feature or type, and create a separate commit for each group.

## Usage

```
/git-commit
```

---

## Commit Types

| Type         | Emoji | Description                                      |
|--------------|-------|--------------------------------------------------|
| feat         | ✨    | New feature or functionality                     |
| fix          | 🐛    | Bug fix                                          |
| hotfix       | 🚑    | Critical hotfix                                  |
| refactor     | ♻️    | Code refactoring without changing behavior       |
| style        | 💄    | UI/styling changes (CSS, layout)                 |
| docs         | 📝    | Documentation changes                            |
| test         | 🧪    | Adding or updating tests                         |
| config       | ⚙️    | Configuration file changes                       |
| perf         | ⚡    | Performance improvements                         |
| chore        | 🔧    | Maintenance tasks, dependencies                  |
| security     | 🔒    | Security-related changes                         |
| api          | 🔌    | API endpoint changes                             |
| db           | 🗃️    | Database/migration/model changes                 |
| ui           | 🎨    | Frontend component/view changes                  |
| build        | 🏗️    | Build system or external dependencies            |
| ci           | 👷    | CI/CD configuration changes                      |
| revert       | ⏪    | Revert previous changes                          |
| add          | ➕    | Add new file or dependency                       |
| remove       | ➖    | Remove file or dependency                        |
| move         | 🚚    | Move or rename files                             |
| upgrade      | ⬆️    | Upgrade dependencies                             |
| lint         | 🚨    | Fix linter warnings                              |
| wip          | 🚧    | Work in progress                                 |
| i18n         | 🌐    | Internationalization and localization            |
| typo         | ✏️    | Fix typos                                        |
| auth         | 🔐    | Authentication/authorization changes             |
| validation   | ✅    | Add or update validation                         |
| error        | 🥅    | Error handling improvements                      |

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `git`, `github`.

---

## Step 1: Branch and Ticket Setup

```bash
git branch --show-current
```

**If on a protected branch (`main`, `master`, `develop`):**
1. Warn: "You are on a protected branch. A new branch is required."
2. Ask for the issue number
3. Create branch with `/git-branch <issue-number>` before continuing

**If on a feature/fix branch:**
- Extract issue number from branch name (e.g., `feature/123-slug` → `#123`)
- If no number found, ask the user for the issue number
- Store it for use in commit messages

---

## Step 2: Gather Changes

Run in parallel:

```bash
git diff HEAD --name-only
git diff HEAD --stat
git status --short
```

Also check for untracked files that should be staged.

---

## Step 3: Analyze and Group Changes

Group changed files into logical commits. Consider grouping by:

1. **Feature domain** — files that implement the same feature together
2. **Layer** — e.g., all migration files together, all test files together, all config changes together
3. **Type** — pure refactoring separate from new features; dependency changes separate from code changes

Avoid mixing unrelated concerns in one commit.

---

## Step 4: Create Commits

For each group:

1. Stage files:
   ```bash
   git add <file1> <file2> ...
   ```

2. Commit with ticket, emoji, type, and description:
   ```bash
   git commit -m "#<issue-number> <emoji> <type>: <short description>"
   ```

**Format:**
```
#123 ✨ feat: add dark mode toggle to settings page
#123 🧪 test: add unit tests for settings toggle
#123 ⚙️ config: add darkMode key to app config
```

---

## Step 5: Output Summary

```
## Commits Created

| # | Issue | Type  | Files | Message                          |
|---|-------|-------|-------|----------------------------------|
| 1 | #123  | ✨ feat | 4   | add dark mode toggle             |
| 2 | #123  | 🧪 test | 2   | add unit tests for toggle        |
| 3 | #123  | ⚙️ config | 1  | add darkMode config key          |

Total: 3 commits, 7 files
```

---

## Step 6: Push

After all commits, push to remote:

```bash
git push
```

If no upstream is set:

```bash
git push -u origin <branch-name>
```

---

## Error Handling

### Nothing to commit:
```
No changes detected. Working tree is clean.
```

### Commit fails (hooks):
Show the hook output. Fix the issue, re-stage, and create a new commit. Never use --no-verify.
