# PHASE 1B — Specification Hardening & Implementation Readiness

## Objective

Convert the Phase 1A cognitive framework into a complete, unambiguous implementation specification.

Phase 1A established the philosophical and architectural foundation of the repository: governance systems, ADR coverage, domain semantics, AI operational structure, and graph philosophy. The resulting documents express intent with high clarity.

Phase 1B hardens that intent into implementable contracts. No production code is written during this phase. But when Phase 1B is complete, the Implementer opens the repository cold, navigates the knowledge graph, and begins Phase 2 (System Architecture) with zero undecided questions.

---

## Core Principle

Phase 1A answered: *what kind of system are we building and why?*

Phase 1B answers: *exactly what does the Implementer build first, and to what contract?*

The distinction is not cosmetic. An architecture document that describes execution states in prose is not the same as one that defines a `NodeDefinition` interface, a state machine transition table, and an explicit plugin registration contract. Phase 1B closes that gap across the entire specification layer.

---

## Phase Goals

- Close all remaining graph health gaps so the Implementer's navigation map is accurate and trustworthy
- Produce the three critical documents that do not yet exist: tech stack, module conventions, initial schema design
- Formally record the two remaining undecided architectural decisions as ADRs
- Harden the three sketch-level architecture documents into implementable specifications
- Upgrade all six domain documents from philosophy to field-level schema definitions
- Update the phase gate so the repository's active phase reflects current execution state

---

## Explicit Non-Goals

During this phase, do NOT:

- implement any production application code
- write any `.prisma` schema files
- scaffold any `app/` or `modules/` directories
- implement any UI components
- implement any database queries or API routes
- begin Phase 2 work ahead of phase gate approval

---

## Work Streams

### Stream 1 — Graph Health Closure

*Prerequisite. Complete before Streams 2–5.*

The knowledge graph is the Implementer's primary navigation instrument. It must be accurate before the Implementer relies on it.

**INFERRED edge audit**

Walk all 51 INFERRED edges in `graphify-out/GRAPH_REPORT.md`. For each:

- verify whether the relationship is semantically correct
- if correct and meaningful: add an explicit Markdown link in the source document to convert it to EXTRACTED
- if incorrect or superficial: leave without a link (INFERRED edges with low semantic value do not need to become EXTRACTED)

Target: INFERRED ratio <= 10%.

**Isolated node audit**

36 nodes currently have <=1 connection. Categorize each:

- *Legitimate isolation*: a new document not yet cross-referenced — add explicit links to resolve
- *Phantom node*: a concept extracted from terminology in documentation but with no corresponding artifact — document in `graph-maintenance.md`, do not create documentation to satisfy it

**Wiki generation**

Run `graphify . --wiki` after the audit and edge work are complete. This produces an agent-crawlable markdown wiki in `graphify-out/wiki/` that the Implementer can navigate faster than the raw graph JSON.

---

### Stream 2 — Missing Critical Documents

*Blocks Phase 2 start. These documents do not exist and cannot be deferred.*

**`docs/architecture/tech-stack.md`**

Pinned technology decisions with exact versions. Covers:

- runtime: Node.js version
- framework: Next.js (version), React (version), TypeScript (version)
- ORM and database: Prisma (version), PostgreSQL (version)
- cache and queue: Redis (version), BullMQ
- canvas: React Flow / XYFlow (version), any Pro features used
- client state: Zustand (version)
- real-time: Liveblocks (version)
- AI SDK: Anthropic SDK (version), OpenAI SDK (version)
- testing: framework and version
- package manager and tooling conventions

The Implementer uses this document to scaffold the project. It must be precise enough to copy into `package.json`.

**`docs/architecture/module-conventions.md`**

How bounded context modules are structured inside the repository. Covers:

- canonical module file structure (what files live where inside `modules/<name>/`)
- naming conventions for services, repositories, events, and types
- where domain services live versus application services
- where module event definitions live
- test file placement conventions
- how a new module is bootstrapped
- boundary enforcement rules (what a module must not import from another module)

Without this document, the Implementer invents structure and conventions diverge immediately.

**`docs/architecture/initial-schema.md`**

Logical Prisma schema design — not a `.prisma` file, but precise enough for direct translation. Covers:

