# Persistence Strategy

## Purpose

Define how the EngraphSW platform persists its graph, artifacts, execution state, events, and versioning data.

This document establishes the persistence architecture — the data layer that makes the graph durable, consistent, and queryable.

It does not define schema implementation or Prisma model details.

---

## Technology Foundation

| Concern | Technology | Role |
|---|---|---|
| Primary database | PostgreSQL | System of record for all durable state |
| ORM | Prisma | Schema definition, migrations, query interface |
| Ephemeral storage | Redis | Background job queues, transient caching |
| Realtime layer | Liveblocks | Collaboration presence state (external, not PostgreSQL) |

PostgreSQL is the single source of durable truth for the platform.

Redis is ephemeral operational infrastructure. It is not the system of record. If Redis state is lost, the system recovers from PostgreSQL.

Liveblocks manages realtime presence state externally. The platform does not own or persist collaboration presence in its database.

### Why PostgreSQL for a Graph Platform

The platform intentionally uses a relational database rather than a native graph database.

Rationale:

- The graph data in this system is structured and bounded, not unstructured or schema-free
- PostgreSQL's ACID guarantees are essential for graph consistency during concurrent modifications
- Prisma provides a maintainable schema and migration strategy
- Operational simplicity: one database engine, one operational concern
- PostgreSQL's JSONB support handles artifact content flexibility without schema proliferation
- Native graph databases (Neo4j, etc.) introduce significant operational overhead without sufficient benefit at current scale

This decision should be revisited if graph traversal performance becomes a scaling constraint at significant data volumes.

---

## Persistence Philosophy

The graph is the system of record.

See [ADR-003 — Graph as System of Record](../adr/003-graph-system-of-record.md).

This means:

- Nodes and edges are first-class persistent entities
- Graph relationships are not secondary metadata — they are durable domain records
- The persistent graph must be fully reconstructable from the database without relying on derived state
- Every node, every edge, and every relationship carries a stable, persistent identity

Persistence serves the graph. The schema must reflect this priority.

A persistence design that treats nodes as generic records and edges as join table rows misses the point — edges carry semantic meaning and must be persisted as typed domain entities.

---

## Graph Persistence Model

The core graph persistence model is an **adjacency list stored in a relational schema**.

### Nodes

Each node is a persistent record with:

- a stable unique identifier
- workspace association
- node type (the registered plugin type)
- artifact content (JSONB — flexible per node type)
- core metadata (created, updated, creator identity)
- execution state (idle, running, success, failed, stale)
- soft-deletion marker

Node identity is immutable. A node's ID never changes after creation.

Node type is immutable. A node's semantic type does not change after creation.

Artifact content evolves through versioned updates. Prior content is preserved in execution and versioning history.

### Edges

Each edge is a persistent record with:

- a stable unique identifier
- workspace association
- source node reference
- target node reference
- edge type (typed semantic relationship — see typed edge semantics)
- edge metadata (direction hints, propagation boundary declarations, custom attributes)
- created timestamp

Edges are directional. Source and target are distinct roles.

Edge type is immutable after creation. Changing the semantic meaning of a relationship requires deleting the edge and creating a new one with the correct type.

### Graph Consistency

The persistence layer is responsible for referential integrity between nodes and edges.

An edge must not reference a node that does not exist.

Node deletion must handle associated edges explicitly — either cascade-soft-deleting edges or enforcing edge removal before node removal.

Cycle detection occurs at the application layer before write; the persistence layer enforces referential integrity after.

---

## Artifact Content Storage

Engineering artifact content (the domain-specific fields of a requirement, persona, WBS package, etc.) is stored as JSONB within the node record.

This approach:

- avoids a proliferation of artifact-type-specific tables
- allows each artifact type to evolve its schema without database migrations
- keeps the core graph persistence model uniform across all node types

### Tradeoffs

**Flexibility**: JSONB content can evolve without schema changes.

**Queryability**: Core fields that require filtering or indexing (type, workspace, execution state) are typed columns. Fields that are artifact-specific and rarely queried are JSONB.

**Validation**: Content schema validation is enforced at the application layer through node type contracts, not at the database layer.

This tradeoff is correct for the current stage. If specific artifact fields require frequent complex queries at scale, those fields should be promoted to typed columns through explicit migration.

---

## Module-Scoped Persistence

Each bounded context owns its persistent data.

No module may write to tables owned by another module.

No module may read from another module's tables except through that module's application service interface.

### Schema Organization

The platform uses a single Prisma schema with table ownership enforced by naming convention.

Tables are prefixed by module ownership:

```
graph_*         — graph module tables
node_*          — nodes module tables
exec_*          — execution module tables
trace_*         — traceability module tables
collab_*        — collaboration module tables
version_*       — versioning module tables
ws_*            — workspace module tables
req_*           — requirements module tables
story_*         — user-stories module tables
persona_*       — personas module tables
stakeholder_*   — stakeholders module tables
risk_*          — risk module tables
wbs_*           — WBS module tables
est_*           — estimation module tables
ai_*            — AI module tables
event_outbox_*  — event outbox tables
```

This naming convention makes module ownership visible in the schema and prevents accidental cross-module table access.

### Cross-Module Data Assembly

When a response requires data from multiple modules (e.g., a node with its artifact content and execution state), the assembly happens at the application layer — not through cross-module database joins.

