# Dependency Propagation

## Purpose

Define how changes in one graph node propagate through the dependency graph to affect connected nodes.

This document defines the propagation mechanism — the engine that gives the graph its intelligence.

It does not define edge-type-specific propagation rules. Those are defined in the domain layer.

See [docs/domain/typed-edge-semantics.md](../domain/typed-edge-semantics.md).

---

## Core Concept

The graph is not a static visualization.

It is a live dependency network where the state of any node may depend on the state of upstream nodes.

When an upstream node changes, downstream nodes that depend on it may no longer reflect current reality.

Propagation is the mechanism by which the graph acknowledges this and surfaces it to the user.

Propagation does not automatically recompute downstream nodes.

Propagation marks them as stale — signaling that they require attention, recalculation, or review.

This distinction is fundamental:

- **Propagation** = marking affected nodes as potentially invalid
- **Recalculation** = resolving stale nodes by re-executing them

The two are separate concerns and must not be conflated in implementation.

---

## Staleness

### Definition

A node is **stale** when its current outputs may no longer reflect the state of its upstream dependencies.

Staleness is a signal, not an error.

A stale node still holds its last computed outputs. Those outputs remain readable and visible.

Staleness does not erase prior state.

### Staleness Conditions

A node becomes stale when:

- a directly connected upstream node changes its output state
- a directly connected upstream node becomes stale (transitively stale)
- an edge connecting it to an upstream node is added, modified, or removed
- an upstream node's execution result is invalidated

### Staleness Resolution

A stale node is resolved when:

- it is explicitly re-executed by the user or the system
- its outputs are recomputed and validated as current
- the execution completes successfully and its state returns to `success`

A stale node that fails re-execution transitions to `failed`, not back to stale.

### Staleness Persistence

Staleness must be persisted.

A node that is stale at the time of a session close must remain stale when the session resumes.

Staleness state is not session-local.

---

## Invalidation Cascade

### Cascade Definition

An invalidation cascade is the process by which staleness propagates downstream through the graph when a source node changes.

When node A changes:

1. All nodes directly downstream of A are marked stale
2. All nodes downstream of those nodes are marked stale
3. The cascade continues until it reaches nodes with no further downstream connections, or until it reaches a propagation boundary

### Cascade Semantics

The cascade marks nodes stale — it does not trigger execution.

The cascade is depth-first and traverses all reachable downstream paths from the changed node.

The cascade must complete atomically before any recalculation begins. This prevents partial-stale graph states.

### Cascade Scope

By default, a cascade propagates to all reachable downstream nodes within the graph.

Propagation may be bounded by:

- explicit propagation boundary declarations on edge types (defined in typed edge semantics)
- workspace-scoped propagation limits
- user-configured propagation scope preferences

When a cascade terminates at a boundary, nodes beyond the boundary are not marked stale.

Boundary decisions belong to the domain layer, not the propagation engine.

The propagation engine respects boundaries but does not define them.

---

## Recalculation

### Trigger Model

Recalculation may be triggered by:

- explicit user action on a stale node ("recalculate")
- system-initiated recalculation when propagation completes and auto-recalculation is configured
- dependency-ordered batch recalculation

Recalculation is always traceable to a trigger. It must not occur silently.

### Execution Ordering

When multiple stale nodes are recalculated, execution must respect dependency order.

A node must not recalculate before its upstream dependencies have resolved to a non-stale state.

Dependency-ordered execution:

1. Identify the set of stale nodes requiring recalculation
2. Topologically sort the set by dependency graph order
3. Execute nodes in dependency order, upstream to downstream
4. Propagate updated outputs to downstream nodes after each execution

If a node in the ordered set fails execution, downstream nodes in the same batch should not proceed. They remain stale.

### Recalculation History

Each recalculation should produce an auditable execution record.

Records must capture:

- the trigger (user action, system event, batch operation)
- the input state at time of execution
- the output produced
- the execution result (success, failed)
- timestamps and latency

Prior execution records must not be overwritten. They form the execution history of the node.

---

## Propagation Direction

The graph supports two traversal directions with distinct semantic purposes.

### Downstream Propagation (Forward)

Used for: staleness propagation, invalidation cascades, impact analysis

Direction: source → dependents

When node A changes, downstream propagation traverses the graph forward to identify all nodes whose outputs depend on A.

### Upstream Traversal (Reverse)

