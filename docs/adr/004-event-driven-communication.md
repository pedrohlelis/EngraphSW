# ADR-004 — Event-Driven Internal Communication

## Status

Accepted

---

# Context

The platform contains multiple interconnected bounded contexts including:
- graph
- nodes
- execution
- AI
- traceability
- estimation
- collaboration

Operations within one module frequently affect other modules.

Examples include:
- node updates invalidating downstream calculations
- requirement changes triggering estimation recalculation
- graph changes affecting traceability
- AI generation creating new artifacts

Direct synchronous coupling between modules would create:
- fragile dependencies
- cascading complexity
- reduced modularity
- difficult future extraction

The platform requires a scalable mechanism for:
- dependency propagation
- execution invalidation
- graph consistency
- modular coordination

---

# Decision

Internal module communication should primarily use event-driven patterns.

Modules should communicate through:
- domain events
- application events
- explicit event contracts

Events should:
- represent meaningful domain actions
- remain strongly typed
- avoid infrastructure leakage
- preserve bounded context isolation

Examples:
- RequirementCreatedEvent
- RequirementUpdatedEvent
- NodeConnectedEvent
- NodeExecutionCompletedEvent
- ArtifactVersionCreatedEvent

Events should support:
- dependency propagation
- recalculation triggers
- traceability updates
- AI workflow initiation

---

# Consequences

## Positive

- Reduced coupling
- Better modularity
- Easier future service extraction
- Improved extensibility
- Cleaner execution propagation
- Better observability

## Negative

- Increased architectural complexity
- Event lifecycle coordination overhead
- More difficult debugging
- Requires careful event governance

---

# Alternatives Considered

## Direct Module Coupling

Rejected because:
- creates tight dependencies
- weakens modular boundaries
- harms extensibility

## Shared Service Layer Only

Rejected because:
- insufficient decoupling
- weak propagation semantics
- poor scalability for graph events

## Global State Orchestration

Rejected because:
- difficult to scale cleanly
- weak domain isolation
- high coupling risk

---

# Long-Term Implications

The event system may later evolve into:
- async execution orchestration
- distributed event pipelines
- realtime propagation systems
- execution observability infrastructure

The architecture should therefore maintain:
- explicit event contracts
- event traceability
- strong typing
- propagation consistency