Each module fetches its own data through its own repositories.

The application layer combines the results.

This preserves module independence at the persistence level.

---

## Event Persistence — Outbox Pattern

The platform uses the **transactional outbox pattern** for reliable event publishing.

### Problem Addressed

Domain events (e.g., `RequirementUpdatedEvent`) must be published after a database write. Without the outbox pattern, a process failure between the DB write and event publication produces a silent inconsistency — the data changed but no event was fired.

### Outbox Mechanics

When a module performs a state change that produces a domain event:

1. The domain change and the event record are written to the database **in the same transaction**
2. A background worker polls the outbox table for unprocessed events
3. The worker publishes each event to the internal event bus
4. Successfully published events are marked as processed in the outbox

If the process fails between step 1 and step 3, the unprocessed event remains in the outbox and will be retried on next polling cycle.

### Outbox Record

Each outbox record captures:

- unique event ID
- owning module
- event type
- event payload (typed)
- created timestamp
- processed timestamp (null until published)
- retry count and failure metadata

### Idempotency

Event consumers must be designed to handle duplicate delivery.

The outbox guarantees at-least-once delivery, not exactly-once. Consumers must be idempotent.

---

## Execution State Persistence

Node execution state is durable. It is not held in memory only.

### Execution State

The current execution state of each node (idle, running, success, failed, stale) is persisted as a column on the node record.

State transitions update this column within a transaction.

Staleness state must be persisted — a node that is stale when a session closes must remain stale when the session resumes.

See [dependency-propagation.md](dependency-propagation.md) — Staleness Persistence.

### Execution History

Each node maintains an append-only execution history.

Each history record captures:

- the trigger (user action, system event, batch ID)
- the input state at time of execution (snapshot of relevant upstream context)
- the output produced
- the execution result (success, failed, cancelled)
- timestamps and latency

Execution history records are never modified or deleted.

They form the auditable execution lineage of the node.

---

## Versioning and Snapshot Persistence

The `versioning` module persists graph snapshots as explicit durable records.

### Snapshot Model

A snapshot captures the state of a graph (or subgraph) at a specific point in time.

A snapshot record captures:

- unique snapshot ID
- workspace association
- the serialized graph state (node set + edge set + content) at snapshot time
- version label
- snapshot timestamp
- author identity
- snapshot type (manual, automatic, pre-execution, milestone)

### Snapshot Storage

Snapshots are stored as structured records, not as opaque binary blobs.

The snapshot payload must be queryable enough to support:

- snapshot comparison (diff between two versions)
- selective restoration (restoring a subset of a snapshot)
- traceability history (what did artifact X look like at version Y)

Snapshots are append-only. They are never modified after creation.

---

## Ephemeral State

### Redis

Redis is used exclusively for:

- **BullMQ job queues** — background execution jobs, async AI operations, batch recalculation scheduling
- **Transient caching** — short-lived cache of computed values where performance justifies it

Redis state is not authoritative. Any Redis state that is lost is either recomputed or recovered from PostgreSQL.

Background jobs that are lost from Redis queues should be recoverable by replaying from the execution history or by re-triggering the relevant operation.

Redis must never become a hidden persistence layer for state that should live in PostgreSQL.

### Liveblocks

Liveblocks manages realtime collaboration presence externally.

The platform does not store presence state in PostgreSQL.

Presence state (who is viewing, cursors, live selections) is ephemeral by design.

---

## Artifact Retention

Engineering artifacts are never hard-deleted.

See [engineering-artifact-taxonomy.md](../domain/engineering-artifact-taxonomy.md) — Artifact Lifecycle Principles.

### Soft Deletion

All artifact nodes support a soft-deletion marker (`deleted_at` timestamp).

A soft-deleted node:

- is excluded from active workspace queries
- remains in the database with its full history
- retains all edges for historical traceability queries
- may be restored if required

### Why No Hard Deletion

Hard deletion breaks traceability provenance.

An estimation that was anchored to a requirement cannot explain its lineage if that requirement record no longer exists.

The semantic graph must remain historically coherent even as artifacts are retired.

Hard deletion is prohibited for any artifact that has participated in a graph relationship.

---

## Schema Ownership and Migration

### Single Schema

The platform uses a single Prisma schema file.

Module ownership is enforced by naming convention, not by separate schema files or database schemas.

This approach avoids the operational complexity of multi-schema databases while maintaining clear logical ownership boundaries.

### Migration Strategy

Prisma Migrate manages all schema migrations.

Migrations are version-controlled alongside the application code.

Each migration must be:

- backward-compatible where possible
- explicitly reviewed for cross-module impact before application
- applied to all environments through the standard deployment pipeline

Migrations that affect core graph tables (nodes, edges) require orchestrator review before implementation, as they carry the highest risk of graph consistency impact.

---

## Future Extensibility

The persistence strategy should remain extensible to support:

- **Read replicas** for graph traversal query separation from write paths
- **Partitioning** of node and edge tables by workspace as data volumes grow
- **Graph query optimization** through materialized adjacency views or graph indexes if traversal performance degrades
- **Event streaming** if the internal outbox pattern needs to evolve into an external event stream (Kafka, etc.) when operational scale justifies it
- **Cold storage** for archived workspace snapshots and execution history beyond active retention windows

These are not current-phase concerns.

The persistence model described in this document must be compatible with these evolutions without requiring foundational rethinking.
