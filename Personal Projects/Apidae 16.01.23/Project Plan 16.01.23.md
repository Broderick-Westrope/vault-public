## Why build a Distributed Task Queue System?

1. **Natural Progressive Complexity**
- I can start simple with an in-memory queue and single worker
- Gradually add complexity with distributed workers, persistence, retry logic
- Each feature teaches fundamental distributed systems concepts
- I can lean on my Temporal experience for insight while building from scratch

2. **Practical Learning Impact**
- Teaches core distributed systems patterns I'll use throughout my career
- Forces me to think about:
  - How to handle node failures
  - Data persistence and recovery
  - Task scheduling algorithms
  - System monitoring and debugging
  - Scalability concerns
- These are exactly the kinds of problems senior engineers need to solve

3. **Career Growth Alignment**
- Builds directly on my Go and microservices experience
- Very relevant to my current work with military C2 systems where reliability is crucial
- Great talking point for senior positions - I can discuss concrete examples of handling distributed system challenges
- The skills transfer well to many other systems (message queues, job processors, workflow engines)

## MVP Core Requirements

Trying to be achievable and educational while maintaining enough complexity to be worthwhile.

**General Technical Considerations:**
- Use gRPC for communication between components
- Use database transactions to ensure task assignment atomicity
- Implement basic leader election for worker coordination
- Include reasonable timeouts and circuit breaking
- Support graceful shutdown of workers

### Basic Task Management

- Single task queue with FIFO ordering
- Tasks must be uniquely identifiable
- Tasks should support:
  - A task type/name (e.g., "send_email", "process_image")
  - Payload data (serialised task parameters)
  - Basic metadata (creation time, status)
  - Task timeouts
- Simple retry policy for failed tasks (e.g., max 3 retries)

### Distributed Worker System

- Support for multiple worker nodes
- Workers should be able to:
  - Join/leave the system dynamically
  - Pick up tasks from the queue
  - Report task completion/failure
- Basic worker health checking
- Prevent multiple workers from processing the same task

### Persistence

- Tasks must survive system restarts
- Use a single Postgres instance for storage
- Store both task data and task state
- Basic task history/audit log

### Simple Client Interface

Go client library that supports:
  ```go
  // Example API structure
  type Task struct {
      Type    string
      Payload []byte
      Timeout time.Duration
  }

  type Client interface {
      // Enqueue a new task
      EnqueueTask(ctx context.Context, task Task) (string, error)
      
      // Get task status
      GetTaskStatus(ctx context.Context, taskID string) (TaskStatus, error)
      
      // Cancel a task
      CancelTask(ctx context.Context, taskID string) error
  }
  ```
  
Basic worker framework:
  ```go
  type Worker interface {
      // Register a handler for a task type
      RegisterHandler(taskType string, handler func(ctx context.Context, payload []byte) error)
      
      // Start processing tasks
      Start() error
      
      // Graceful shutdown
      Stop() error
  }
  ```

### Basic Monitoring

- Simple metrics for:
  - Queue length
  - Task processing times
  - Success/failure rates
  - Worker count
- HTTP endpoint exposing basic system status

### Out of Scope

- Multiple queues/priority queues
- Complex retry policies
- Task scheduling (delayed tasks)
- Task dependencies
- Distributed persistence
- Web UI
- Advanced monitoring
- Task routing/affinity
- Rate limiting
- Dead letter queues

## Existing Systems to Learn From

**Key Features to Notice in All:**
- Task persistence strategies
- Worker management
- Error handling
- Monitoring capabilities
- Scaling patterns

### Temporal

- Core built in Go with SDKs in multiple languages
- I've used this before, but worth studying deeper
- Key features to notice:
  - Durable execution guarantees
  - Complex workflow orchestration
  - Versioning for long-running tasks
  - Built-in retry policies

**Strengths:**
- Powerful workflow engine
- Strong consistency guarantees
- Excellent scalability
- Rich operational features

**Weaknesses:**
- Complex to set up
- Heavy infrastructure requirements
- Steep learning curve
- Overkill for simple tasks

### Machinery

- Built with Go. Closer to what my MVP might look like
- Features to study:
  - Simple task definition
  - Multiple backends (Redis, Postgres, MongoDB)
  - Basic retry mechanism
  - Worker groups

**Strengths:**
- Simple, Go-native API
- Multiple backend support
- Lightweight and approachable

**Weaknesses:**
- Limited failure handling
- Basic retry mechanism only
- No workflow concepts
- Lacks operational tools
- State management can be fragile

### Bull

- Built with NodeJS. Notable for it's design
- Notable features:
  - Priority queues
  - Job scheduling
  - Progress tracking
  - Rate limiting

**Strengths:**
- Excellent developer experience
- Rich feature set
- Great UI/monitoring
- Queue patterns (priority, rate limiting)

**Weaknesses:**
- Node.js only
- Redis-dependent
- No complex workflows
- Limited to single-node Redis

### Other Notable Mentions

- Celery. Industry standard for Python.
- Apache Kafka. Not exactly a task queue, but relevant for their partitioning strategies, consumer group patterns, and message persistence.

## Opportunities For Improvement

1. **Developer Experience Focus**
	- Provide type-safe task definitions
	- Leverage Go's concurrency patterns
	- Bull and Sidekiq code tools for debugging task timelines and manually manipulating tasks. Eg: inspect a tasks state, simulate execution of another task, and control a task (pause, resume, step).
2. **Operational Excellence** (?)
	- Rich task context with tracing and monitoring
	- Built-in health checks and circuit breaking
	- Apache Airflow and Temporal provide advanced scheduling features. Eg: CRON-like scheduling with timezone support, and rate limiting per task.
	- Take inspiration from Kafka and RabbitMQ for their version task definitions, circuit breaker with custom strategies, and DLQ handling.
1. **Smart Defaults, Progressive Complexity**
	- Use functional options with sane defaults to make configuration optional.
2. **Unique Features**
	- Built-in task visualization/debugging
	- Local development mode with time travel
	- Automatic task payload versioning
	- Smart batching/coalescing of similar tasks
	- Predictive scaling based on queue patterns
	- Geographic task routing
	- Task simulation/replay capabilities
