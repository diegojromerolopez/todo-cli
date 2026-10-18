# Task: Implement done and rm command handlers

- **ID**: `US-002-TASK-002`
- **Story ID**: `US-002`
- **Status**: `PENDING`
- **Change Type**: `FEATURE`
- **Depends On**: `US-001-TASK-001`, `US-002-TASK-001`
- **Target Files**: `internal/cli/done.go`, `internal/cli/rm.go`, `internal/cli/done_test.go`, `internal/cli/rm_test.go`, `tests/done_rm_integration_test.go`

## Description

Implement injected done and rm handlers for the binary CLI. Parse one integer ID and preserve --file and TODO_FILE behavior. Emit exact success and error messages with required exit codes. Keep done idempotent without changing CompletedAt. Add fake-repository handler tests and temporary-file integration tests for completion, deletion, ID gaps, invalid IDs, missing IDs, and repeated completion.
