# Plan Implementation from GitHub Issue

Load a GitHub issue and generate a concrete, file-level implementation plan before any code is written.

## Usage

```
/git-issue-plan <issue-number>
```

**Example:** `/git-issue-plan 123`

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`.

---

## Step 1: Load the Issue

Run `/git-issue-load $ARGUMENTS` to fetch full issue context (title, body, labels, comments).

---

## Step 2: Understand the Codebase

Based on keywords and domain from the issue, explore relevant parts of the codebase:

1. Identify the tech stack from the repo root (look for `package.json`, `*.csproj`, `pom.xml`, `requirements.txt`, `go.mod`, etc.)
2. Find files likely related to the issue scope using Glob and Grep
3. Read key files to understand existing patterns, naming conventions, and architecture
4. Note reusable components, utilities, services, or helpers that apply

---

## Step 3: Generate Implementation Plan

Produce a structured plan with:

### Acceptance Criteria
List the explicit criteria from the issue. If none stated, derive them from the description.

### Affected Areas
List the files or modules that will need to change, grouped by layer (e.g., backend, frontend, database, tests).

### Implementation Tasks

Number each task. Each task must reference a specific file path (or new file to create):

```
1. [layer] filename.ext — what to change and why
2. [layer] filename.ext — what to add
...
```

Order tasks from foundation to surface (data model → service → API → UI → tests).

### Out of Scope
Note anything mentioned in the issue that will NOT be addressed in this implementation.

### Open Questions
List any ambiguities or decisions that need user input before implementation starts.

---

## Step 4: Display Plan and Ask for Feedback

Show the full plan, then ask:

> **Does this plan look correct? Any changes before we start implementation?**

Wait for user response. If the user requests changes, update the plan and ask again.

Once approved, display:

```
Plan approved. Next steps:
- Create branch: /git-branch $ARGUMENTS
- Full workflow:  /workflow $ARGUMENTS
```

---

## Error Handling

### Issue not found:
```
Error: Issue #$ARGUMENTS not found.
```

### Codebase exploration yields nothing relevant:
Inform the user which search terms were tried and ask them to point to the relevant directory.
