# Update Skills from DevSkills Source

Pull the latest commands from the DevSkills repository and update the current project's `.claude/commands/` directory.

## Usage

```
/skill-update
```

Run this from a **target project** (not the DevSkills repo itself) to pull in the latest skills.

---

## Step 1: Read devSkillsSource Path

Read `.claude/config.json` and extract `devSkillsSource.path`:

```bash
cat .claude/config.json
```

If `config.json` does not exist or `devSkillsSource.path` is empty, stop and display:

```
Cannot update: devSkillsSource.path is not set in .claude/config.json.
Run /skill-install from the DevSkills repository to set up this project first.
```

Store as `DEVSKILLS_PATH`.

---

## Step 2: Verify Source Exists

```bash
test -d "$DEVSKILLS_PATH/.claude/commands"
```

If missing, stop:
```
DevSkills source not found at: <DEVSKILLS_PATH>
Verify devSkillsSource.path in .claude/config.json.
```

---

## Step 3: Pull Latest from DevSkills

```bash
git -C "$DEVSKILLS_PATH" checkout main
git -C "$DEVSKILLS_PATH" pull
```

If pull fails, stop and show the error. Do not copy stale files.

Show what changed:
```bash
git -C "$DEVSKILLS_PATH" log --oneline -10
```

---

## Step 4: Copy Updated Files

```bash
PROJECT_PATH="$(pwd)"

cp -r "$DEVSKILLS_PATH/.claude/commands" "$PROJECT_PATH/.claude/"
cp "$DEVSKILLS_PATH/.claude/config.template.json" "$PROJECT_PATH/.claude/"
```

Do NOT overwrite `.claude/config.json` — it contains project-specific settings.

---

## Step 5: Display Summary

```
## Update Complete

| Item              | Status                    |
|-------------------|---------------------------|
| Source            | [DEVSKILLS_PATH]          |
| Branch            | main (latest)             |
| Commands          | ✓ updated (13 files)      |
| Config template   | ✓ updated                 |
| config.json       | preserved (not changed)   |

Latest changes from DevSkills:
[last 5 git log lines from DevSkills repo]
```

---

## Error Handling

- If `git checkout main` fails due to uncommitted changes in DevSkills, inform the user to commit or stash those changes first
- If `cp` fails due to permissions, suggest running as administrator
- If DevSkills has diverged (no `main` branch), ask the user which branch to pull from
