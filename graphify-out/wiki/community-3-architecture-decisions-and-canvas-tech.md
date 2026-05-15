# Architecture Decisions & Canvas Tech

**Community 3** · 18 nodes · cohesion=0.13

## Nodes

- **ADR-001: Modular Monolith Architecture** [`rationale`] — `docs/adr/001-modular-monolith.md`
- **ADR-002: Plugin-Oriented Node Architecture** [`rationale`] — `docs/adr/002-plugin-node-architecture.md`
- **ADR-006: Canvas and Visualization Technology** [`rationale`] — `docs/adr/006-canvas-technology.md`
- **Canvas Module (modules/canvas/)** [`concept`] — `docs/adr/006-canvas-technology.md`
- **Cytoscape.js** [`concept`] — `docs/adr/006-canvas-technology.md`
- **D3.js** [`concept`] — `docs/adr/006-canvas-technology.md`
- **React Flow (XYFlow)** [`concept`] — `docs/adr/006-canvas-technology.md`
- **ADR-007: Client State Management** [`rationale`] — `docs/adr/007-state-management.md`
- **Execution State Slice** [`concept`] — `docs/adr/007-state-management.md`
- **Graph State Slice** [`concept`] — `docs/adr/007-state-management.md`
- **Jotai** [`concept`] — `docs/adr/007-state-management.md`
- **React Context** [`concept`] — `docs/adr/007-state-management.md`
- **Redux Toolkit** [`concept`] — `docs/adr/007-state-management.md`
- **Selection State Slice** [`concept`] — `docs/adr/007-state-management.md`
- **Viewport State Slice** [`concept`] — `docs/adr/007-state-management.md`
- **Workspace State Slice** [`concept`] — `docs/adr/007-state-management.md`
- **Zustand** [`concept`] — `docs/adr/007-state-management.md`
- **Modular Boundaries Architecture** [`document`] — `docs/architecture/modular-boundaries.md`

## Internal Edges

  - ADR-006: Canvas and Visualization Technology --references--> ADR-001: Modular Monolith Architecture [EXTRACTED 1.0]
  - ADR-006: Canvas and Visualization Technology --references--> ADR-002: Plugin-Oriented Node Architecture [EXTRACTED 1.0]
  - ADR-006: Canvas and Visualization Technology --references--> ADR-007: Client State Management [EXTRACTED 1.0]
  - ADR-006: Canvas and Visualization Technology --references--> Modular Boundaries Architecture [EXTRACTED 1.0]
  - ADR-006: Canvas and Visualization Technology --implements--> React Flow (XYFlow) [EXTRACTED 1.0]
  - Canvas Module (modules/canvas/) --implements--> React Flow (XYFlow) [EXTRACTED 0.95]
  - React Flow (XYFlow) --conceptually_related_to--> D3.js [EXTRACTED 0.9]
  - React Flow (XYFlow) --conceptually_related_to--> Cytoscape.js [EXTRACTED 0.9]
  - ADR-007: Client State Management --references--> ADR-001: Modular Monolith Architecture [EXTRACTED 1.0]
  - ADR-007: Client State Management --references--> ADR-006: Canvas and Visualization Technology [EXTRACTED 1.0]
  - ADR-007: Client State Management --implements--> Zustand [EXTRACTED 1.0]
  - Graph State Slice --shares_data_with--> React Flow (XYFlow) [EXTRACTED 0.95]
  - Zustand --conceptually_related_to--> Redux Toolkit [EXTRACTED 0.9]
  - Zustand --conceptually_related_to--> Jotai [EXTRACTED 0.9]
  - Zustand --conceptually_related_to--> React Context [EXTRACTED 0.9]
  - Zustand --shares_data_with--> React Flow (XYFlow) [EXTRACTED 1.0]
  - Zustand --shares_data_with--> Graph State Slice [INFERRED 0.9]
  - Zustand --shares_data_with--> Selection State Slice [INFERRED 0.9]
  - Zustand --shares_data_with--> Execution State Slice [INFERRED 0.9]
  - Zustand --shares_data_with--> Viewport State Slice [INFERRED 0.9]
  - Zustand --shares_data_with--> Workspace State Slice [INFERRED 0.9]

## Cross-Community Edges

  - ADR-006: Canvas and Visualization Technology --references--> canvas_engine_architecture [EXTRACTED] (``)
  - ADR-007: Client State Management --references--> canvas_engine_architecture [EXTRACTED] (``)

[Back to index](index.md)
