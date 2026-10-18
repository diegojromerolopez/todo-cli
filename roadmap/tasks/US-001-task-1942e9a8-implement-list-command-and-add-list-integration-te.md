# Task: Implement list command and add-list integration tests

- **ID**: `task-1942e9a8`
- **Story ID**: `US-001`
- **Status**: `PENDING`
- **Change Type**: `FEATURE`
- **Depends On**: `US-001-TASK-001`, `US-001-TASK-002`
- **Target Files**: `internal/cli/list.go`, `internal/cli/list_test.go`, `tests/add_list_integration_test.go`, `cmd/todo/main.go`

## Description

Create internal/cli/list.go implementing the 'todo list' command using the Repository interface from task US-001-TASK-001. Print header 'ID  Status  Description  Created At\n' followed by one data row per task: '<id>  [< >|<x>]  <description>  <YYYY-MM-DD HH:MM>\n' using two-space separators, with CreatedAt rendered in local time as '2006-01-02 15:04'. If no tasks exist, print 'No tasks found.\n' and return exit code 0. Accept the --file flag before or after the list keyword. Update cmd/todo/main.go to dispatch the 'list' subcommand to this handler. Write internal/cli/list_test.go injecting a fake Repository with canned task data, asserting exact stdout strings including the empty case and exit code 0. Write tests/add_list_integration_test.go that builds the real binary with 'go build -o /tmp/todo ./cmd/todo', runs it in a temp directory, and asserts: add "Buy milk" prints 'Added task: "Buy milk" (ID: 1)' with exit code 0; list prints the exact header plus data row; list on an empty directory prints 'No tasks found.' with exit code 0; todo.json exists and parses back to one task; add with quotes and spaces in the description produces byte-exact output and exit code 0.
