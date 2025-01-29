## 1. System Overview

Apidae is a distributed task queue system built in Go, designed to provide reliable task processing with progressive complexity. The system emphasises developer experience, operational excellence, and maintainable code while teaching fundamental distributed systems concepts.

### 1.1 Core Objectives

- Create a reliable distributed task processing system
- Support horizontal scaling through distributed workers
- Ensure task persistence and system resilience
- Provide clear monitoring and observability
- Maintain simple, idiomatic Go interfaces

### 1.2 Key Technical Decisions

- **Language**: Go for both client and server components
- **Communication**: gRPC for inter-component communication
- **Storage**: PostgreSQL for task persistence
- **Architecture**: Distributed workers with leader election
- **Monitoring**: Built-in metrics and health endpoints

## 2. Core Components

### 2.1 Task Management

```go
type Task struct {
    ID        string
    Type      string
    Payload   []byte
    Status    TaskStatus
    CreatedAt time.Time
    Timeout   time.Duration
    Metadata  map[string]string
}

type TaskStatus string

const (
    TaskStatusPending   TaskStatus = "pending"
    TaskStatusRunning   TaskStatus = "running"
    TaskStatusComplete  TaskStatus = "complete"
    TaskStatusFailed    TaskStatus = "failed"
    TaskStatusCancelled TaskStatus = "cancelled"
)
```

### 2.2 Client Interface

```go
type Client interface {
    // Core operations
    EnqueueTask(ctx context.Context, task Task) (string, error)
    GetTaskStatus(ctx context.Context, taskID string) (TaskStatus, error)
    CancelTask(ctx context.Context, taskID string) error
    
    // Batch operations
    EnqueueBatch(ctx context.Context, tasks []Task) ([]string, error)
    
    // Monitoring
    GetQueueMetrics(ctx context.Context) (*QueueMetrics, error)
}
```

### 2.3 Worker Interface

```go
type Worker interface {
    // Handler registration
    RegisterHandler(taskType string, handler TaskHandler)
    
    // Lifecycle management
    Start() error
    Stop() error
    
    // Health reporting
    Heartbeat() error
}

type TaskHandler func(ctx context.Context, payload []byte) error
```

## 3. Implementation Phases

### Phase 1: Core Foundation (Weeks 1-2)

#### Objectives:
- Implement basic task queue with in-memory storage
- Create worker pool with simple task distribution
- Establish core interfaces and type systems
- Set up basic project structure and testing framework

#### Key Components:
```go
// Queue implementation
type Queue struct {
    tasks   chan Task
    workers map[string]*Worker
    store   TaskStore
    mu      sync.RWMutex
}

// Basic task store interface
type TaskStore interface {
    SaveTask(ctx context.Context, task Task) error
    GetTask(ctx context.Context, taskID string) (Task, error)
    UpdateTaskStatus(ctx context.Context, taskID string, status TaskStatus) error
}
```

### Phase 2: Reliability Layer (Weeks 3-4)

#### Objectives:
- Implement PostgreSQL persistence
- Add retry mechanism with backoff
- Implement worker health checks
- Add basic error handling and recovery

#### Key Components:
```go
// Postgres implementation
type PostgresStore struct {
    db *sql.DB
}

// Retry policy
type RetryPolicy struct {
    MaxAttempts int
    BackoffFunc func(attempt int) time.Duration
}
```

### Phase 3: Operational Features (Weeks 5-6)

#### Objectives:
- Add metrics collection and exposure
- Implement health check endpoints
- Create dead letter queue
- Add basic task routing

#### Key Components:
```go
// Metrics
type QueueMetrics struct {
    TasksEnqueued    int64
    TasksCompleted   int64
    TasksFailed      int64
    ActiveWorkers    int
    QueueLength      int
    ProcessingTime   time.Duration
    ErrorRate        float64
}

// Dead letter queue
type DeadLetterQueue struct {
    store    TaskStore
    maxRetry int
}
```

### Phase 4: Enhancements (Weeks 7-8)

#### Objectives:
- Implement task batching
- Add CLI tools for management
- Enhance error handling
- Implement basic task scheduling

#### Key Components:
```go
// Batch processing
type BatchConfig struct {
    MaxSize    int
    MaxWait    time.Duration
    BatchType  string
}

// CLI tool structure
type CLI struct {
    client  Client
    printer OutputPrinter
}
```

