# Graph Report - .  (2026-05-15)

## Corpus Check
- Corpus is ~44,559 words - fits in a single context window. You may not need a graph.

## Summary
- 199 nodes · 257 edges · 27 communities (13 shown, 14 thin omitted)
- Extraction: 81% EXTRACTED · 19% INFERRED · 0% AMBIGUOUS · INFERRED: 50 edges (avg confidence: 0.84)
- Token cost: 0 input · 0 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Core Architecture & Governance|Core Architecture & Governance]]
- [[_COMMUNITY_Graph Propagation & Event System|Graph Propagation & Event System]]
- [[_COMMUNITY_Canvas State & Rendering|Canvas State & Rendering]]
- [[_COMMUNITY_Architecture Decisions & Canvas Tech|Architecture Decisions & Canvas Tech]]
- [[_COMMUNITY_SDLC Artifact Domain Model|SDLC Artifact Domain Model]]
- [[_COMMUNITY_Platform Module Structure|Platform Module Structure]]
- [[_COMMUNITY_Graph Edge Semantics|Graph Edge Semantics]]
- [[_COMMUNITY_Export & Reporting System|Export & Reporting System]]
- [[_COMMUNITY_Brand Identity & Design Language|Brand Identity & Design Language]]
- [[_COMMUNITY_Core Artifact Schema|Core Artifact Schema]]
- [[_COMMUNITY_AI Intelligence Layer|AI Intelligence Layer]]
- [[_COMMUNITY_Repository Cognition & Meta|Repository Cognition & Meta]]
- [[_COMMUNITY_AI Operation Lifecycle|AI Operation Lifecycle]]
- [[_COMMUNITY_AI Provider Architecture|AI Provider Architecture]]
- [[_COMMUNITY_Frontend Framework|Frontend Framework]]
- [[_COMMUNITY_Constitutional Document|Constitutional Document]]
- [[_COMMUNITY_Database Skill|Database Skill]]
- [[_COMMUNITY_Event-Driven ADR|Event-Driven ADR]]
- [[_COMMUNITY_AI Traceability|AI Traceability]]
- [[_COMMUNITY_AI Estimation|AI Estimation]]
- [[_COMMUNITY_AI Audit Table|AI Audit Table]]
- [[_COMMUNITY_Event Outbox Table|Event Outbox Table]]
- [[_COMMUNITY_Persistence Strategy|Persistence Strategy]]
- [[_COMMUNITY_Testing Framework|Testing Framework]]
- [[_COMMUNITY_Naming Conventions|Naming Conventions]]
- [[_COMMUNITY_Active Phase Tracker|Active Phase Tracker]]
- [[_COMMUNITY_Phase 3 Canvas|Phase 3 Canvas]]

## God Nodes (most connected - your core abstractions)
1. `Typed Edge Semantics Document` - 16 edges
2. `Dependency Propagation Engine` - 14 edges
3. `node_nodes Table` - 13 edges
4. `Zustand` - 10 edges
5. `Engineering Artifact Taxonomy` - 10 edges
6. `EngraphSW Repository Constitution` - 8 edges
7. `Graph-Native SDLC Engineering Platform` - 7 edges
8. `useCanvasStore` - 7 edges
9. `Execution Module` - 7 edges
10. `Dependency Propagation` - 6 edges

## Surprising Connections (you probably didn't know these)
- `Graph as System of Record` --conceptually_related_to--> `Graph-Native SDLC Engineering Platform`  [INFERRED]
  docs/adr/003-graph-system-of-record.md → .claude/CLAUDE.md
- `Traceability Intelligence` --conceptually_related_to--> `Engineering Artifacts as First-Class Entities`  [INFERRED]
  docs/adr/003-graph-system-of-record.md → .claude/CLAUDE.md
- `Execution Slice` --references--> `ExecuteNodeUseCase`  [INFERRED]
  docs/architecture/canvas-engine.md → docs/architecture/node-execution-model.md
- `EngraphSW Orchestrator Agent` --conceptually_related_to--> `Artifact vs Task Boundary`  [INFERRED]
  .claude/agents/orchestrator.md → docs/domain/engineering-artifact-taxonomy.md
