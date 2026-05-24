# Configure Skills for This Project

Run the setup wizard to generate `.claude/config.json` for the current project.

## Usage

```
/skill-setup
```

---

## Prerequisites Check

### 1. Check Git

```bash
git --version
```

If git is not found, stop and display:
```
Git is required but not installed.

Install Git:
  Windows : https://git-scm.com/download/win
  macOS   : brew install git   (or xcode-select --install)
  Ubuntu  : sudo apt install git
  Fedora  : sudo dnf install git

After installing, restart your terminal and run /skill-setup again.
```

### 2. Check GitHub CLI

```bash
gh --version
```

If `gh` is not found, detect the OS and display tailored install instructions:

**Detect OS:**
```bash
uname -s 2>/dev/null || echo "Windows"
```

**Windows** (output is `Windows` or contains `MINGW`/`CYGWIN`):
```
GitHub CLI (gh) is not installed.

Install options (choose one):

  Option A — winget (recommended, built into Windows 10/11):
    winget install --id GitHub.cli

  Option B — Scoop:
    scoop install gh

  Option C — Chocolatey:
    choco install gh

  Option D — Manual installer:
    Download from: https://github.com/cli/cli/releases/latest
    Run the .msi installer, then restart your terminal.

After installing, restart your terminal and run /skill-setup again.
```

**macOS** (output is `Darwin`):
```
GitHub CLI (gh) is not installed.

Install options (choose one):

  Option A — Homebrew (recommended):
    brew install gh

  Option B — MacPorts:
    sudo port install gh

  Option C — Manual installer:
    Download from: https://github.com/cli/cli/releases/latest

After installing, run /skill-setup again.
```

**Linux** (output is `Linux`):

Detect distro:
```bash
cat /etc/os-release 2>/dev/null | grep ^ID=
```

Display the matching instructions:
```
GitHub CLI (gh) is not installed.

Install for your distro:

  Debian/Ubuntu:
    sudo apt update && sudo apt install gh

  Fedora/RHEL:
    sudo dnf install gh

  Arch:
    sudo pacman -S github-cli

  Other / Manual:
    https://github.com/cli/cli/releases/latest

After installing, run /skill-setup again.
```

Stop here if `gh` is not installed — do not proceed until it is.

### 3. Check GitHub Authentication

```bash
gh auth status
```

**If authenticated**, continue to Step 1.

**If not authenticated**, guide the user through login:

```
GitHub CLI is installed but you are not logged in.

Starting GitHub authentication...
```

Then run the interactive login:
```bash
gh auth login
```

The login flow will:
1. Ask: GitHub.com or GitHub Enterprise → select **GitHub.com**
2. Ask: preferred protocol → select **HTTPS** (or SSH if preferred)
3. Ask: authenticate with browser or token → select **Login with a web browser**
4. Display a one-time code → user copies it
5. Open browser → user pastes the code and approves

After the command completes, verify it succeeded:
```bash
gh auth status
```

If still not authenticated after the attempt, display:
```
Authentication did not complete. Please try manually:
  gh auth login

Or authenticate with a Personal Access Token:
  1. Go to: https://github.com/settings/tokens/new
  2. Select scopes: repo, read:org, read:user
  3. Copy the token
  4. Run: gh auth login --with-token
     and paste your token when prompted.
```

Stop if authentication cannot be confirmed. Do not proceed with config until `gh auth status` returns a logged-in user.

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
