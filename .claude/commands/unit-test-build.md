# Generate Unit Tests for Changed Files

Analyze files changed since the merge target branch and generate unit tests following the project's existing test conventions.

## Usage

```
/unit-test-build
```

Or target a specific file:

```
/unit-test-build src/services/authService.ts
```

---

## Configuration

**Before executing, read `.claude/config.json` using the Read tool.** Config sections used: `github`.

---

## Step 1: Identify Files to Test

If `$ARGUMENTS` is a file path, test only that file.

Otherwise, find all changed source files (excluding test files, config, and assets):

```bash
git diff <github.defaultMergeTarget>...HEAD --name-only
```

Filter to testable source files only (exclude: `*.test.*`, `*.spec.*`, `*.md`, `*.json`, `*.yml`, `*.css`, `*.png`, images, lock files).

---

## Step 2: Discover Test Conventions

Before generating any tests, read the existing test suite to understand conventions:

1. Find existing test files:
   ```
   Glob: **/*.{test,spec}.{ts,tsx,js,jsx,py,cs,java,go}
   ```

2. Read 2–3 representative test files to understand:
   - Test framework (Jest, Vitest, pytest, xUnit, JUnit, Go test, etc.)
   - File naming convention (`foo.test.ts` vs `foo.spec.ts` vs `test_foo.py`)
   - Test file location (co-located vs `__tests__/` vs `tests/` directory)
   - How mocks/stubs are set up
   - How assertions are written
   - What helper utilities or fixtures exist

---

## Step 3: Read Source Files

For each file to test, read it fully to understand:
- Exported functions, classes, or modules
- Input/output contracts
- Edge cases visible in the implementation
- Dependencies that need mocking

---

## Step 4: Generate Tests

For each source file, generate a test file that:

1. Follows the exact naming and location convention discovered in Step 2
2. Uses the same test framework and imports
3. Covers:
   - **Happy path** — expected inputs produce expected outputs
   - **Edge cases** — empty input, boundary values, null/undefined
   - **Error cases** — invalid input, dependency failures
4. Mocks external dependencies (network calls, DB, file system) using the project's existing mock pattern
5. Does NOT test implementation details — test observable behavior only

Write each generated test file using the Write tool.

---

## Step 5: Output Summary

```
## Unit Tests Generated

| Source File           | Test File                        | Tests Added |
|-----------------------|----------------------------------|-------------|
| src/services/auth.ts  | src/services/auth.test.ts        | 8           |
| src/utils/format.ts   | src/utils/format.test.ts         | 5           |

Total: [N] test files, [N] test cases

Run tests with: [detected test command, e.g., npm test / pytest / go test ./...]
```

---

## Error Handling

### No changed files:
```
No testable source files changed since <defaultMergeTarget>.
To target a specific file: /unit-test-build <path/to/file>
```

### No existing tests found:
Warn the user that no test conventions could be detected. Ask for the test framework and file structure before generating.

### File too large to analyze fully:
Read the exported public API section only and generate tests for public interfaces.