- `Artifact Lifecycle (Created, Active, Stale, Retired)` --semantically_similar_to--> `Node Staleness Mechanism`  [INFERRED] [semantically similar]
  docs/domain/engineering-artifact-taxonomy.md → docs/architecture/dependency-propagation.md

## Hyperedges (group relationships)
- **AI Agent Governance System: Orchestrator + Implementer + Information Boundaries** — agent_orchestrator, agent_implementer, instruction_information_boundaries [EXTRACTED 0.95]
- **Core Graph Architecture: Graph-as-Record + Plugin Nodes + Event-Driven Communication** — adr_003_graph_system_of_record, adr_002_plugin_node, adr_004_event_driven [EXTRACTED 0.95]
- **Graph-Node-Traceability Core Platform Concepts** — concept_graph_as_system_of_record, concept_node_system, concept_traceability, concept_dependency_propagation [INFERRED 0.85]
- **Propagation Engine instantiated by Typed Edge Semantics over Artifact Taxonomy** — dep_prop_propagation_engine, typed_edge_semantics, artifact_taxonomy [EXTRACTED 0.95]
- **Phase 5 SDLC Nodes implement Artifact Taxonomy Categories with Typed Edge Relationships** — phase5_sdlc_nodes, artifact_taxonomy, typed_edge_semantics [INFERRED 0.85]
- **Phase 1B hardens Architecture Docs, Domain Docs, and ADRs for Phase 2 readiness** — phase1b_specification, phase2_system_architecture, artifact_taxonomy [EXTRACTED 0.90]

## Communities (27 total, 14 thin omitted)

### Community 0 - "Core Architecture & Governance"
Cohesion: 0.15
Nodes (25): ADR-003 Graph as System of Record, EngraphSW Implementer Agent, EngraphSW Repository Constitution, AI-Native Engineering Philosophy, Bounded Contexts, Dependency Propagation, Engineering Artifacts as First-Class Entities, Event-Driven Internal Communication (+17 more)

### Community 1 - "Graph Propagation & Event System"
Cohesion: 0.1
Nodes (22): Artifact Lifecycle (Created, Active, Stale, Retired), ADR-004 Event-Driven Communication (referenced by Dep. Propagation), Batch Propagation Semantics, Cycle Detection (DAG Enforcement), Downstream Propagation (Forward Traversal), Invalidation Cascade, Dependency Propagation Engine, Recalculation Model (+14 more)

### Community 2 - "Canvas State & Rendering"
Cohesion: 0.11
Nodes (19): EdgeRenderer, EdgeTypeRegistry, Execution Slice, Graph Slice, NodeRenderer, Selection Slice, useCanvasStore, Viewport Slice (+11 more)

### Community 3 - "Architecture Decisions & Canvas Tech"
Cohesion: 0.13
Nodes (18): ADR-001: Modular Monolith Architecture, ADR-002: Plugin-Oriented Node Architecture, ADR-006: Canvas and Visualization Technology, Canvas Module (modules/canvas/), Cytoscape.js, D3.js, React Flow (XYFlow), ADR-007: Client State Management (+10 more)

### Community 4 - "SDLC Artifact Domain Model"
Cohesion: 0.14
Nodes (18): Engineering Artifact Taxonomy, Platform Capability Domains (5 Domains), Context Artifacts Category (Persona, Stakeholder), Graph Semantic Principles (Artifacts as Nodes), Planning Artifacts Category (WBS, Estimation), Requirements Artifacts Category (FR, User Story), Risk Artifacts Category (Risk Analysis), Stream 5 — Domain Specification Hardening (+10 more)

### Community 5 - "Platform Module Structure"
Cohesion: 0.19
Nodes (16): exec_execution_logs Table, AI Module, Execution Module, Graph Module, Nodes Module, Shared Module, Traceability Module, NodeCreatedEvent (+8 more)

