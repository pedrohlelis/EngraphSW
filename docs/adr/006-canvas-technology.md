# ADR-006 — Canvas and Visualization Technology

## Status

Accepted

## Metadata

| Field | Value |
|---|---|
| Domain | Canvas Engine |
| Phase Introduced | Phase 1B |
| Related ADRs | [ADR-001 Modular Monolith](001-modular-monolith.md), [ADR-002 Plugin-Oriented Node Architecture](002-plugin-node-architecture.md) |
| Related Architecture | [Canvas Engine](../architecture/canvas-engine.md), [Modular Boundaries](../architecture/modular-boundaries.md) |

---

# Context

EngraphSW requires a graph visualization and interaction layer capable of:

- rendering arbitrary node types with custom visual representations
- rendering typed edges with distinct visual semantics (labels, colors, routing styles)
- supporting drag-and-drop node placement and repositioning
- supporting interactive edge creation between node handles
- managing a persistent viewport (pan, zoom, minimap)
- integrating with React and the broader TypeScript ecosystem
- supporting large graphs without prohibitive performance degradation

The canvas layer is not cosmetic. It is the primary user interface of the platform. Node interaction, traceability visualization, dependency exploration, and execution state display all surface through it.

Three candidate approaches were evaluated:

1. **React Flow (XYFlow)** — a React-native graph library with first-class support for custom nodes, typed edges, and interactive handles
2. **D3.js** — a low-level SVG manipulation library requiring custom layout, interaction, and rendering implementation
3. **Cytoscape.js** — a graph library with declarative API and extensive layout algorithms, primarily targeting static graph display

---

# Decision

EngraphSW will use **React Flow v12** (the XYFlow package, `@xyflow/react`) as the canvas and graph visualization library.

## Rationale

**React-native architecture.** React Flow renders nodes as standard React components. Custom node types are React components mounted inside the graph viewport. This means the entire React ecosystem (state management, context, animation libraries, UI component libraries) is available inside node implementations without bridging layers or escape hatches. No other evaluated library provides this.

**Typed edge support.** React Flow has first-class support for custom edge types, rendered as React components. This directly supports the typed edge model defined in `typed-edge-semantics.md`: each edge type (DERIVED_FROM, REFINES, IMPLEMENTS, etc.) can have a distinct visual representation, label, and routing style without library workarounds.

**Custom node handles.** React Flow's handle system allows nodes to define typed input and output connection points. This maps cleanly onto the node execution model's input/output port concept and allows edge creation to be semantically constrained at the UI level.

**Active maintenance and ecosystem.** XYFlow is the company behind React Flow, maintaining both the open-source library and a Pro tier. The library is actively maintained, well-documented, and widely adopted in production graph applications.

**TypeScript-first.** The library is written in TypeScript and ships full type definitions. This aligns with the platform's strong-typing doctrine.

**React ecosystem alignment.** The platform uses Next.js, React, Zustand, and shadcn/ui. React Flow integrates directly with this stack. D3.js and Cytoscape.js would require significant bridging work to coexist with React's component lifecycle and state management.

## React Flow Pro

React Flow Pro features (node resizing, collaboration cursors) are not required for Phase 3 (Canvas Engine) or Phase 4 (Node Framework). The open-source license (`@xyflow/react`) is sufficient for all planned functionality. Pro features may be reconsidered if collaboration requirements in Phase 8 cannot be met with the open-source tier.

---

# Consequences

## Positive

- Custom node types are React components — no bridging or special rendering primitives
- Typed edge rendering is supported natively
- Full TypeScript type safety across canvas implementation
- Zustand integration follows documented React Flow patterns (see ADR-007)
- Graph viewport management (pan, zoom, fit-to-view, minimap) is provided out of the box
- No custom layout engine required for the initial implementation

## Negative

- React Flow's default layout is manual (user positions nodes). Automatic layout algorithms require a separate library (e.g., ELK, Dagre) if needed in future phases
- Performance at very large node counts (1000+) requires React Flow's virtualization configuration; this is documented but requires deliberate setup
- Pro features require a commercial license if adopted; OSS tier is feature-complete for planned scope

---

# Alternatives Considered

## D3.js

Rejected because:

- D3 operates on the DOM directly; integrating with React requires either `useEffect`-based mutation or full ownership of DOM nodes outside React's control
- all interaction behavior (drag, zoom, edge creation, selection) must be implemented from scratch
- custom node rendering requires manual SVG or foreignObject management
- development cost is prohibitively high compared to React Flow for equivalent functionality
- no advantage justifies the implementation cost given the team's React expertise

## Cytoscape.js

Rejected because:

- Cytoscape renders to a canvas or SVG element and does not use React components for node rendering
- custom node UI requires embedding HTML via `popper.js` extensions — a brittle bridging layer
- primarily designed for static graph display and analysis, not interactive node-based editing
- TypeScript support is secondary; type definitions are community-maintained
- does not natively support the React component model that the rest of the platform depends on

## Custom Canvas Implementation

Rejected without detailed evaluation:

- the scope of building a graph interaction layer from scratch (viewport, hit-testing, drag, edge routing, custom node rendering) represents months of foundational work
- no unique requirements exist that cannot be met by React Flow
- React Flow is open-source; the team inherits a tested, maintained implementation

---

# Long-Term Implications

The canvas module (`modules/canvas/`) will own the React Flow integration layer.

As the graph grows in complexity, performance optimization of the canvas layer will require:

- React Flow's `nodeExtent` and `translateExtent` constraints for large graphs
- potential adoption of automatic layout (Dagre or ELK) for structured graph views
- progressive loading or virtualization strategies if graph size grows beyond typical React Flow defaults

These concerns are deferred to Phase 3 and beyond. The decision to use React Flow does not preclude future optimization; it provides the baseline upon which optimization is layered.

---

# Related Decisions

- [ADR-001 — Modular Monolith Architecture](001-modular-monolith.md)
- [ADR-002 — Plugin-Oriented Node Architecture](002-plugin-node-architecture.md)
- [ADR-007 — Client State Management](007-state-management.md)
