# Traceability Model

## Related Documents

* [Domain: Typed Edge Semantics](../domain/typed-edge-semantics.md) — defines the edge types (DERIVED_FROM, IMPLEMENTS, TRACES_TO, IMPACTS, MITIGATES, REFINES, ESTIMATES) that carry traceability meaning and their endpoint rules
* [Architecture: Dependency Propagation](dependency-propagation.md) — defines the propagation mechanism; traceability traversal is distinct from propagation but shares the same edge graph
* [ADR-003 Graph as System of Record](../adr/003-graph-system-of-record.md) — the architectural decision that makes traceability a first-class graph concern
* [Architecture: Initial Schema](initial-schema.md) — defines the `graph_edges` table that backs all traversal queries
* [Architecture: Modular Boundaries](modular-boundaries.md) — traceability is owned by the `traceability/` module; graph storage is owned by `graph/`

---

## Purpose

Define how engineering artifacts remain connected across the SDLC workspace as a traceable graph. This document provides the traversal query interface, the edge types that carry traceability semantics, the relationship between traceability and propagation, and the performance strategy.

---

## Core Concepts

Traceability in EngraphSW is the ability to answer two questions for any artifact:

1. **Upstream**: What is the justification chain for this artifact's existence and current state?
2. **Downstream**: If this artifact changes, what other artifacts are impacted?

Both questions are answered by traversing typed edges in the graph. The `traceability/` module owns the traversal logic. The `graph/` module owns the edge storage.

---

## Edge Types That Carry Traceability Semantics

Not all edge types participate equally in traceability traversal. The following table defines which edge types are traceability-relevant and what role they play.

| Edge Type | Traceability Role | Traversal Direction |
|---|---|---|
| `DERIVED_FROM` | Provenance chain — why does this artifact exist? | Upstream: follow to find origins |
| `REFINES` | Specification chain — what requirement is this story implementing? | Upstream: follow to find context |
| `IMPLEMENTS` | Realization chain — what requirement does this component satisfy? | Upstream: follow to find intent |
| `TRACES_TO` | Explicit traceability link — auditable coverage assertion | Bidirectional: explicit audit trail |
| `IMPACTS` | Impact chain — what will break if this changes? | Downstream: follow to find blast radius |
| `MITIGATES` | Risk coverage — what risk does this story or requirement address? | Upstream from risk perspective |
| `ESTIMATES` | Size justification — what work items justify this estimate? | Upstream from estimate perspective |
| `CONTEXTUALIZES` | Context enrichment — provides background but does not derive | Upstream context only; does not trigger propagation |
| `INFLUENCES` | Soft dependency — affects but does not determine | Upstream context only; does not trigger propagation |

Edge types `CONTAINS` and `EXPOSES` represent structural/organizational relationships, not traceability chains. They are not included in traceability traversal.

See [Typed Edge Semantics](../domain/typed-edge-semantics.md) for the complete edge type definitions, propagation contracts, and valid endpoint rules.

---

## Traversal Query Interface

The `traceability/` module exposes traversal through its application service interface (`modules/traceability/index.ts`). No module may call traversal functions directly on the graph tables.

```typescript
interface TraversalService {
  // Return all nodes reachable by following edges upstream from the given node.
  // Upstream = following edge.source toward root artifacts.
  // depth: max traversal depth; default 10 (prevents runaway on cycles)
  // edgeTypes: if provided, only follow edges of these types
  getUpstreamNodes(
    nodeId: string,
    options?: { depth?: number; edgeTypes?: string[] }
  ): Promise<TraversalResult>;

  // Return all nodes reachable by following edges downstream from the given node.
  // Downstream = following edge.target to dependent artifacts.
  getDownstreamNodes(
    nodeId: string,
    options?: { depth?: number; edgeTypes?: string[] }
  ): Promise<TraversalResult>;

  // Return the shortest path between two nodes, if one exists.
  // Uses BFS across all edge types.
  getPath(
    fromNodeId: string,
    toNodeId: string,
    options?: { edgeTypes?: string[] }
  ): Promise<TraversalPath | null>;

  // Return the impact blast radius for a given node:
  // all downstream nodes that would be affected if this node changes.
  // Respects edge propagation contracts (IMPACTS, DERIVED_FROM, REFINES, IMPLEMENTS).
  getImpactScope(nodeId: string): Promise<TraversalResult>;

  // Invalidate traversal cache for a node (called after graph mutations)
  invalidateCache(nodeId: string): Promise<void>;
}

interface TraversalResult {
  nodes: TraversalNode[];
  edges: TraversalEdge[];
  depth: number;             // maximum depth reached
  truncated: boolean;        // true if depth limit was hit before exhausting the graph
}

interface TraversalNode {
  nodeId: string;
  nodeType: string;
  depth: number;             // how many hops from the root node
  content: unknown;          // current node content snapshot
  execStatus: NodeExecStatus;
}

interface TraversalEdge {
  edgeId: string;
  sourceId: string;
  targetId: string;
  edgeType: string;
}

interface TraversalPath {
  nodes: TraversalNode[];
  edges: TraversalEdge[];
}
```