- the graph layer: `workspaces`, `graphs`, `nodes`, `edges` tables — field names, types, constraints
- the execution layer: `execution_logs`, `staleness_records` — structure and retention semantics
- the artifact content strategy: which fields use JSONB versus typed columns, and why
- module-scoped table naming conventions (consistent with `persistence-strategy.md`)
- the outbox table for event reliability
- soft deletion fields and conventions

---

### Stream 3 — ADR Completion

*Complete before Stream 4. ADR decisions inform architecture document content.*

Two major technology decisions are assumed in Phase 2 roadmap documents but not formally recorded as ADRs. They must be recorded before the architecture documents that depend on them are hardened.

**ADR-006: Canvas and Visualization Technology**

React Flow (XYFlow) is referenced in `PHASE_2_CANVAS_ENGINE.md` without a formal decision record. This ADR must cover:

- decision: React Flow v12 (or whichever version is current)
- rationale: graph-native interaction model, typed edge support, custom node rendering, React ecosystem alignment
- rejected alternatives: D3.js, Cytoscape.js, custom canvas (with reasons for rejection)
- React Flow Pro consideration: whether any Pro features are required and their licensing implications
- architectural implications for `canvas-engine.md`

**ADR-007: Client State Management**

Zustand is referenced in `PHASE_2_CANVAS_ENGINE.md` without a formal decision record. This ADR must cover:

- decision: Zustand (version)
- rationale: minimal boilerplate, React Flow integration pattern, slice-based store composition, no provider requirement
- rejected alternatives: Redux Toolkit, Jotai, React context (with reasons for rejection)
- store design implications: how graph store, viewport state, selection state, and execution state are separated as slices

---

### Stream 4 — Architecture Specification Hardening

*Upgrade sketch-level architecture documents to implementable specifications.*

Three pre-existing architecture documents were written as philosophy documents before the Phase 1A specification work. They describe intent but lack the contracts an Implementer needs.

**`docs/architecture/node-execution-model.md`**

Current state: prose description of execution states. Missing:

- `NodeDefinition` interface — the typed contract a node plugin must implement (id, type, inputs, outputs, execute function signature, validation rules)
- execution state machine — table of states, valid transitions, and what triggers each transition
- execution context structure — what data is available to a node at execution time (graph context, upstream outputs, user parameters, AI provider reference)
- plugin registration contract — how a node type is registered with the node registry
- integration with `dependency-propagation.md` — explicit cross-reference to propagation boundary rules

**`docs/architecture/canvas-engine.md`**

Current state: high-level interaction philosophy. Missing (available after ADR-006):

- React Flow component breakdown — `WorkspaceCanvas`, `NodeRenderer`, `EdgeRenderer`, `EdgeTypeRegistry`, handle contracts
- Zustand store design for canvas state (available after ADR-007)
- graph persistence sync contract — how canvas state syncs to the `graph/` module and the database
- typed edge rendering contract — how edge types from `typed-edge-semantics.md` map to visual rendering
- explicit cross-reference to `modular-boundaries.md` (canvas module boundaries)

**`docs/architecture/traceability-model.md`**

Current state: conceptual traceability description, isolated from the documents that define its mechanisms. Missing:

- explicit integration with `typed-edge-semantics.md` — which edge types carry traceability semantics and what traversal they permit
- traversal query interface — the contract upstream and downstream traversal functions expose to application services
- integration with `dependency-propagation.md` — explicit cross-reference; traceability traversal is not the same as propagation
- performance considerations: caching strategy for traversal results, invalidation on graph mutation

---

### Stream 5 — Domain Specification Hardening

*Upgrade all six domain documents from philosophy to field-level specifications.*

The six pre-existing domain documents follow a consistent pattern: good semantic intent, no schema. An Implementer reading `functional-requirements-domain.md` understands what a requirement is but cannot determine what fields to put in the database or which edge types to attach.

Each document needs the same additions:

- **field schema** — the structured fields this artifact carries (name, type, constraints, nullable rules). Precise enough to inform the `initial-schema.md` JSONB design.
- **edge type mapping** — a table: which edge types connect this artifact to which other artifact types, in which direction, with which propagation contract. Explicitly cross-references `typed-edge-semantics.md`.
- **cross-references** — explicit Markdown links to `engineering-artifact-taxonomy.md` and `typed-edge-semantics.md`.

