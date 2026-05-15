# Module Conventions

## Related Documents

* [ADR-001 Modular Monolith Architecture](../adr/001-modular-monolith.md) — the architectural decision this document operationalizes
* [ADR-004 Event-Driven Communication](../adr/004-event-driven-communication.md) — event contract rules
* [Architecture: Modular Boundaries](modular-boundaries.md) — module ownership, dependency rules, and communication contracts
* [Architecture: Tech Stack](tech-stack.md) — technology decisions that inform naming and tooling conventions

---

## Purpose

This document defines how bounded context modules are structured inside the repository. It establishes the canonical file layout, naming rules, and bootstrapping process that every module must follow.

Without this document, modules will diverge immediately. With it, the Implementer can open any module and know exactly where everything lives.

---

## Canonical Module File Structure

Every module under `modules/` follows this exact directory layout:

```
modules/<name>/
  index.ts                  — public interface (only file importable by other modules)
  types.ts                  — domain types, value objects, and interfaces for this module
  events.ts                 — domain event definitions published by this module
  domain/
    <Entity>.ts             — domain model classes
    <Entity>Service.ts      — domain services (private to module)
    <Entity>Repository.ts   — repository interface (port) definition
  application/
    <Action>UseCase.ts      — application service / use case
  infrastructure/
    <Entity>PrismaRepository.ts   — Prisma repository implementation (adapter)
    <Queue>.queue.ts              — BullMQ queue definitions (if module owns a queue)
    <Worker>.worker.ts            — BullMQ worker implementations (if module owns a worker)
  __tests__/
    <Entity>Service.test.ts       — unit tests for domain services
    <Action>UseCase.test.ts       — integration tests for use cases
```

**The `index.ts` rule is absolute.** Other modules may only import from `modules/<name>/index.ts`. Imports of internal paths (`modules/graph/domain/GraphRepository.ts`) are boundary violations.

---

## File Naming Conventions

### Domain Layer

| Artifact | Naming Pattern | Example |
|---|---|---|
| Domain entity | `PascalCase.ts` | `Graph.ts`, `Node.ts` |
| Domain service | `<Entity>Service.ts` | `GraphService.ts` |
| Repository interface | `<Entity>Repository.ts` | `NodeRepository.ts` |
| Value object | `<Name>.ts` | `NodeId.ts`, `EdgeType.ts` |

### Application Layer

| Artifact | Naming Pattern | Example |
|---|---|---|
| Use case / application service | `<Action><Entity>UseCase.ts` | `CreateNodeUseCase.ts`, `ExecuteNodeUseCase.ts` |
| Input DTO | `<Action><Entity>Input.ts` | `CreateNodeInput.ts` |
| Output DTO | `<Action><Entity>Output.ts` | `CreateNodeOutput.ts` |

### Infrastructure Layer

| Artifact | Naming Pattern | Example |
|---|---|---|
| Prisma repository | `<Entity>PrismaRepository.ts` | `NodePrismaRepository.ts` |
| BullMQ queue | `<Purpose>.queue.ts` | `execution.queue.ts` |
| BullMQ worker | `<Purpose>.worker.ts` | `execution.worker.ts` |

### Events

| Artifact | Naming Pattern | Example |
|---|---|---|
| Event class | `<Entity><Past-tense>Event.ts` | `NodeCreatedEvent`, `ExecutionCompletedEvent` |
| Event file | `events.ts` | `modules/nodes/events.ts` |

Event names follow `<Entity><Verb>Event` with the verb in past tense (the event describes something that already happened). All events for a module live in a single `events.ts` file at the module root.

### Types

| Artifact | Naming Pattern | Example |
|---|---|---|
| Domain interface | `I<Name>` | `INodeRepository`, `INodeService` |
| DTO type | `<Name>Dto` | `NodeDto`, `EdgeDto` |
| Enum | `PascalCase` | `NodeStatus`, `EdgeType` |

---

## Application Services vs Domain Services

This distinction is critical and must be maintained consistently.

### Domain Services

Domain services encapsulate business logic that belongs to the domain but does not fit naturally on a single entity.

Rules:
- Live in `domain/`
- Are private to the module — never exported via `index.ts`
- Accept domain model objects (entities, value objects) as parameters
- Have no knowledge of HTTP, queues, Prisma, or infrastructure
- May call repository interfaces (ports), but not implementations
- May publish domain events

Example: `GraphService` computes whether adding an edge would create a cycle. It knows about `Graph` and `Edge` domain types. It does not know about Prisma or HTTP.

### Application Services (Use Cases)

Application services coordinate the module's response to an external request. They are the module's public operational interface.

