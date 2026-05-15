# Modular Boundaries

## Purpose

Define the bounded contexts of the EngraphSW platform, the ownership rules of each module, and the contracts governing inter-module communication.

This document establishes the modular architecture that the implementation must follow.

It does not define implementation detail — it defines boundaries, responsibilities, and communication rules.

---

## Architectural Foundation

The platform is a modular monolith organized into bounded contexts.

See [ADR-001 — Modular Monolith Architecture](../adr/001-modular-monolith.md).

Inter-module communication occurs through domain events and explicit public interfaces.

See [ADR-004 — Event-Driven Communication](../adr/004-event-driven-communication.md).

No module may import the internal logic of another module.

Modules expose only their public interface. Internal implementation is private to the module.

---

## Module Map

```
modules/
├── workspace/       — workspace lifecycle and canvas session management
├── graph/           — graph operations, edge management, traversal
├── nodes/           — node registry, plugin system, node lifecycle
├── execution/       — node execution engine, state, history
├── traceability/    — impact analysis, dependency index, traversal
├── ai/              — provider abstraction, prompt templates, AI workflows
├── collaboration/   — realtime presence and multi-user editing
├── versioning/      — graph versioning, snapshots, history
├── requirements/    — functional requirements artifact domain
├── estimation/      — function point analysis, size estimation
├── user-stories/    — user story artifact domain
├── personas/        — persona artifact domain
├── stakeholders/    — stakeholder artifact domain
├── risk/            — risk analysis artifact domain
└── shared/          — value objects, shared types, domain primitives
```

**Anticipated future module:** As multi-node execution flows, propagation coordination, and execution planning mature, the `execution/` module may need to extract a dedicated `orchestration/` or `workflow/` bounded context responsible for scheduling, execution planning, and multi-step coordination. This is not in scope for current phases. The boundary is noted here to prevent premature scope expansion of `execution/` and to anticipate the extraction point.

---

## Module Definitions

### workspace

**Owns:**
- workspace entity lifecycle (create, open, close, delete)
- canvas session state
- workspace-level metadata and configuration
- multi-workspace navigation state

**Does not own:**
- graph structure (owned by `graph`)
- node instances (owned by `nodes`)
- collaboration presence (owned by `collaboration`)

---

### graph

**Owns:**
- graph entity lifecycle
- edge creation, deletion, and validation
- graph-level traversal operations
- graph topology queries
- adjacency state management

**Does not own:**
- node domain logic (owned by `nodes`)
- traceability index (owned by `traceability`)
- workspace context (owned by `workspace`)
- specific artifact semantics (owned by artifact domain modules)

---

### nodes

**Owns:**
- node type registry and plugin contracts
- node instance lifecycle (create, update, delete)
- node schema and metadata validation
- node input and output contracts
- node visual renderer registration

**Does not own:**
- node execution (owned by `execution`)
- graph topology (owned by `graph`)
- specific artifact content (owned by artifact domain modules)

---

### execution

**Owns:**
- node execution engine
- execution state machine (idle, running, success, failed, stale)
- execution queue management
- execution history and audit records
- async execution lifecycle (retry, cancellation, progress)

**Does not own:**
- node type definitions (owned by `nodes`)
- AI provider calls (owned by `ai`)
- propagation semantics (owned by `traceability`)

---

### traceability

**Owns:**
- traceability index (edge-based dependency map)
- upstream and downstream traversal
- impact analysis and staleness propagation
- traceability metadata persistence
- consistency and coverage analysis

**Does not own:**
- edge storage (owned by `graph`)
- execution triggering (owned by `execution`)
- specific artifact meaning (owned by artifact domain modules)

---

### ai

**Owns:**
- AI provider abstraction and registration
- provider request routing and fallback
- prompt template management
- response normalization
- token usage and latency tracking
- AI request audit logging

**Does not own:**
- domain-specific AI logic (owned by the invoking module)
- prompt content for engineering operations (owned by relevant domain module)
- graph execution orchestration (owned by `execution`)

See [ADR-005 — AI Provider Abstraction](../adr/005-ai-provider-abstraction.md).

---

### collaboration

**Owns:**
- realtime presence state
- multi-user cursor and selection state
- collaborative editing conflict resolution
- live awareness metadata

**Does not own:**
- workspace persistence (owned by `workspace`)
- graph mutation authority (owned by `graph`)

---

### versioning

**Owns:**
- graph snapshot lifecycle
- version history and metadata
- snapshot comparison and diffing
- version restoration operations

**Does not own:**
- live graph state (owned by `graph`)
- node execution history (owned by `execution`)

---

### requirements

**Owns:**
- functional requirement artifact schema and lifecycle
- requirement-specific validation and AI capabilities
- requirement node renderer contract

**Does not own:**
- graph topology (owned by `graph`)
- node plugin registration (owned by `nodes`)
- traceability index (owned by `traceability`)

---

### estimation

**Owns:**
- function point analysis logic
- estimation artifact schema and lifecycle
- estimation-specific AI capabilities
- estimation recalculation semantics

---

### user-stories

**Owns:**
- user story artifact schema and lifecycle
- acceptance criteria modeling
- user story-specific AI capabilities

---

### personas

