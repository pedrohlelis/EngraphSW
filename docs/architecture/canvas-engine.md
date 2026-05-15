# Canvas Engine

## Related Documents

* [ADR-002 Plugin-Oriented Node Architecture](../adr/002-plugin-node-architecture.md) — node types rendered on canvas are defined by the plugin registry
* [ADR-006 Canvas and Visualization Technology](../adr/006-canvas-technology.md) — React Flow decision record; rationale for all component choices below
* [ADR-007 Client State Management](../adr/007-state-management.md) — Zustand decision record; store slice design referenced below
* [Architecture: Modular Boundaries](modular-boundaries.md) — canvas belongs to the `workspace/` module; graph operations belong to the `graph/` module
* [Architecture: Module Conventions](module-conventions.md) — canvas component file organization
* [Domain: Typed Edge Semantics](../domain/typed-edge-semantics.md) — edge types that canvas connections must validate and visually represent
* [Phase 3 — Canvas Engine](../roadmap/PHASE_3_CANVAS_ENGINE.md) — the implementation phase that builds from this architecture

---

## Purpose

Define the component breakdown, state management design, and persistence sync contract for the EngraphSW graph canvas. This document provides the Implementer with the information needed to build Phase 3 without design decisions.

---

## React Flow Component Breakdown

The canvas is built on `@xyflow/react` (React Flow v12). All canvas components live under `app/(workspace)/canvas/` and `modules/workspace/`.

### `WorkspaceCanvas`

The root canvas component. Mounts React Flow and wires all event handlers to the Zustand graph store.

```typescript
// app/(workspace)/canvas/WorkspaceCanvas.tsx
// "use client"

interface WorkspaceCanvasProps {
  graphId: string;
}
```

Responsibilities:
- Renders `<ReactFlow>` with `nodes`, `edges`, `nodeTypes`, and `edgeTypes` props from Zustand store
- Binds `onNodesChange`, `onEdgesChange`, `onConnect` to Zustand store mutations
- Renders `<MiniMap>`, `<Controls>`, `<Background>` as standard React Flow panels
- Does not contain business logic; delegates all state to Zustand

### `NodeRenderer`

The per-node React component. Registered in the `nodeTypes` map passed to `<ReactFlow>`.

```typescript
// app/(workspace)/canvas/nodes/NodeRenderer.tsx
// "use client"

// React Flow calls this with NodeProps for every node in the graph
// It looks up the registered node type's rendererKey and renders the matching UI component
interface NodeRendererProps extends NodeProps {
  data: {
    nodeType: string;
    content: unknown;
    execStatus: NodeExecStatus;
  };
}
```

Each node type plugin declares a `rendererKey` in its `NodeDefinition`. `NodeRenderer` looks up the corresponding React component from a renderer registry. This allows node types to provide custom visual representations without coupling the canvas to specific artifact types.

### `EdgeRenderer`

The per-edge React component. Registered in the `edgeTypes` map passed to `<ReactFlow>`.

```typescript
// app/(workspace)/canvas/edges/EdgeRenderer.tsx
// "use client"

// Renders a typed edge with appropriate visual styling
// Edge type maps to a visual style defined in EdgeTypeRegistry
interface EdgeRendererProps extends EdgeProps {
  data: {
    edgeType: string;   // from typed-edge-semantics.md
    label?: string;
  };
}
```

### `EdgeTypeRegistry`

Maps typed edge keys from [Typed Edge Semantics](../domain/typed-edge-semantics.md) to visual rendering parameters. Edge type keys match the `edge_type` column in [graph_edges](initial-schema.md).

```typescript
interface EdgeTypeRenderConfig {
  color: string;        // edge line color
  strokeDasharray?: string;  // dashed/dotted pattern
  label?: string;       // default label (override per edge instance)
  markerEnd?: string;   // arrowhead type
}

// Edge type keys match typed-edge-semantics.md exactly
const EdgeTypeRegistry: Record<string, EdgeTypeRenderConfig> = {
  DERIVED_FROM: { color: '#F59E0B', markerEnd: 'arrowclosed' },
  REFINES:      { color: '#6366F1', strokeDasharray: '5,3', markerEnd: 'arrowclosed' },
  IMPLEMENTS:   { color: '#10B981', markerEnd: 'arrowclosed' },
  CONTEXTUALIZES: { color: '#8B5CF6', strokeDasharray: '3,3' },
  INFLUENCES:   { color: '#F97316', strokeDasharray: '5,3' },
  ESTIMATES:    { color: '#14B8A6', markerEnd: 'arrowclosed' },
  IMPACTS:      { color: '#EF4444', markerEnd: 'arrowclosed' },
  MITIGATES:    { color: '#22C55E', markerEnd: 'arrowclosed' },
  TRACES_TO:    { color: '#94A3B8', strokeDasharray: '2,2', markerEnd: 'arrow' },
  CONTAINS:     { color: '#64748B' },
  EXPOSES:      { color: '#A78BFA', markerEnd: 'arrowclosed' },
};
```

### Handle Contracts

Handles (connection points on nodes) are defined per node type and are typed by the `NodeInputDefinition` and `NodeOutputDefinition` arrays in the `NodeDefinition`.

- Each input slot becomes a `<Handle type="target" id={input.key} />`
- Each output slot becomes a `<Handle type="source" id={output.key} />`
- React Flow's `isValidConnection` callback validates edge creation against allowed node types declared in `NodeInputDefinition.allowedNodeTypes`

---

## Zustand Store Design

