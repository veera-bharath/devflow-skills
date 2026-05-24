# Create GitHub Issue

Interactively create a new GitHub issue and return its number for use in other skills.

## Usage

```
/git-issue-create
```

Or with a title pre-filled:

```
/git-issue-create Add dark mode support
```

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`.

---

## Step 1: Gather Issue Details

If `$ARGUMENTS` is provided, use it as the title. Otherwise ask the user for:

1. **Title** — short, imperative sentence describing the change
2. **Description** — what needs to be done and why (acceptance criteria, context)
3. **Labels** — fetch available labels first:
   ```bash
   gh label list --repo <github.owner>/<github.repo> --json name,description
   ```
   Present them and ask the user to pick (optional).
4. **Assignee** — ask if the issue should be self-assigned (`--assignee @me`) or left unassigned.

---

## Step 2: Create the Issue

```bash
gh issue create \
  --title "<title>" \
  --body "<description>" \
  --label "<label1>,<label2>" \
  --assignee "<assignee or omit>" \
  --repo <github.owner>/<github.repo>
```

Omit `--label` if no labels selected. Omit `--assignee` if unassigned.

---

## Step 3: Display Output

```
## Issue Created

| Field     | Value            |
|-----------|------------------|
| Number    | #[number]        |
| Title     | [title]          |
| Labels    | [labels or none] |
| Assignee  | [assignee or —]  |
| URL       | [url]            |

Next steps:
- Plan implementation: /git-issue-plan [number]
- Create branch:       /git-branch [number]
- Full workflow:       /workflow [number]
```

---

## Error Handling

### Authentication:
```
Error: Not authenticated. Run: gh auth login
```

### Label not found:
Skip invalid labels and warn the user. Do not fail the whole command.
