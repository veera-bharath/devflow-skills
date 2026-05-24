# Install Skills to a Target Project

Copy the `.claude/` commands directory from DevSkills into a target project so all skills become available there.

## Usage

```
/skill-install
```

**This command must be run from the DevSkills repository.**

---

## Prerequisites Check

Verify you are in the DevSkills repository:

```bash
basename "$(git rev-parse --show-toplevel 2>/dev/null)"
```

If the result is NOT `DevSkills`, stop and display:
```
This command must be run from the DevSkills repository.
Navigate to DevSkills and try again.
```

Verify the commands directory exists:

```bash
test -d .claude/commands
```

---

## Step 1: Ask for Target Project Path

Ask the user:

> **What is the full path of the project to install skills into?**
> (e.g., `C:\Users\you\source\repos\my-project`)

Store as `PROJECT_PATH`.

---

## Step 2: Validate Target

Check the directory exists:

```bash
test -d "$PROJECT_PATH"
```

Check it is a git repository:

```bash
git -C "$PROJECT_PATH" rev-parse --show-toplevel 2>/dev/null
```

If not a git repo, warn the user but allow them to proceed.

If `.claude/` already exists in the target:

> **A `.claude/` directory already exists at `<PROJECT_PATH>/.claude/`.**
> Overwrite commands? (config.json will be preserved)

If yes, back up existing config:
```bash
cp "$PROJECT_PATH/.claude/config.json" "$PROJECT_PATH/.claude/config.json.bak" 2>/dev/null
```

---

## Step 3: Copy Commands

```bash
# Create target .claude dir
mkdir -p "$PROJECT_PATH/.claude"

# Copy commands and config template
cp -r .claude/commands "$PROJECT_PATH/.claude/"
cp .claude/config.template.json "$PROJECT_PATH/.claude/"
```

Do NOT copy `.claude/config.json` — it contains settings specific to the DevSkills repo.

---

## Step 4: Set devSkillsSource.path

Determine the DevSkills repo root:

```bash
DEVSKILLS_PATH="$(git rev-parse --show-toplevel)"
```

If `$PROJECT_PATH/.claude/config.json` exists (backed up), update its `devSkillsSource.path` field using the Edit tool.

If no `config.json` exists, copy the template:
```bash
cp "$PROJECT_PATH/.claude/config.template.json" "$PROJECT_PATH/.claude/config.json"
```

Then set `devSkillsSource.path` to `$DEVSKILLS_PATH` using the Edit tool.

---

## Step 5: Add to .gitignore

Check if `.claude/` is already in the target's `.gitignore`:

```bash
grep -q "^\.claude" "$PROJECT_PATH/.gitignore" 2>/dev/null
```

If not present, append:

```
# Claude Code (contains credentials and local config — managed by DevSkills)
.claude/
```

---

## Step 6: Display Summary

```
## Installation Complete

| Item              | Status             |
|-------------------|--------------------|
| Commands copied   | ✓ (13 commands)    |
| Config template   | ✓                  |
| devSkillsSource   | ✓ set to [path]    |
| .gitignore        | ✓ updated          |

## Next Steps

1. Navigate to your project:
   cd <PROJECT_PATH>

2. Start Claude Code:
   claude

3. Run the setup wizard:
   /skill-setup
```

---

## Error Handling

- If `cp` fails due to permissions, suggest running terminal as administrator
- If the target is on a network drive, warn about potential path issues
- If git is not available, skip git checks but continue with copy