The canvas uses a single composed Zustand store with five slices. See [ADR-007](../adr/007-state-management.md) for rationale.

### Graph Slice

Owns the live graph data for React Flow. Mirrors the [`node_nodes`](initial-schema.md) and [`graph_edges`](initial-schema.md) tables as the client-side representation.

```typescript
interface GraphSlice {
  nodes: Node[];             // React Flow node array
  edges: Edge[];             // React Flow edge array
  isDirty: boolean;          // local changes not yet synced to server
  lastSyncedAt: Date | null;

  // React Flow event handlers — these update the slice
  onNodesChange: (changes: NodeChange[]) => void;
  onEdgesChange: (changes: EdgeChange[]) => void;
  onConnect: (connection: Connection) => void;

  // Actions
  loadGraph: (graphId: string) => Promise<void>;
  syncToServer: () => Promise<void>;
}
```

### Selection Slice

```typescript
interface SelectionSlice {
  selectedNodeIds: string[];
  selectedEdgeIds: string[];
  multiSelectActive: boolean;

  selectNode: (nodeId: string, multi?: boolean) => void;
  selectEdge: (edgeId: string, multi?: boolean) => void;
  clearSelection: () => void;
}
```

### Execution Slice

Reflects the execution status of each node. Drives execution state rendering in the canvas. Status values are sourced from [NodeExecStatus](node-execution-model.md) and synced via [ExecuteNodeUseCase](node-execution-model.md).

```typescript
interface ExecutionSlice {
  executionStatus: Record<string, NodeExecStatus>;  // nodeId -> status
  pendingExecutions: string[];                       // nodeIds queued for execution

  updateNodeStatus: (nodeId: string, status: NodeExecStatus) => void;
  triggerExecution: (nodeId: string) => Promise<void>;
}
```

### Viewport Slice

```typescript
interface ViewportSlice {
  viewport: Viewport;   // React Flow Viewport: { x, y, zoom }
  setViewport: (viewport: Viewport) => void;
  fitView: () => void;
}
```

### Workspace Slice

```typescript
interface WorkspaceSlice {
  activeWorkspaceId: string | null;
  activeGraphId: string | null;
  setActiveGraph: (workspaceId: string, graphId: string) => void;
}
```

### Store Composition

```typescript
// modules/workspace/infrastructure/canvasStore.ts
export const useCanvasStore = create<
  GraphSlice & SelectionSlice & ExecutionSlice & ViewportSlice & WorkspaceSlice
>()(
  devtools(
    immer((...args) => ({
      ...createGraphSlice(...args),
      ...createSelectionSlice(...args),
      ...createExecutionSlice(...args),
      ...createViewportSlice(...args),
      ...createWorkspaceSlice(...args),
    }))
  )
);
```

---

## Graph Persistence Sync Contract

The canvas maintains a local Zustand state that must stay synchronized with the server-persisted graph. This is the sync contract.

### Load flow

1. `WorkspaceCanvas` mounts with `graphId`
2. `graphSlice.loadGraph(graphId)` calls the graph API route
3. API returns nodes and edges from the database
4. Slice populates `nodes` and `edges` arrays; `isDirty = false`

### Mutation flow

1. User drags a node, creates an edge, or makes a canvas change
2. React Flow event handlers update the Zustand `GraphSlice` immediately (optimistic)
3. `isDirty = true`
4. A debounced or explicit save triggers `graphSlice.syncToServer()`
5. Sync calls the graph API route with the delta
6. On success: `isDirty = false`, `lastSyncedAt = now`
7. On failure: `isDirty` stays true; error surfaced to user

### Sync granularity

Canvas position changes (node drag) sync at coarse intervals (debounced ~500ms).

Structural changes (node create/delete, edge create/delete) sync immediately — they affect the graph's semantic structure, not just visual layout.

---

## Typed Edge Rendering Contract

Edge types from [Typed Edge Semantics](../domain/typed-edge-semantics.md) map to canvas visual rendering through `EdgeTypeRegistry`.

When a user creates an edge by connecting two handles:
1. The connection attempt triggers `isValidConnection` validation
2. If valid, the user is prompted to select the edge type (or a default is applied based on node type combination)
3. The `onConnect` handler creates an edge record with the selected `edge_type`
4. `EdgeRenderer` looks up the rendering config from `EdgeTypeRegistry` and renders accordingly

Edge labels are shown on hover or always-on based on canvas zoom level (label visibility threshold is a viewport-level concern in the `ViewportSlice`).

---

## Core Interaction Model

- infinite canvas: pan and zoom via React Flow's default viewport controls
- node drag: updates `position_x` / `position_y` in the graph slice; synced to server on debounce
- node selection: single-click selects; shift-click adds to multi-select; `SelectionSlice` owns state
- edge creation: drag from source handle to target handle; triggers `isValidConnection` before completing
- canvas right-click: context menu for node creation (new node placed at click coordinates)
- minimap: always visible; clicking the minimap pans the viewport
- keyboard: Delete key removes selected nodes/edges; Ctrl+A selects all; Escape clears selection

---

## Future Extensibility

- **Collaboration cursors**: Liveblocks presence cursors overlay on the canvas viewport in Phase 8; no canvas changes required until then
- **Graph overlays**: traceability and execution state overlays (highlight path, dim unrelated nodes) layered on top of standard rendering
- **Auto-layout**: Dagre or ELK integration for automatic graph layout; triggered as an optional user action, not the default
- **Node grouping**: React Flow's group node concept for visual clustering of related artifacts
