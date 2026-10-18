# Task: Implement add command

- **ID**: `task-52e55d65`
- **Story ID**: `US-001`
- **Status**: `PENDING`
- **Change Type**: `FEATURE`
- **Depends On**: `US-001-TASK-001`
- **Target Files**: `internal/cli/add.go`, `internal/cli/add_test.go`, `cmd/todo/main.go`

## Description

Create internal/cli/add.go implementing the 'todo add "<description>"' command using the Repository interface from task US-001-TASK-001. On success print 'Added task: "<task description>" (ID: <id>)' to stdout and return exit code 0. The repository assigns CreatedAt from the injected Clock in RFC3339 UTC format with Z suffix. On empty or missing description, write 'todo: missing task description' to stderr, return exit code 2, and perform no file write. Accept the --file flag before or after the add keyword and pass it through to repository path resolution. Update cmd/todo/main.go to dispatch the 'add' subcommand to this handler, keeping main.go under 15 lines by delegating to a testable run function. Write internal/cli/add_test.go injecting a fake Repository returning canned data, and assert exact stdout text and exit codes for success, empty description, and missing description cases.