---

## Distinction from Dependency Propagation

Traceability traversal and dependency propagation share the same edge graph but serve different purposes and are owned by different modules.

| Concern | Module | Trigger | Purpose |
|---|---|---|---|
| Traceability traversal | `traceability/` | User query or AI analysis | Answer "what does this connect to?" |
| Dependency propagation | `traceability/` | `NodeExecutionCompletedEvent` | Mark downstream nodes stale after an upstream change |

Propagation is event-driven and automatic. Traversal is query-driven and on-demand.

A traceability traversal reads the graph state as-is. A propagation pass modifies node execution status.

Implementation note: both live in the `traceability/` module because they share the traversal algorithm. However, they are separate use cases (`GetUpstreamNodesUseCase` vs `PropagateStaleStateUseCase`).

See [Dependency Propagation](dependency-propagation.md) for the propagation algorithm, batch semantics, and boundary rules.

---

## Performance Considerations

### Traversal Caching

Traversal results for frequently queried nodes are cached in memory (or Redis for multi-instance deployments).

Cache invalidation triggers:
- Any edge mutation affecting the traversal subgraph (edge create, delete)
- Any node soft-deletion in the traversal subgraph
- Explicit invalidation via `invalidateCache(nodeId)` called after graph mutations

Cache TTL: 5 minutes for read-heavy workspace sessions. Cache is warm for active graphs, cold for inactive ones.

### Traversal Boundaries

Default depth limit: 10 hops. This prevents runaway traversal on cyclic-adjacent graphs (the graph model prevents true cycles, but long chains exist).

`truncated: true` in the result signals that the traversal hit the depth limit. The UI shows a "traversal truncated" indicator when this occurs.

### Query Strategy

Traversal queries use recursive CTE (Common Table Expression) on the `graph_edges` table, scoped by `graph_id` to limit the search space.

```sql
-- Upstream traversal example (simplified)
WITH RECURSIVE upstream AS (
  SELECT source_id as node_id, id as edge_id, edge_type, 1 as depth
  FROM graph_edges
  WHERE target_id = $nodeId AND graph_id = $graphId

  UNION ALL

  SELECT e.source_id, e.id, e.edge_type, u.depth + 1
  FROM graph_edges e
  JOIN upstream u ON e.target_id = u.node_id
  WHERE e.graph_id = $graphId AND u.depth < $maxDepth
)
SELECT * FROM upstream;
```

Index requirements (from [Initial Schema](initial-schema.md)):
- `(graph_id, source_id)` — required for downstream traversal
- `(graph_id, target_id)` — required for upstream traversal

Both indexes must exist before traversal queries are executed in production.

---

## AI-Assisted Traceability

The `traceability/` module exposes AI-assisted operations through the `ai/` module abstraction:

- **Suggest missing links**: AI analyzes two nodes and suggests whether a traceability edge should exist between them
- **Explain impact chain**: AI explains in natural language why a downstream node is affected by an upstream change
- **Identify inconsistencies**: AI flags traceability gaps — artifact types that should be connected but aren't

All AI suggestions are staged for user review. No AI operation mutates the graph directly. See [AI Architecture](ai-architecture.md) — AI Output Lifecycle.

---

## Future Extensibility

- **Traceability matrices**: tabular view of coverage (e.g., which requirements have linked user stories)
- **Impact reports**: export the downstream blast radius of a change as a structured report
- **Cross-workspace provenance**: traversal across workspace boundaries for organization-level traceability
- **Semantic consistency checks**: automated analysis of whether the current traceability graph is logically coherent
