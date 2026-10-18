# Task: Walking skeleton: task model, repository contract, JSON storage

- **ID**: `task-0abcf7ae`
- **Story ID**: `US-001`
- **Status**: `PENDING`
- **Change Type**: `FEATURE`
- **Depends On**: `[]` 
- **Target Files**: `go.mod`, `cmd/todo/main.go`, `internal/task/entity.go`, `internal/task/repository.go`, `internal/storage/jsonrepo.go`, `internal/task/entity_test.go`, `internal/storage/jsonrepo_test.go`, `Makefile`

## Description

Create go.mod for module github.com/noctifab/todo-cli (Go 1.22+). Write internal/task/entity.go with Task struct (ID int, Description string, Completed bool, CreatedAt time.Time, CompletedAt *time.Time) and a NewTask constructor that assigns the next id and sets Completed=false, CompletedAt=nil. Write internal/task/repository.go with the Repository interface (Add, List, GetByID, MarkCompleted, Delete, Save, Load) and an ErrNotFound sentinel error. Write internal/storage/jsonrepo.go implementing Repository using JSON file storage. Inject a Clock interface (default returns time.Now().UTC()) and a FileSystem interface for file reads and writes to allow fake implementations in tests. Resolve the file path using this order: --file flag, then TODO_FILE env var, then ./todo.json. Treat an absent file as empty. On malformed JSON content, Load must return an error that the caller can map to exit code 1 and stderr message 'todo: corrupt file: <path>'. Auto-increment IDs as max(existing IDs)+1 starting at 1, safe for concurrent Add calls. Write cmd/todo/main.go as a thin shell under 15 lines that wires the JSON repository, os.Stdin, os.Stdout, os.Args and delegates to an internal run function (to be extended by later tasks) returning an exit code. Write Makefile with a test target running 'go test -v ./...'. Write internal/task/entity_test.go verifying NewTask id assignment and default fields. Write internal/storage/jsonrepo_test.go using an in-memory FileSystem and a fake fixed Clock: verify Add then List round-trips a task verbatim, file contents match expected JSON, and concurrent Add calls from an absent file produce ids 1, 2, 3. Keep each file under 500 lines.
