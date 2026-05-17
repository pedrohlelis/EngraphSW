# PHASE 2 — SYSTEM ARCHITECTURE

## Objective

Translate the Phase 1B specification into a working project foundation.

Phase 1B produced complete architectural specifications, interface contracts, schema designs, and module conventions. Phase 2 instantiates those specifications as a runnable skeleton — no UI, no business logic, but a fully wired infrastructure foundation that Phase 3 and Phase 4 build on top of.

When Phase 2 is complete, the repository contains a running Next.js application with a connected database, a correct module structure, and typed skeletons for the node registry and event system.

---

## What Phase 1B Already Delivered (not re-done here)

- Architecture design and interface contracts (`node-execution-model.md`, `canvas-engine.md`, `traceability-model.md`)
- Logical schema design (`initial-schema.md`)
- Module structure conventions (`module-conventions.md`)
- Technology decisions with pinned versions (`tech-stack.md`, ADR-006, ADR-007)
- Domain field schemas and edge type mappings (all six domain documents)

Phase 2 does not redesign these. It implements them.

---

## Constraints

- No production UI — no React components, no canvas, no styling beyond Next.js baseline config
- No authentication
- No AI execution logic
- No concrete node types registered in the node registry
- Stubs must be correctly typed — no `any` escapes, no placeholder interfaces

---

## Out of Scope (covered in later phases)

- Canvas and React Flow integration (Phase 3)
- Concrete node type implementations (Phase 4)
- Authentication and multi-tenancy
- AI provider execution
- Real-time collaboration (Liveblocks)
- Advanced graph query and traversal operations

---

## Work Streams

### Stream 1 — Project Scaffold

*Prerequisite. Complete before all other streams.*

Create the Next.js application using the exact versions pinned in `docs/architecture/tech-stack.md`.

Deliverables:
- `package.json` with all pinned dependencies from `tech-stack.md`
- `tsconfig.json` with strict TypeScript configuration
- Next.js app directory structure
- ESLint + Prettier configured to project conventions
- `.env.example` defining all required environment variables (DATABASE_URL, REDIS_URL, etc.)
- `GET /api/health` route returning 200 — the canary that proves the scaffold runs

---

### Stream 2 — Prisma Schema + Database

*Unblocked after Stream 1. May run in parallel with Stream 3.*

Translate `docs/architecture/initial-schema.md` into an actual Prisma schema and verify database connectivity.

Deliverables:
- `prisma/schema.prisma` — graph layer (workspaces, graphs, nodes, edges), execution layer (execution_logs, staleness_records), outbox table, soft deletion fields — directly from `initial-schema.md`
- Initial migration generated and applied
- Prisma client generated and importable
- Seed script scaffolded (structure only — no seed data)

---

### Stream 3 — Module Skeleton

*Unblocked after Stream 1. May run in parallel with Stream 2.*

Create the module directory structure exactly as defined in `docs/architecture/module-conventions.md`.

Deliverables — one directory per bounded context, each containing the canonical file set:

| Module | Path |
|--------|------|
| Graph | `modules/graph/` |
| Nodes | `modules/nodes/` |
| Workspace | `modules/workspace/` |
| Execution | `modules/execution/` |
| Traceability | `modules/traceability/` |
| AI | `modules/ai/` |

Each module must contain: `index.ts` (barrel), `types.ts`, `events.ts`, `service.ts` (stub), `repository.ts` (stub where applicable).

No implementation logic — correctly structured, correctly typed stubs only.

---

### Stream 4 — Event System Foundation

*Unblocked after Stream 3.*

Define all typed event interfaces and wire the BullMQ event bus skeleton.

Deliverables:
- `shared/events/` — typed event interfaces for all events implied by Phase 1B domain and architecture docs:
  - `NodeCreatedEvent`, `NodeExecutedEvent`, `NodeStaleEvent`, `NodeFailedEvent`
  - `DependencyInvalidatedEvent`
  - `RequirementCreatedEvent`, `ArtifactVersionCreatedEvent`
  - Base `DomainEvent` interface (id, type, occurredAt, payload)
- BullMQ queue configuration connected to Redis
- Event bus service stub: `publish()` and `subscribe()` signatures typed and wired — no handlers yet

---

### Stream 5 — Node Registry Foundation

*Unblocked after Stream 3.*

Wire the `NodeDefinition` interface into a functional (not stubbed) registry service.

Deliverables:
- `NodeDefinition` TypeScript interface fully instantiated from the contract in `docs/architecture/node-execution-model.md`
- `NodeRegistry` service: `register()`, `getByType()`, `listAll()` — implemented, not stubbed
- Zero concrete node types registered — the registry is empty but functional
- Compile-time test: a `NodeDefinition` fixture that exercises the full interface shape

---

### Stream 6 — Core Graph Persistence

*Unblocked after Streams 2 and 3.*

Implement the graph module's data access layer against the Prisma schema.

Deliverables:
- `modules/graph/repository.ts` — CRUD for workspace, graph, node, edge via Prisma (data access only, no business logic)
- `modules/graph/service.ts` — thin service layer delegating to repository, typed return values
- Integration test skeleton: database connection verified, one happy-path create + read test per entity type

---

### Stream 7 — Phase Gate

*Execute last.*

- `CURRENT_PHASE.md` updated to Phase 2 at phase start
- All success criteria verified before advancing
- Update to Phase 3 only after explicit user approval

---

## Execution Sequence

```
Stream 1 (Scaffold)
    |
    +-- Stream 2 (Prisma + DB)     \
    |                               +-- Stream 6 (Graph Persistence)
    +-- Stream 3 (Module Skeleton) /
            |
            +-- Stream 4 (Event System)
            |
            +-- Stream 5 (Node Registry)
                                        \
                                         +-- Stream 7 (Phase Gate)
```

---

## Success Criteria

- [ ] `npm run dev` starts without errors
- [ ] `npm run build` succeeds with zero TypeScript errors
- [ ] `GET /api/health` returns 200
- [ ] `prisma migrate dev` (or `db push`) completes without error
- [ ] All module directories exist with the canonical file set per `module-conventions.md`
- [ ] `NodeRegistry.register()` accepts a conforming `NodeDefinition` without TypeScript error
- [ ] `NodeRegistry.getByType()` returns the registered definition
- [ ] Graph repository creates and reads a workspace, graph, node, and edge through the service layer
- [ ] BullMQ queue connects to Redis without error
- [ ] All event interfaces in `shared/events/` compile without error

---

## Related Documents

* [Phase 1B — Specification Hardening](PHASE_1B_SPECIFICATION.md) — the specification phase whose outputs this phase implements
* [Phase 3 — Canvas Engine](PHASE_3_CANVAS_ENGINE.md) — next sequential phase, builds React Flow UI on this foundation
* [Phase 4 — Node Framework](PHASE_4_NODE_FRAMEWORK.md) — implements concrete node types against the NodeRegistry built here
* [Tech Stack](../architecture/tech-stack.md) — pinned versions for Stream 1
* [Module Conventions](../architecture/module-conventions.md) — structure guide for Stream 3
* [Initial Schema](../architecture/initial-schema.md) — design document translated in Stream 2
* [Node Execution Model](../architecture/node-execution-model.md) — NodeDefinition contract implemented in Stream 5
