# DevSkills

Shared Claude Code skill repository for GitHub-based development workflows.

Contains slash commands that enable AI-assisted development: from loading a GitHub issue to a fully committed, reviewed pull request — in one command.

---

## Getting Started

### Prerequisites

- [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code) installed
- [GitHub CLI (`gh`)](https://cli.github.com) installed and authenticated (`gh auth login`)
- This repository cloned locally

---

### Step 1: Install to a Target Project

Run from the **DevSkills repository**:

```
/skill-install
```

This copies `.claude/commands/` to your target project and sets the `devSkillsSource.path` reference.

### Step 2: Configure the Target Project

Navigate to your project and run Claude Code:

```bash
cd C:\path\to\your\project
claude
```

Then run:

```
/skill-setup
```

This auto-detects your GitHub owner/repo from the git remote and writes `.claude/config.json`.

### Step 3: Keep Up to Date

When DevSkills is updated, run from your target project:

```
/skill-update
```

---

## Available Commands

| Command | Usage | Description |
|---------|-------|-------------|
| `/git-issue-load` | `/git-issue-load 123` | Load a GitHub issue with full context |
| `/git-issue-create` | `/git-issue-create` | Create a new GitHub issue |
| `/git-issue-status` | `/git-issue-status 123` | Show issue state, linked PRs, recent activity |
| `/git-issue-plan` | `/git-issue-plan 123` | Generate a file-level implementation plan |
| `/git-branch` | `/git-branch 123` | Create feature branch from issue |
| `/git-commit` | `/git-commit` | Smart grouped commits with emoji |
| `/git-pr` | `/git-pr` | Create pull request with generated description |
| `/git-pr-code-review` | `/git-pr-code-review 45` | Review PR diff, optionally post findings |
| `/unit-test-build` | `/unit-test-build` | Generate unit tests for changed files |
| `/workflow` | `/workflow 123` | Full end-to-end: issue → branch → code → tests → PR → review |
| `/skill-setup` | `/skill-setup` | Configure this project (writes config.json) |
| `/skill-install` | `/skill-install` | Install skills to a target project |
| `/skill-update` | `/skill-update` | Pull latest skills from DevSkills source |

---

## Typical Workflow

```
# 1. Pick an issue and plan it
/git-issue-plan 123

# 2. Run the full workflow
/workflow 123

# — or run steps individually —
/git-branch 123
# ... write code ...
/unit-test-build
/git-commit
/git-pr
/git-pr-code-review 45
```

---

## Configuration

Each project uses `.claude/config.json` (not committed to the project repo):

```json
{
  "github": {
    "owner": "your-org",
    "repo": "your-repo",
    "defaultBranch": "main",
    "defaultMergeTarget": "main"
  },
  "git": {
    "ticketPattern": "#\\d+"
  },
  "devSkillsSource": {
    "path": "C:\\path\\to\\DevSkills"
  }
}
```

Generate this automatically with `/skill-setup`.