Rules:
- Live in `application/`
- Are exported via `index.ts` as part of the module's public interface
- Accept and return plain DTOs or primitive types (not domain model internals)
- Orchestrate: they call domain services, repository implementations, and emit events
- May call the `ai/` module's application service for AI-assisted operations
- Must not contain business logic — delegate to domain services and repositories

Example: `CreateNodeUseCase` receives a `CreateNodeInput` DTO, calls `NodeService` to validate the new node, persists it via `NodePrismaRepository`, publishes `NodeCreatedEvent`, and returns `CreateNodeOutput`.

---

## Event Definitions

All domain events published by a module are defined in `modules/<name>/events.ts`.

Events must:
- Be named in past tense (`NodeCreatedEvent`, not `CreateNodeEvent`)
- Carry only the data needed by consumers — no internal domain model objects
- Be treated as public contracts — breaking changes require review
- Be exported via `index.ts` so consuming modules can subscribe

Event fields use plain serializable types (string, number, Date, primitive arrays). Never embed domain model class instances in an event payload.

Example:

```typescript
export class NodeCreatedEvent {
  constructor(
    public readonly nodeId: string,
    public readonly graphId: string,
    public readonly nodeType: string,
    public readonly createdAt: Date,
  ) {}
}
```

---

## Test File Placement

| Test type | Location | Runner |
|---|---|---|
| Unit tests for domain services | `modules/<name>/__tests__/<Entity>Service.test.ts` | Vitest |
| Integration tests for use cases | `modules/<name>/__tests__/<Action>UseCase.test.ts` | Vitest |
| API route handler tests | `app/api/**/__tests__/*.test.ts` | Vitest |
| End-to-end tests | `e2e/*.spec.ts` | Playwright |

Unit tests mock repositories. Integration tests use a real test database (see [Tech Stack](tech-stack.md) — no mock databases in integration tests).

Test files follow the `<subject>.test.ts` naming pattern. Never `<subject>.spec.ts` (reserved for Playwright E2E).

---

## The `shared` Module

The `shared` module is not a general utility folder. It holds only domain primitive types that are meaningful across bounded contexts.

```
modules/shared/
  index.ts
  types.ts         — branded ID types, core enums used across boundaries
  domain/
    NodeId.ts
    EdgeId.ts
    WorkspaceId.ts
    GraphId.ts
```

Nothing with business logic belongs in `shared`. If logic is needed across modules, the correct answer is to identify which module owns the concept and invoke it through that module's application service.

---

## Bootstrapping a New Module

To add a new module:

1. Create `modules/<name>/` directory
2. Create `index.ts` — initially empty, populated as public interface grows
3. Create `types.ts` — module-specific type definitions
4. Create `events.ts` — initially empty; populate as events are defined
5. Create `domain/`, `application/`, `infrastructure/`, `__tests__/` directories
6. Register any new domain events in the module's `events.ts`
7. Declare the module's ownership in [Modular Boundaries](modular-boundaries.md)
8. Add Prisma models for the module following the naming conventions in [Initial Schema](initial-schema.md)

Do not create a module unless its bounded context is defined in [Modular Boundaries](modular-boundaries.md). Unauthorized modules are architectural violations.

---

## Boundary Enforcement Rules

### What a module MUST export via `index.ts`

- Application service classes (use cases)
- Event classes (for consumers to subscribe)
- Public DTO types
- Public interface types (if consumed by other modules)

### What a module must NEVER export

- Domain service classes
- Repository implementations
- Internal domain model classes
- Prisma model types
- Module-internal utilities

### What other modules MUST NOT do

- Import from any path inside a module other than its `index.ts`
- Directly call a module's Prisma queries
- Directly mutate another module's domain state
- Subscribe to another module's internal events (only publicly exported events)

### Dependency direction

```
artifact modules (requirements, estimation, user-stories, personas, stakeholders, risk)
  -> nodes (for node plugin contracts)
  -> ai (for AI-assisted operations)
  -> shared (for domain primitives)

graph
  -> shared

execution
  -> nodes
  -> ai
  -> graph
  -> shared

traceability
  -> graph
  -> execution (for staleness state)
  -> shared

all modules
  -> shared
```

No circular dependencies. No upward dependencies (artifact modules must not be imported by `graph`, `nodes`, or `execution`).

---

## Directory Layout Summary

```
modules/
  workspace/      index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  graph/          index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  nodes/          index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  execution/      index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  traceability/   index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  ai/             index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  collaboration/  index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  versioning/     index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  requirements/   index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  estimation/     index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  user-stories/   index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  personas/       index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  stakeholders/   index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  risk/           index.ts, types.ts, events.ts, domain/, application/, infrastructure/, __tests__/
  shared/         index.ts, types.ts, domain/
```
