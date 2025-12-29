# Data consistency strategies


Because services don’t share databases, strong consistency across services is avoided. Instead, systems choose among the following:

### 1) Eventual Consistency

Each service updates its own data.

Other services learn about changes via events.

Temporary inconsistencies are acceptable.

Example:
Order Service → emits OrderCreated → Inventory & Billing update asynchronously.

### 2) Saga Pattern (multi-step workflows)

Coordinates a business transaction across services.

Types

Choreography: services react to events (no central controller).

Orchestration: a coordinator tells each service what to do.

Compensating actions undo work if a later step fails.

### 3) Two-Phase Commit (2PC)

Strong consistency across services.

Tight coupling, poor resilience, blocks on failure.

Generally avoided in cloud-native systems.


## Communication strategies

Services communicate synchronously, asynchronously, or both.

### 1) Synchronous (request/response)

**HTTP/REST** or **gRPC**

Simple mental model

Tighter coupling and latency sensitivity

Use when: immediate response is required (e.g., read queries).

### 2) Asynchronous (event-driven)

Message brokers (Kafka, RabbitMQ, cloud pub/sub)

Loose coupling, resilient, scalable

Requires idempotency and eventual consistency handling

Use when: workflows, integrations, fan-out updates.



## Rule of thumb

- Prefer async + eventual consistency

- Use sync calls only when necessary

- Model failures explicitly

- Design for retries and duplicates

- Keep data ownership strict