**Owns:**
- persona artifact schema and lifecycle
- persona attribute modeling
- persona-specific AI capabilities

---

### stakeholders

**Owns:**
- stakeholder artifact schema and lifecycle
- stakeholder relationship modeling
- stakeholder-specific AI capabilities

---

### risk

**Owns:**
- risk analysis artifact schema and lifecycle
- risk scoring and severity modeling
- risk-specific AI capabilities

---

### shared

**Owns:**
- domain primitive value objects (e.g., `NodeId`, `EdgeId`, `WorkspaceId`)
- shared type contracts used across module boundaries
- shared validation primitives

**Does not own:**
- business logic of any kind
- cross-module orchestration
- utility functions without clear domain ownership

`shared` must remain minimal. It is not a general-purpose utility folder.

---

## Dependency Rules

### Permitted Dependencies

Modules may depend on:

- `shared` — all modules may import shared primitives
- `ai` — any module may invoke the AI abstraction
- `nodes` — artifact domain modules depend on node contracts
- `graph` — `traceability`, `execution`, `versioning`, and `collaboration` depend on graph operations
- `execution` — `traceability` depends on execution state for staleness propagation

### Prohibited Dependencies

- Artifact domain modules must not depend on each other
- `graph` must not depend on any artifact domain module
- `nodes` must not depend on `execution`
- `ai` must not depend on any domain or artifact module
- `shared` must not depend on any other module

### Visualization

```
shared          ← depended on by all
ai              ← depended on by domain modules and execution
nodes           ← depended on by artifact domain modules
graph           ← depended on by traceability, execution, versioning, collaboration
execution       ← depended on by traceability (for staleness)
workspace       ← depended on by collaboration
```

Circular dependencies between modules are never permitted.

---

## Application Services

Each module exposes its operational behavior through a layered service model:

**Domain services** encapsulate internal domain behavior. They are private to the module and must not be imported by other modules.

**Application services** expose the module's public operational interface. They are the only synchronous entry point visible outside the module boundary. External modules and request handlers interact with a module exclusively through its application services.

Cross-module orchestration that requires synchronous coordination must occur through application service calls. Cross-module notification that is fire-and-observe must occur through domain events.

Repositories, internal domain services, and domain model internals must remain private to the module that owns them. They must never be imported or invoked from outside the module boundary.

---

## Inter-Module Communication

Modules communicate across boundaries through:

1. **Domain events** — the primary mechanism for cross-module notification
2. **Application services** — the only synchronous operational interface visible outside a module boundary

Modules must not call each other's internal repositories, domain logic, or private services directly.

Example events:

- `NodeCreatedEvent` — published by `nodes`, consumed by `graph`, `traceability`, `execution`
- `EdgeConnectedEvent` — published by `graph`, consumed by `traceability`
- `NodeExecutionCompletedEvent` — published by `execution`, consumed by `traceability`, `nodes`
- `RequirementUpdatedEvent` — published by `requirements`, consumed by `traceability`, `execution`

### Event Ownership

A domain event is owned by the module that publishes it.

The publishing module defines:

- the event schema
- the event semantics
- the event lifecycle guarantees

Consumer modules may react to events but must not redefine their meaning, re-emit them with altered payloads, or treat them as internal implementation signals of the publishing module.

Event contracts are part of the publishing module's public interface. They must be treated with the same stability discipline as application service contracts.

Breaking changes to an event schema require explicit review and versioning. Silent schema changes to published events are an architectural violation.

---

## Shared State Constraints

Modules must not share mutable internal state directly.

State that appears to be shared across modules must be owned by exactly one module. Other modules observe it through events or query it through application services.

Shared state transitions must occur through:

- public application services (synchronous mutation with clear ownership)
- domain events (observable state change notifications)
- explicit persistence boundaries (one module owns the persistent record)

Hidden mutable shared state across module boundaries — such as a shared in-memory store, a shared cache with write access from multiple modules, or a shared singleton mutated by multiple contexts — is an architectural violation.

This constraint is especially critical for the graph runtime, where concurrent mutations to node state, edge state, and execution state could produce undefined behavior if ownership boundaries are not respected.

---

## Cross-Cutting Concerns

Infrastructure concerns that span all modules — such as logging, telemetry, observability, authentication, and metrics — must be treated as infrastructure, not as domain logic.

Cross-cutting systems:

- must not carry business logic
- must not become hidden shared domain layers
- must not couple modules to each other through a shared infrastructure side channel
- must not violate bounded context ownership

Authentication and authorization context may be passed through request context at the application layer. Modules must not reach into a shared auth singleton or bypass request-scoped context.

Logging and telemetry instrumentation may be applied uniformly at the infrastructure level without requiring each module to depend on a shared logging module.

Cross-cutting concerns that begin accumulating domain logic are a signal that a new bounded context is forming and should be explicitly recognized and modeled.

---

## Boundary Enforcement Principles

- Module internal directories are private by convention
- Only explicitly exported interfaces constitute the module's public contract
- Cross-module imports of internal paths are architectural violations
- New cross-module dependencies require orchestrator review before implementation
- Bounded context ownership must be clear for every domain concept in the system

When a domain concept cannot be clearly assigned to a single module, this indicates a missing bounded context or a boundary that needs refinement. Surface this rather than distributing ownership informally.
