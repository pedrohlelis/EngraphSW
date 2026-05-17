# ADR-007 — Client State Management

## Status

Accepted

## Metadata

| Field | Value |
|---|---|
| Domain | Canvas Engine / Frontend |
| Phase Introduced | Phase 1B |
| Related ADRs | [ADR-001 Modular Monolith](001-modular-monolith.md), [ADR-006 Canvas Technology](006-canvas-technology.md) |
| Related Architecture | [Canvas Engine](../architecture/canvas-engine.md) |

---

# Context

EngraphSW requires a client-side state management solution to coordinate:

- graph viewport state (pan position, zoom level, node positions)
- graph selection state (selected nodes, selected edges, multi-select)
- graph execution state (running nodes, pending nodes, error states)
- canvas UI state (active tool, edge creation mode, panel visibility)
- workspace state (active graph, workspace metadata)
- optimistic update state (local changes pending server confirmation)

The state management layer must integrate cleanly with React Flow (ADR-006), which exposes its own internal state through a `useReactFlow` hook but expects external state to be driven by the application. Graph persistence sync — pushing canvas mutations to the server — runs through this layer.

Several candidate approaches were evaluated:

1. **Zustand** — a lightweight, hooks-based store with slice composition and no provider requirement
2. **Redux Toolkit** — a structured state container with actions, reducers, and DevTools
3. **Jotai** — an atomic state library with fine-grained reactivity
4. **React Context** — built-in React primitive for shared state

---

# Decision

EngraphSW will use **Zustand** for client-side state management.

## Rationale

**Minimal boilerplate.** Zustand stores are plain JavaScript objects with setter functions. There are no actions, reducers, action creators, or dispatch patterns. State reads and writes are direct function calls, which reduces cognitive overhead compared to Redux Toolkit's ceremony.

**No provider requirement.** Zustand stores exist outside the React component tree. State is accessed via hooks (`useGraphStore`, `useSelectionStore`) anywhere in the component hierarchy without wrapping components in providers. React Flow's component tree does not need a wrapping context layer.

**Slice composition.** Zustand supports slice-based store composition using the `combine` or `create` patterns. Graph state, selection state, execution state, and viewport state can be defined as separate slices that are composed into the store at creation time. This maps cleanly onto the distinct state concerns of the canvas.

**React Flow integration.** The React Flow documentation explicitly demonstrates Zustand as the recommended external state management approach. The integration pattern is well-documented: React Flow's `onNodesChange`, `onEdgesChange`, and `onConnect` handlers write to the Zustand store, and the store's state drives React Flow's `nodes` and `edges` props. This avoids the React Flow anti-pattern of maintaining duplicate node/edge state in both React Flow internal state and an external store.

**TypeScript support.** Zustand is fully typed with TypeScript generics. Store slices are strongly typed; selectors and setters carry full type inference.

**Lightweight footprint.** Zustand adds approximately 1KB to the bundle. Redux Toolkit adds significantly more. For a canvas-heavy application where most runtime cost is React Flow rendering, keeping the state layer minimal is appropriate.

## Store Design

The client store will be organized as distinct slices, composed into a single store:

**[Graph State Slice](../architecture/canvas-engine.md)** — current graph data (nodes, edges), dirty state, last-synced revision. Drives React Flow `nodes` and `edges` props. Receives mutations from React Flow event handlers.

**[Selection State Slice](../architecture/canvas-engine.md)** — currently selected node IDs, selected edge IDs, multi-select mode. Used by context menus, detail panels, and batch operations.

**[Execution State Slice](../architecture/canvas-engine.md)** — per-node execution status (idle, running, complete, error), pending execution queue, real-time progress state.

**[Viewport State Slice](../architecture/canvas-engine.md)** — current pan position, zoom level, viewport bounds. Used for minimap, fit-to-view commands, and viewport-dependent rendering decisions.

**[Workspace State Slice](../architecture/canvas-engine.md)** — active workspace ID, active graph ID, workspace metadata. Drives navigation and context.

---

# Consequences

## Positive

- Minimal boilerplate; store setup is fast and readable
- No provider wrapping; state accessible anywhere without component restructuring
- Slice composition keeps concerns separated without requiring multiple independent stores
- React Flow integration is well-documented and follows established patterns
- Bundle impact is negligible
- DevTools support available via `zustand/middleware` (devtools middleware)

## Negative

- No built-in action history or time-travel debugging (unlike Redux DevTools); must be layered manually if needed
- Large or deeply nested state trees require careful selector design to avoid excess re-renders; not a current concern given the planned store structure
- Less structured than Redux Toolkit; discipline required to avoid store sprawl as the application grows

---

# Alternatives Considered

## Redux Toolkit

Rejected because:

- boilerplate cost (slice files, action definitions, selectors) is not justified for the state complexity planned for Phase 3
- the React Flow integration pattern with Redux requires more indirection than the Zustand pattern
- introduces `react-redux` provider wrapping throughout the component tree
- Redux's strengths (DevTools, time-travel, strict action traceability) are not required at the current phase
- can be reconsidered if state complexity grows to a scale where Redux's structure is net positive

## Jotai

Rejected because:

- atomic model is well-suited for fine-grained isolated state (individual form fields, component-level toggles) but awkward for coordinated state like the graph slice, where multiple fields change together on a single graph mutation
- React Flow integration requires bridging atoms to React Flow's node/edge arrays, which introduces synchronization complexity
- no meaningful advantage over Zustand for this use case

## React Context

Rejected because:

- React Context causes all consuming components to re-render when any part of the context value changes; graph state mutations (node position updates, edge creation) occur at high frequency during interaction
- fine-grained subscription to specific state slices requires additional libraries (useMemo, React.memo, context splitting) that eliminate the simplicity advantage
- not suitable as the primary state container for a high-frequency interaction layer like the graph canvas

---

# Long-Term Implications

The store structure defined here should remain stable through Phase 4 (Node Framework) and Phase 5 (SDLC Nodes).

As the application grows, individual slices may be extracted into module-specific substores if the graph slice grows unwieldy. Zustand's composition model supports this without a full state management migration.

If real-time collaboration requirements in Phase 8 require server-authoritative state synchronization (via Liveblocks or similar), the Zustand store becomes the client-side mirror of server state. Liveblocks integrates with Zustand stores through its `@liveblocks/zustand` package, preserving the store architecture while adding real-time sync.

Middleware (`zustand/middleware`) should be used from the start for:

- `devtools` — enables Redux DevTools inspection in development
- `immer` — allows mutations inside set functions without manual spread, which reduces boilerplate for nested state updates in the graph slice

---

# Related Decisions

- [ADR-001 — Modular Monolith Architecture](001-modular-monolith.md)
- [ADR-006 — Canvas and Visualization Technology](006-canvas-technology.md)