Used for: provenance analysis, dependency inspection, impact justification

Direction: node → its sources

When a user asks "why is this node stale?" or "what does this node depend on?", upstream traversal traces back through the graph to the contributing sources.

Upstream traversal does not trigger staleness. It is a read operation.

### Symmetry Rule

Downstream propagation and upstream traversal must produce symmetric results over the same graph topology.

If A → B → C (downstream), then C → B → A (upstream).

Asymmetry in traversal results indicates a data integrity issue in edge storage.

---

## Propagation Boundaries

Propagation boundaries limit how far an invalidation cascade travels.

### Hard Boundaries

A hard boundary stops propagation entirely. Nodes beyond the boundary are not marked stale regardless of upstream changes.

Hard boundaries are appropriate when:

- a node explicitly isolates itself from upstream staleness (a "snapshot" semantic)
- workspace or access control boundaries are crossed
- a node's output is intentionally frozen

### Soft Boundaries

A soft boundary pauses propagation and notifies rather than continues silently.

Nodes beyond a soft boundary are flagged as "potentially affected" rather than "stale."

The user or system then decides whether propagation should continue.

Soft boundaries are appropriate for high-impact propagation events where silent cascading is undesirable.

### Boundary Ownership

Boundary types and their conditions are defined in the domain layer through typed edge semantics.

The propagation engine does not decide whether a boundary applies — it reads boundary declarations from edge metadata and respects them.

---

## Cycle Detection

The dependency graph used for execution propagation must be a directed acyclic graph (DAG).

Cycles are invalid in the propagation model.

A cycle would mean a node's output depends on itself, either directly or transitively — producing undefined execution behavior.

### Cycle Prevention

Cycle detection must be performed:

- when an edge is created
- before any batch execution begins

If a proposed edge would create a cycle, the edge creation must be rejected with an explicit error.

### Cycle Recovery

If a cycle is detected in an existing graph (due to data inconsistency), the affected nodes must be flagged and propagation must halt rather than enter an infinite loop.

Cycle presence is a data integrity violation and must be surfaced as such, not silently tolerated.

---

## Propagation and Module Boundaries

Propagation crosses module boundaries through domain events, consistent with [ADR-004 — Event-Driven Communication](../adr/004-event-driven-communication.md).

The `traceability` module owns the propagation engine and the dependency index.

When a node changes, the module owning that node publishes a domain event.

The `traceability` module consumes this event, determines the affected downstream nodes, marks them stale, and publishes a `NodesStaledEvent` for downstream consumers.

The `execution` module consumes stale events and applies execution lifecycle state transitions.

No module directly calls the traceability or execution internals to trigger propagation.

Propagation is always event-driven across module boundaries.

---

## Batch Propagation

### Batching Semantics

Multiple simultaneous changes should be batched before propagation begins.

Batching prevents redundant cascade computation when multiple upstream nodes change within the same operation.

Without batching: if A and B both connect to C, and both A and B change simultaneously, C would be processed twice.

With batching: changes are collected, the affected set is deduplicated, and a single cascade is computed over the merged change set.

### Batch Boundaries

A batch is bounded by an explicit operation boundary — a user action, an import, a bulk update.

The propagation engine should not attempt to infer batch boundaries automatically.

Batch boundaries must be explicitly declared by the invoking operation.

---

## Audit and Observability

The propagation engine must produce observable, auditable records.

### Propagation Events

All propagation operations must emit observable events:

- which node changed (the source)
- which nodes were marked stale (the affected set)
- the cascade depth and scope
- the timestamp of the propagation

### Stale State Visibility

Stale nodes must be visually distinguishable in the graph interface.

Stale state must not be hidden or minimized. It is engineering information.

The graph should communicate at a glance which nodes are current, which are stale, and which have failed.

### Propagation Trace

For any given stale node, the system must be able to produce a propagation trace — the chain of upstream changes that caused the staleness.

Propagation traces are essential for engineering reasoning in complex graphs.

---

## Future Extensibility

The propagation engine should be designed to support:

- configurable propagation strategies per workspace or per graph region
- priority-based recalculation ordering
- partial graph propagation for large graphs
- subscription-based staleness notifications for external consumers
- propagation dry-runs (simulating a change without applying staleness)
- cross-workspace propagation when artifact dependencies span workspaces

These are not current phase requirements.

The mechanism-level design described in this document must remain extensible enough to accommodate them without architectural rethinking.
