# Task: Implement completion and deletion repository operations

- **ID**: `US-002-TASK-001`
- **Story ID**: `US-002`
- **Status**: `PENDING`
- **Change Type**: `FEATURE`
- **Depends On**: `US-001-TASK-001`
- **Target Files**: `internal/task/repository.go`, `internal/task/repository_test.go`

## Description

Extend Repository with MarkCompleted and Delete operations. Preserve injected clock usage and existing JSON persistence behavior. Set CompletedAt once in UTC RFC3339 format. Keep remaining task IDs unchanged after deletion. Return task.ErrNotFound for unknown IDs. Add co-located unit tests for completion, idempotency, deletion, timestamps, and errors.