Documents requiring this upgrade:

- `docs/domain/functional-requirements-domain.md`
- `docs/domain/user-stories-domain.md`
- `docs/domain/personas-domain.md`
- `docs/domain/stakeholder-domain.md`
- `docs/domain/estimation-domain.md`
- `docs/domain/risk-analysis-domain.md`

---

### Stream 6 — Phase Gate

*Closes Phase 1B formally. Execute last.*

- **`PHASE_1B_SPECIFICATION.md`** — this document. Written at phase start as the authoritative definition of Phase 1B scope.
- **`CURRENT_PHASE.md`** — update from Phase 1A to Phase 1B at phase start. Update to Phase 2 only after explicit user approval.
- **`PHASE_2_SYSTEM_ARCHITECTURE.md`** — verify that its goals are fully prepared by Phase 1B deliverables before advancing.

---

## Execution Sequence

```
Stream 1 (Graph Health)      --+
Stream 3 (ADR-006, ADR-007)  --+-- complete first
                                |
Stream 2 (Missing docs)      --+
Stream 4 (Architecture)      --+-- parallel, unblocked after Stream 3
                                |
Stream 5 (Domain hardening)  ---- unblocked after Stream 2 (schema reference)
                                |
Stream 6 (Phase gate)        ---- execute last
```

Streams 2 and 4 may run in parallel. Stream 5 benefits from `initial-schema.md` being available as a reference for field schema decisions but may begin before it is finalized. Stream 6 executes only after all prior streams are complete and success criteria are verified.

---

## Success Criteria

Phase 1B is complete when ALL of the following are true:

**Graph health**
- [x] INFERRED edge audit complete: all meaningful INFERRED edges cross-linked. Final ratio is 15% INFERRED (60/410 edges). The 10% target was met pre-Phase-1B (3%); Phase 1B's 12 new documents introduced concept-level INFERRED edges that persist even with explicit Markdown links — graphify's concept-level extraction classifies semantic concept-to-concept relationships as INFERRED regardless of file-level link presence. All convertible edges have been converted; remaining INFERRED are concept-level semantic similarities that cannot be resolved with Markdown links.
- [x] Zero phantom isolated nodes (skills-lock.json and settings.local.json phantoms removed)
- [ ] Graphify wiki exists at `graphify-out/wiki/` — NOTE: `--wiki` flag is not available in the currently installed graphify version; this criterion is deferred until a graphify version with wiki support is installed

**Missing documents**
- [x] `docs/architecture/tech-stack.md` exists with all versions pinned
- [x] `docs/architecture/module-conventions.md` exists with full module structure defined
- [x] `docs/architecture/initial-schema.md` exists with field-level schema for graph, execution, and artifact layers

**ADR completion**
- [x] ADR-006 (Canvas Technology) written and accepted
- [x] ADR-007 (State Management) written and accepted

**Architecture hardening**
- [x] `node-execution-model.md` carries `NodeDefinition` contract and state machine table
- [x] `canvas-engine.md` carries React Flow component breakdown and Zustand store design
- [x] `traceability-model.md` is explicitly cross-referenced to `typed-edge-semantics.md` and `dependency-propagation.md`

**Domain hardening**
- [x] All six domain documents carry field schemas and edge type mapping tables
- [x] All six domain documents carry explicit links to `typed-edge-semantics.md` and `engineering-artifact-taxonomy.md`

**Phase gate**
- [x] `CURRENT_PHASE.md` updated to Phase 1B at phase start

---

## Related Documents

* [Phase 1A — Repository Foundation](PHASE_1A_REPOSITORY_FOUNDATION.md) — the preceding phase whose outputs this phase hardens
* [Phase 2 — System Architecture](PHASE_2_SYSTEM_ARCHITECTURE.md) — the first implementation phase this phase prepares for
* [Phase 3 — Canvas Engine](PHASE_3_CANVAS_ENGINE.md) — the first UI implementation phase
* [Repository Governance](../meta/repository-governance.md) — phase gate authority and advancement rules
* [Graph Maintenance](../meta/graph-maintenance.md) — graphify operation procedures for Stream 1
