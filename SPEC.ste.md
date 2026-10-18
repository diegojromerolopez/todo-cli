# Todo CLI Specification

## 1. Overview
The project shall implement a command-line interface (CLI) application for personal TODO tasks. The CLI shall allow users to add, list, complete, and remove tasks. The application shall store tasks in a local JSON file.

---

## 2. Requirements

### 2.1. CLI Subcommands
The compiled binary (`todo`) shall support these commands and arguments:

1.  **`todo add "<task description>"`**
    *   **Description:** Add a new task to the list.
    *   **Behavior:** Append the task with pending status. Print a success message that includes the new task ID.
    *   **Output Example:** `Added task: "Buy milk" (ID: 1)`

2.  **`todo list`**
    *   **Description:** List all tasks. Show each task ID, description, status, and creation date.
    *   **Behavior:** Print a formatted list. Mark completed tasks with `[x]`. Mark pending tasks with `[ ]`.
    *   **Output Example:**
        ```
        ID  Status  Description  Created At
        1   [ ]     Buy milk     2026-06-21 23:15
        2   [x]     Clean room   2026-06-21 22:00
        ```

3.  **`todo done <id>`**
    *   **Description:** Mark the task with the specified ID as completed.
    *   **Behavior:** Update the task state. Print a confirmation message.
    *   **Output Example:** `Task 1 marked as completed.`
    *   **Error Case:** If the ID does not exist, print `todo: task not found: <id>` to `stderr`. Exit with code 1.

4.  **`todo rm <id>`**
    *   **Description:** Remove the task with the specified ID from the list.
    *   **Behavior:** Delete the task. Print a confirmation message.
    *   **Output Example:** `Task 1 removed.`
    *   **Error Case:** If the ID does not exist, print `todo: task not found: <id>` to `stderr`. Exit with code 1.

### 2.2. Persistence
*   Store tasks in a file named `todo.json`.
*   Use `./todo.json` in the current working directory as the default path. Apply this path precedence, from highest to lowest: `--file <path>` flag, `TODO_FILE` environment variable, and `./todo.json`. If the parent directory of `--file` does not exist, exit with code 1. Print `todo: directory does not exist: <dir>` to `stderr`. Treat an absent file as empty for `add` and `list`. Treat `done` and `rm` on an absent file as ID-not-found operations. Exit with code 1. If JSON is malformed, exit with code 1. Print `todo: corrupt file: <path>` to `stderr`.
*   Use this JSON schema for each task:
    *   `id` (integer)
    *   `description` (string)
    *   `completed` (boolean)
    *   `created_at` (ISO 8601 timestamp string, RFC3339 with `Z` suffix — UTC)
    *   `completed_at` (ISO timestamp string or null)

### 2.3. Architecture (DDD + DI)
Use this directory layout (DDD + DI):
- `cmd/todo/main.go` — composition root; wire injectors; contain no business logic.
- `internal/task/entity.go` — `Task` struct (ID int, Description string, Completed bool, CreatedAt time.Time, CompletedAt *time.Time).
- `internal/task/repository.go` — `Repository` interface (Add, List, GetByID, MarkCompleted, Delete, Save, Load).
- `internal/storage/jsonrepo.go` — JSON-file implementation of `Repository`; use an injected `Clock` interface and `FileSystem` interface for testability.
- `internal/cli/` — one file for each subcommand (`add.go`, `list.go`, `done.go`, `rm.go`). Export `Run(args []string, repo task.Repository, out io.Writer, errOut io.Writer) int` from each file.

Limit every file to 500 lines (AGENTS.md §2.1). Inject all collaborators through constructors. These collaborators include Repository, Clock, FS, and writers.

### 2.4. ID & Timestamp Semantics
Assign IDs as integers. The repository shall assign each ID as `max(existing IDs) + 1`. Use `1` when the store is empty. Do not reindex or reuse IDs after `rm`. Use `max(remaining IDs) + 1` for the next `add`. Record `created_at` with an injected `Clock` in RFC3339-UTC format with a `Z` suffix. Set `completed_at` on the first `done`. Make later `done` calls idempotent. Perform no operation, return exit code 0, print the same confirmation message, and do NOT set a new `completed_at` value.

### 2.5. List Output Format (byte-level)
- Use this exact header row: `ID  Status  Description  Created At\n` (two-space separators).
- Use this data row format: `<id>  [< >|<x>]  <description>  <YYYY-MM-DD HH:MM>\n`. Format `Created At` in **local time** as `2006-01-02 15:04`. Print descriptions verbatim. Do not truncate descriptions.
- For an empty list, print `No tasks found.\n` and exit with code 0.

### 2.6. Exit Codes & Error Message Format
- For a missing ID in `done` or `rm`, print `todo: task not found: <id>` to `stderr`. Exit with code 1.
- For a non-integer `<id>` or a missing argument in `done`, `rm`, or `add`, print `todo: missing task id` (or `todo: invalid id: <input>` for a non-integer). Exit with code 2.
- For `add` with no arguments, print `todo: missing task description` to `stderr`. Exit with code 2.
- Handle file and directory errors as specified in §2.2.

---

## 3. Technical Constraints
*   **Language:** Use Go (module path `github.com/noctifab/todo-cli`, Go 1.22+). This requirement is mandatory for AGENTS.md tooling (go.mod, Makefile, golangci-lint, BDD).
*   **Dependencies:** Use only the Go standard library for CLI parsing and JSON I/O. You may implement a subcommand parser by inspecting `os.Args[1]`. Do not add third-party CLI libraries. You may use test-only dependencies, such as `github.com/cucumber/godog` for BDD, only in `*_test.go` files.
*   **Storage Format:** Use a raw JSON file.

---

## 4. Verification Criteria & Testing

### 4.1. Expected Tests
*   **Unit Tests** (under `internal/.../*_test.go`): Test task addition, completion, and deletion functions in isolation with fake repositories. Test JSON serialization and deserialization with an in-memory `FileSystem` and a fake `Clock` that returns a fixed time.
*   **Integration Tests** (under `tests/`): Execute CLI commands as subprocesses. Build the binary with `go build -o /tmp/todo ./cmd/todo`. Run the binary in a temporary directory. Check exit codes and exact stdout and stderr output. Verify that the application reads tasks from and writes tasks to the `todo.json` file.
*   **BDD acceptance tests** (under `tests/acceptance/`): Use godog with `when`/`it` style as required by AGENTS.md §2.3.

### 4.2. Out of Scope
Do NOT use the AGENTS.md `docker compose` Postgres stack for this project. State this requirement explicitly to prevent an agent from starting the stack.

---

## 5. Definition of Done (DoD)

Consider `todo-cli` fully implemented only when the Go CLI application satisfies all these requirements:
1. **Public Binary:** The `todo-cli` executable shall parse the `add`, `list`, `complete`, and `delete` subcommands. It shall persist items to a JSON file store. It shall return exit code `0` after success.
2. **Linting Invariant:** `go vet ./...` shall produce zero warnings. `go fmt ./...` shall format the source without changes.
3. **Verification Criteria:** `go test ./...` shall pass all tests.