### Community 6 - "Graph Edge Semantics"
Cohesion: 0.18
Nodes (14): Propagation Boundaries (Hard and Soft), CONTAINS Edge Type (WBS Hierarchy), CONTEXTUALIZES Edge Type, DERIVED_FROM Edge Type, ESTIMATES Edge Type, EXPOSES Edge Type, Edge Five Dimensions (Semantic, Endpoints, Direction, Propagation, Traceability), IMPACTS Edge Type (+6 more)

### Community 7 - "Export & Reporting System"
Cohesion: 0.17
Nodes (13): Export Pipelines, Report Generation Architecture, Export System Architecture, Reusable Export Templates, Traceability Model (referenced by Export System), Phase 10 — Exports & Reporting, Phase 8 — Collaboration, Liveblocks Realtime Integration (+5 more)

### Community 8 - "Brand Identity & Design Language"
Cohesion: 0.17
Nodes (12): Artifact vs Task Boundary, AI Brand Philosophy (Transparent Engineering Assistant), Color Philosophy (Amber Accent + Semantic Colors), Dark-First Interface Theme, EngraphSW Brand Guidelines, Node Design Philosophy (Engineering-First), UI Language Guidelines (referenced by Brand Guidelines), Visual Identity Philosophy (+4 more)

### Community 9 - "Core Artifact Schema"
Cohesion: 0.53
Nodes (9): Estimation Artifact, Requirement Artifact, exec_staleness_records Table, node_nodes Table, Persona Artifact, Risk Artifact, Stakeholder Artifact, Prisma ORM (+1 more)

### Community 10 - "AI Intelligence Layer"
Cohesion: 0.33
Nodes (7): AI Audit Record, Graph-Aware Context Construction, Domain AI Logic Layer, AI Graph Reasoning, AI Infrastructure Layer (ai/ module), Prompt Template Registry, AI Provider Strategy

### Community 11 - "Repository Cognition & Meta"
Cohesion: 0.57
Nodes (7): AI Role Separation, Graph Maintenance Guide, Orchestration Model, Phase 1A: Repository Foundation and Cognitive Stabilization, Repository Cognition, Repository Governance, EngraphSW UI Language Guidelines

### Community 12 - "AI Operation Lifecycle"
Cohesion: 0.67
Nodes (3): AI Analysis Operation, AI Generation Operation, AI Output Lifecycle

## Knowledge Gaps
- **86 isolated node(s):** `CLAUDE.md Constitutional Document`, `Phase-Gated Execution Control`, `Prisma Postgres Patterns Skill`, `ADR-002: Plugin-Oriented Node Architecture`, `ADR-004 Event-Driven Communication` (+81 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **14 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `Dependency Propagation Engine` connect `Graph Propagation & Event System` to `Graph Edge Semantics`, `Export & Reporting System`?**
  _High betweenness centrality (0.067) - this node is a cross-community bridge._
- **Why does `Engineering Artifact Taxonomy` connect `SDLC Artifact Domain Model` to `Brand Identity & Design Language`, `Graph Propagation & Event System`, `Graph Edge Semantics`?**
  _High betweenness centrality (0.066) - this node is a cross-community bridge._
- **Why does `Typed Edge Semantics Document` connect `Graph Edge Semantics` to `Graph Propagation & Event System`, `SDLC Artifact Domain Model`?**
  _High betweenness centrality (0.064) - this node is a cross-community bridge._
- **Are the 8 inferred relationships involving `node_nodes Table` (e.g. with `Graph Slice` and `NodeDefinition Interface`) actually correct?**
  _`node_nodes Table` has 8 INFERRED edges - model-reasoned connections that need verification._
- **Are the 5 inferred relationships involving `Zustand` (e.g. with `Graph State Slice` and `Selection State Slice`) actually correct?**
  _`Zustand` has 5 INFERRED edges - model-reasoned connections that need verification._
- **What connects `CLAUDE.md Constitutional Document`, `Phase-Gated Execution Control`, `Prisma Postgres Patterns Skill` to the rest of the system?**
  _86 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Graph Propagation & Event System` be split into smaller, more focused modules?**
  _Cohesion score 0.1 - nodes in this community are weakly interconnected._