## 4. Technical Requirements

### 4.1 Performance Targets
- Task enqueue latency: < 100ms (p95)
- Task processing start time: < 500ms (p95)
- Worker registration time: < 1s
- System can handle 1000 tasks/second per worker node
- Maximum task payload size: 1MB

### 4.2 Reliability Requirements
- No single point of failure in worker system
- Tasks must be persisted before acknowledgment
- Failed tasks retry up to 3 times by default
- System must recover from worker node failures
- Task status must be accurately tracked

### 4.3 Operational Requirements
- Expose Prometheus metrics
- Health check endpoints
- Graceful shutdown support
- Task inspection capabilities
- Basic CLI for system management

## 5. Future Considerations

### 5.1 Planned Future Features
- Geographic task routing
- Task time travel debugging
- Workflow integration
- Predictive analytics
- Complex scheduling
- Task simulation
- Web UI dashboard

### 5.2 Scalability Considerations
- Support for multiple queue partitions
- Distributed persistence layer
- Worker auto-scaling
- Task prioritization
- Rate limiting per task type

## 6. Development Guidelines

### 6.1 Code Structure
```
apidae/
├── cmd/
│   ├── apidaed/       # Server binary
│   └── apidae/        # CLI tool
├── internal/
│   ├── queue/         # Core queue implementation
│   ├── worker/        # Worker implementation
│   ├── storage/       # Storage implementations
│   └── metrics/       # Metrics collection
├── pkg/
│   ├── client/        # Client library
│   └── types/         # Shared types
└── api/
    └── proto/         # Protocol definitions
```

### 6.2 Testing Strategy
- Unit tests for all packages
- Integration tests for storage layer
- End-to-end tests for complete workflows
- Chaos testing for distributed components
- Performance benchmarks for critical paths

### 6.3 Best Practices
- Use context for cancellation and timeouts
- Implement graceful shutdown everywhere
- Log structured data for observability
- Use defensive programming for edge cases
- Document all public APIs
- Follow Go best practices and idioms

## 7. Appendix

### 7.1 Example Usage

```go
// Client usage example
func ExampleUsage() {
    client := apidae.NewClient(apidae.ClientConfig{
        Addr: "localhost:8080",
    })
    
    task := apidae.Task{
        Type: "email",
        Payload: []byte(`{"to": "user@example.com", "subject": "Hello"}`),
        Timeout: 5 * time.Minute,
    }
    
    taskID, err := client.EnqueueTask(context.Background(), task)
    if err != nil {
        log.Fatal(err)
    }
    
    status, err := client.GetTaskStatus(context.Background(), taskID)
    if err != nil {
        log.Fatal(err)
    }
    
    fmt.Printf("Task %s status: %s\n", taskID, status)
}

// Worker usage example
func ExampleWorker() {
    worker := apidae.NewWorker(apidae.WorkerConfig{
        ServerAddr: "localhost:8080",
        ID:        "worker-1",
    })
    
    worker.RegisterHandler("email", func(ctx context.Context, payload []byte) error {
        var email EmailTask
        if err := json.Unmarshal(payload, &email); err != nil {
            return err
        }
        return sendEmail(ctx, email)
    })
    
    if err := worker.Start(); err != nil {
        log.Fatal(err)
    }
    defer worker.Stop()
}
```

### 7.2 Database Schema

```sql
-- Core tables
CREATE TABLE tasks (
    id            TEXT PRIMARY KEY,
    type          TEXT NOT NULL,
    payload       BYTEA,
    status        TEXT NOT NULL,
    created_at    TIMESTAMP NOT NULL,
    started_at    TIMESTAMP,
    completed_at  TIMESTAMP,
    timeout       INTERVAL,
    retries       INT DEFAULT 0,
    error         TEXT,
    worker_id     TEXT
);

CREATE TABLE workers (
    id            TEXT PRIMARY KEY,
    status        TEXT NOT NULL,
    last_seen     TIMESTAMP NOT NULL,
    task_count    INT DEFAULT 0
);

-- Indexes
CREATE INDEX idx_tasks_status ON tasks(status);
CREATE INDEX idx_tasks_type ON tasks(type);
CREATE INDEX idx_workers_status ON workers(status);
```