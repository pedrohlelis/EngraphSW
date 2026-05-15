# Canvas State & Rendering

**Community 2** · 19 nodes · cohesion=0.11

## Nodes

- **EdgeRenderer** [`document`] — `docs/architecture/canvas-engine.md`
- **EdgeTypeRegistry** [`document`] — `docs/architecture/canvas-engine.md`
- **Execution Slice** [`document`] — `docs/architecture/canvas-engine.md`
- **Graph Slice** [`document`] — `docs/architecture/canvas-engine.md`
- **NodeRenderer** [`document`] — `docs/architecture/canvas-engine.md`
- **Selection Slice** [`document`] — `docs/architecture/canvas-engine.md`
- **useCanvasStore** [`document`] — `docs/architecture/canvas-engine.md`
- **Viewport Slice** [`document`] — `docs/architecture/canvas-engine.md`
- **WorkspaceCanvas** [`document`] — `docs/architecture/canvas-engine.md`
- **Workspace Slice** [`document`] — `docs/architecture/canvas-engine.md`
- **graph_edges Table** [`document`] — `docs/architecture/initial-schema.md`
- **graph_graphs Table** [`document`] — `docs/architecture/initial-schema.md`
- **version_snapshots Table** [`document`] — `docs/architecture/initial-schema.md`
- **ws_workspaces Table** [`document`] — `docs/architecture/initial-schema.md`
- **React Flow** [`document`] — `docs/architecture/tech-stack.md`
- **Zustand** [`document`] — `docs/architecture/tech-stack.md`
- **TraversalPath** [`document`] — `docs/architecture/traceability-model.md`
- **TraversalResult** [`document`] — `docs/architecture/traceability-model.md`
- **TraversalService** [`document`] — `docs/architecture/traceability-model.md`

## Internal Edges

  - EdgeRenderer --references--> EdgeTypeRegistry [EXTRACTED 0.95]
  - EdgeTypeRegistry --references--> graph_edges Table [INFERRED 0.78]
  - Graph Slice --shares_data_with--> graph_edges Table [INFERRED 0.82]
  - useCanvasStore --implements--> Graph Slice [EXTRACTED 1.0]
  - useCanvasStore --implements--> Selection Slice [EXTRACTED 1.0]
  - useCanvasStore --implements--> Execution Slice [EXTRACTED 1.0]
  - useCanvasStore --implements--> Viewport Slice [EXTRACTED 1.0]
  - useCanvasStore --implements--> Workspace Slice [EXTRACTED 1.0]
  - WorkspaceCanvas --references--> NodeRenderer [EXTRACTED 0.95]
  - WorkspaceCanvas --references--> EdgeRenderer [EXTRACTED 0.95]
  - WorkspaceCanvas --references--> useCanvasStore [EXTRACTED 1.0]
  - graph_graphs Table --shares_data_with--> graph_edges Table [EXTRACTED 1.0]
  - graph_graphs Table --shares_data_with--> version_snapshots Table [EXTRACTED 1.0]
  - ws_workspaces Table --shares_data_with--> graph_graphs Table [EXTRACTED 1.0]
  - React Flow --references--> WorkspaceCanvas [EXTRACTED 0.95]
  - Zustand --references--> useCanvasStore [EXTRACTED 0.95]
  - TraversalService --references--> graph_edges Table [EXTRACTED 0.95]
  - TraversalService --references--> TraversalResult [EXTRACTED 1.0]
  - TraversalService --references--> TraversalPath [EXTRACTED 1.0]

## Cross-Community Edges

  - Execution Slice --references--> ExecuteNodeUseCase [INFERRED] (`docs/architecture/node-execution-model.md`)
  - Graph Slice --shares_data_with--> node_nodes Table [INFERRED] (`docs/architecture/initial-schema.md`)
  - graph_graphs Table --shares_data_with--> node_nodes Table [EXTRACTED] (`docs/architecture/initial-schema.md`)

[Back to index](index.md)
