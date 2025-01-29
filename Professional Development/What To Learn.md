# What To Learn

## Questions

### Concurrency & Goroutines

**Go's Scheduler and Goroutines**
- How does Go's scheduler work?
- How are goroutines scheduled, and how do they differ from OS threads?

**Managing Goroutines**
- What are patterns for managing and canceling goroutines?
- How to use `context.Context` for handling timeouts and cancellations.

**Error Handling in Concurrency**
- How to handle errors and panics in concurrent systems?
- What is the idiomatic way to pass errors between goroutines?

### Synchronisation & Communication

**Channels vs. Traditional Synchronization**
- How do channels compare to traditional synchronization mechanisms (e.g., mutexes)?
- What are the trade-offs in different use cases?

**Backpressure & Slow Consumers in Pub/Sub Systems**
- How to handle backpressure and slow consumers?
- Explore techniques like buffering, rate limiting, and retry policies.

**Message Partitioning & Scaling**
- How to implement message partitioning and scaling?
- Investigate strategies for load distribution and horizontal scaling in pub/sub systems.

### Networking & Protocols

**HTTP/2 and HTTP/3 in Go**
- How does Go handle HTTP/2 and HTTP/3 (QUIC)?
- Differences between these protocols and their implementation in Golang's `net/http` package.

**REST vs. gRPC**
- What are the differences between REST and gRPC?
- How do performance, payload format, and use-case differ between the two?

### Building & Optimizing HTTP Servers

**Building Robust & Scalable HTTP Servers**
- How do you build robust and scalable HTTP servers in Go?
- Best practices for security, logging, request validation, and response structuring.
- Explore tools like `pprof`, `wrk`, and `hey` for profiling and load testing.

**Securing HTTP APIs in Go**
- How to secure HTTP APIs in Go?
- Authentication and authorization mechanisms: JWT, OAuth2, and API keys.

### Messaging Systems & Patterns

**Comparison of Popular Messaging Systems**
- What are the differences between Kafka, NATS, and RabbitMQ?
- How do they handle persistence, ordering, and durability?

**Message Delivery Guarantees**
- How does message delivery guarantee work (at-least-once, at-most-once, exactly-once)?
- What are the trade-offs for each in different scenarios?

### Databases & Data Management

**SQL vs. NoSQL Databases**
- What are the differences between SQL and NoSQL in terms of performance, consistency, and scalability?

**Interacting with Databases in Go**
- How does Golang interact with different databases?
- Explore `gorm` and alternatives; understand benefits and trade-offs.

**Database Transactions & Consistency**
- How to handle database transactions and consistency?
- Implement retry mechanisms, handle deadlocks, and use context for controlling transaction lifecycles.

**Schema Design for Performance & Flexibility**
- How to design database schemas for performance and flexibility?
- Explore indexing strategies, normalization vs. denormalization, and optimizing read vs. write operations.

### Caching Strategies & Performance

**In-memory Caching in Go**
- How to use in-memory caching effectively?
- When to use caching, what data to cache, and cache expiration strategies.

**Trade-offs Between Caching Strategies**
- What are the trade-offs between different caching strategies (e.g., LRU, LFU, TTL)?
- How do these strategies affect performance?

**Distributed Caching**
- How does distributed caching work, and when should it be used?
- Explore tools like Redis, Memcached, or a custom solution.

### Performance Profiling & Optimization

**Profiling & Optimizing Go Code**
- How do you profile and optimize Go code for performance?
- Use tools like `pprof` to profile CPU and memory usage, and identify and fix bottlenecks.

### Testing & Test Structures

**Unit Testing in Go**
- What is the best way to structure unit tests in Go?
- Alternatives to Go's `testing` package and their differences.
- Explore ways to structure unit, integration, and e2e tests using `testing`.
- How to test HTTP and gRPC endpoints.

**Mocking & Stubbing in Tests**
- Are there alternative ways to handle mocking and stubbing in Go?

**Integration & End-to-End Testing**
- What is the best way to structure and write integration and e2e tests?
- How to test the entire system and simulate real-world conditions.

### Security & Best Practices

**Common Security Vulnerabilities in Go Applications**
- What are the common security vulnerabilities in Go applications?
- Learn about SQL injection, XSS, CSRF, and mitigation techniques.

**Managing Secrets & Environment Variables**
- What is the best way to handle secrets and environment variables?
- Learn about securely storing and accessing environment variables and secrets.
