# Configure Skills for This Project

Run the setup wizard to generate `.claude/config.json` for the current project.

## Usage

```
/skill-setup
```

---

## Prerequisites Check

Verify the required tools are installed and authenticated:

```bash
# Check gh CLI
gh --version

# Check gh auth status
gh auth status

# Check git
git --version
```

If `gh` is not installed, stop and display:
```
GitHub CLI (gh) is required. Install it from: https://cli.github.com
```

If not authenticated, stop and display:
```
Not authenticated with GitHub. Run: gh auth login
```

---

## Step 1: Auto-Detect GitHub Settings

Read the git remote URL to extract owner and repo:

```bash
git remote get-url origin
```

Parse the URL:
- SSH: `git@github.com:owner/repo.git` → owner = `owner`, repo = `repo`
- HTTPS: `https://github.com/owner/repo.git` → owner = `owner`, repo = `repo`

Display detected values and ask the user to confirm or correct them.

---

## Step 2: Detect Default Branch

```bash
git remote show origin | grep "HEAD branch"
```

Or check config — commonly `main` or `master`.

---

## Step 3: Prompt for Remaining Settings

Ask for any values that could not be auto-detected:

| Setting | Question |
|---------|----------|
| `github.defaultMergeTarget` | What branch do PRs target? (e.g., `main`, `develop`) |

---

## Step 4: Write config.json

Write `.claude/config.json` with all collected values:

```json
{
  "github": {
    "owner": "<detected-or-entered>",
    "repo": "<detected-or-entered>",
    "defaultBranch": "<detected>",
    "defaultMergeTarget": "<entered>"
  },
  "git": {
    "ticketPattern": "#\\d+"
  },
  "devSkillsSource": {
    "path": "<path to DevSkills repo>"
  }
}
```

Set `devSkillsSource.path` to the path of this DevSkills repo:

```bash
# If running from DevSkills repo:
pwd
# Or ask the user for the path
```

---

## Step 5: Verify CLAUDE.md

Check if a `CLAUDE.md` exists in the project root:

```bash
test -f CLAUDE.md
```

If it does not exist, ask the user:

> **Create a CLAUDE.md for this project?**

If yes, create a minimal `CLAUDE.md`:

```markdown
# Project

[project name] — [one-line description]

## Commands

- `/git-issue-load <number>` — load a GitHub issue
- `/git-issue-plan <number>` — plan implementation
- `/git-branch <number>` — create feature branch
- `/git-commit` — smart grouped commits
- `/git-pr` — create pull request
- `/git-pr-code-review <pr>` — code review
- `/unit-test-build` — generate unit tests
- `/workflow <number>` — full end-to-end workflow
```

---

## Step 6: Display Summary

```
## Setup Complete

| Setting                    | Value                    |
|----------------------------|--------------------------|
| GitHub Owner               | [owner]                  |
| GitHub Repo                | [repo]                   |
| Default Branch             | [branch]                 |
| PR Target Branch           | [defaultMergeTarget]     |
| DevSkills Source           | [path]                   |
| config.json                | written to .claude/      |
| CLAUDE.md                  | [created / already exists] |

## Next Steps

Run any skill to get started:
  /git-issue-load <issue-number>
  /workflow <issue-number>
```
