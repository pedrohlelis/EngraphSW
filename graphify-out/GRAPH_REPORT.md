# Graph Report - .  (2026-05-15)

## Corpus Check
- cluster-only mode — file stats not available

## Summary
- 242 nodes · 449 edges · 25 communities (13 shown, 12 thin omitted)
- Extraction: 85% EXTRACTED · 15% INFERRED · 0% AMBIGUOUS · INFERRED: 67 edges (avg confidence: 0.84)
- Token cost: 0 input · 0 output

## Graph Freshness
- Built from commit: `48f1503f`
- Run `git rev-parse HEAD` and compare to check if the graph is stale.
- Run `graphify update .` after code changes (no API cost).

## Community Hubs (Navigation)
- [[_COMMUNITY_Community 0|Community 0]]
- [[_COMMUNITY_Community 1|Community 1]]
- [[_COMMUNITY_Community 2|Community 2]]
- [[_COMMUNITY_Community 3|Community 3]]
- [[_COMMUNITY_Community 4|Community 4]]
- [[_COMMUNITY_Community 5|Community 5]]
- [[_COMMUNITY_Community 6|Community 6]]
- [[_COMMUNITY_Community 7|Community 7]]
- [[_COMMUNITY_Community 8|Community 8]]
- [[_COMMUNITY_Community 9|Community 9]]
- [[_COMMUNITY_Community 10|Community 10]]
- [[_COMMUNITY_Community 11|Community 11]]
- [[_COMMUNITY_Community 12|Community 12]]
- [[_COMMUNITY_Community 13|Community 13]]
- [[_COMMUNITY_Community 14|Community 14]]
- [[_COMMUNITY_Community 15|Community 15]]
- [[_COMMUNITY_Community 16|Community 16]]
- [[_COMMUNITY_Community 17|Community 17]]
- [[_COMMUNITY_Community 18|Community 18]]
- [[_COMMUNITY_Community 19|Community 19]]
- [[_COMMUNITY_Community 20|Community 20]]
- [[_COMMUNITY_Community 21|Community 21]]
- [[_COMMUNITY_Community 22|Community 22]]
- [[_COMMUNITY_Community 23|Community 23]]
- [[_COMMUNITY_Community 24|Community 24]]

## God Nodes (most connected - your core abstractions)
1. `Phase 1B: Specification Hardening` - 32 edges
2. `Typed Edge Semantics` - 25 edges
3. `Canvas Engine` - 20 edges
4. `Engineering Artifact Taxonomy` - 17 edges
5. `Dependency Propagation` - 16 edges
6. `Typed Edge Semantics Document` - 16 edges
7. `Dependency Propagation Engine` - 14 edges
8. `Node Execution Model` - 14 edges
9. `node_nodes Table` - 13 edges
10. `Phase 2 — System Architecture` - 12 edges

## Surprising Connections (you probably didn't know these)
- `Graph as System of Record` --conceptually_related_to--> `Graph-Native SDLC Engineering Platform`  [INFERRED]
  docs/adr/003-graph-system-of-record.md → .claude/CLAUDE.md
- `Traceability Intelligence` --conceptually_related_to--> `Engineering Artifacts as First-Class Entities`  [INFERRED]
  docs/adr/003-graph-system-of-record.md → .claude/CLAUDE.md
- `EngraphSW Orchestrator Agent` --references--> `Repository Cognition`  [INFERRED]
  .claude/agents/orchestrator.md → docs/meta/repository-cognition.md
- `Node Execution States (idle, running, success, failed, stale)` --semantically_similar_to--> `Node Staleness Mechanism`  [INFERRED] [semantically similar]
  docs/roadmap/PHASE_4_NODE_FRAMEWORK.md → docs/architecture/dependency-propagation.md
- `Typed Edge Semantics` --references--> `Dependency Propagation`  [EXTRACTED]
  docs/domain/typed-edge-semantics.md → .claude/skills/graph-engineering/SKILL.md

## Communities (25 total, 12 thin omitted)

### Community 0 - "Community 0"
Cohesion: 0.14
Nodes (32): Engineering Artifact Taxonomy, Graph Semantic Principles (Artifacts as Nodes), Risk Artifacts Category (Risk Analysis), Propagation Boundaries (Hard and Soft), CONTAINS Edge Type (WBS Hierarchy), CONTEXTUALIZES Edge Type, DERIVED_FROM Edge Type, ESTIMATES Edge Type (+24 more)

### Community 1 - "Community 1"
Cohesion: 0.13
Nodes (31): AI Role Separation, CURRENT_PHASE Document, Dependency Propagation Document, Graph Maintenance Guide, Graphify Graph Report, Initial Schema Document, Modular Boundaries, Module Conventions Document (+23 more)

### Community 2 - "Community 2"
Cohesion: 0.13
Nodes (25): Execution Slice, AI Module, Execution Module, Graph Module, Nodes Module, Shared Module, Traceability Module, NodeCreatedEvent (+17 more)

### Community 3 - "Community 3"
Cohesion: 0.18
Nodes (22): ADR-001: Modular Monolith Architecture, ADR-002: Plugin-Oriented Node Architecture, ADR-006: Canvas and Visualization Technology, ADR-007: Client State Management, ADR-006: Canvas Technology, Canvas Engine Architecture, Canvas Engine, EdgeRenderer (+14 more)

### Community 4 - "Community 4"
Cohesion: 0.13
Nodes (22): ADR-003 Graph as System of Record, Artifact Lifecycle (Created, Active, Stale, Retired), Dependency Propagation, Graph as System of Record, Traceability Intelligence, ADR-004 Event-Driven Communication (referenced by Dep. Propagation), Batch Propagation Semantics, Cycle Detection (DAG Enforcement) (+14 more)

### Community 5 - "Community 5"
Cohesion: 0.19
Nodes (19): EngraphSW Implementer Agent, EngraphSW Repository Constitution, AI-Native Engineering Philosophy, Bounded Contexts, Engineering Artifacts as First-Class Entities, Event-Driven Internal Communication, Graph-Native SDLC Engineering Platform, Layered Repository Cognition (+11 more)

### Community 6 - "Community 6"
Cohesion: 0.11
Nodes (18): Platform Capability Domains (5 Domains), Planning Artifacts Category (WBS, Estimation), Export Pipelines, Report Generation Architecture, Export System Architecture, Reusable Export Templates, Traceability Model (referenced by Export System), Phase 10 — Exports & Reporting (+10 more)

### Community 7 - "Community 7"
Cohesion: 0.29
Nodes (13): Estimation Artifact, Requirement Artifact, exec_execution_logs Table, exec_staleness_records Table, graph_graphs Table, node_nodes Table, version_snapshots Table, ws_workspaces Table (+5 more)

### Community 8 - "Community 8"
Cohesion: 0.17
Nodes (13): Canvas Module (modules/canvas/), Cytoscape.js, D3.js, React Flow (XYFlow), Execution State Slice, Graph State Slice, Jotai, React Context (+5 more)

### Community 9 - "Community 9"
Cohesion: 0.17
Nodes (12): Artifact vs Task Boundary, AI Brand Philosophy (Transparent Engineering Assistant), Color Philosophy (Amber Accent + Semantic Colors), Dark-First Interface Theme, EngraphSW Brand Guidelines, Node Design Philosophy (Engineering-First), UI Language Guidelines (referenced by Brand Guidelines), Visual Identity Philosophy (+4 more)

### Community 10 - "Community 10"
Cohesion: 0.22
Nodes (11): Context Artifacts Category (Persona, Stakeholder), Requirements Artifacts Category (FR, User Story), Base Node System (BaseNode, NodeDefinition, ExecutionContext), Execution Engine (Phase 4), Phase 4 — Node Framework, Node Execution States (idle, running, success, failed, stale), Functional Requirements Node, Personas Node (+3 more)

### Community 11 - "Community 11"
Cohesion: 0.33
Nodes (7): AI Audit Record, Graph-Aware Context Construction, Domain AI Logic Layer, AI Graph Reasoning, AI Infrastructure Layer (ai/ module), Prompt Template Registry, AI Provider Strategy

### Community 12 - "Community 12"
Cohesion: 0.67
Nodes (3): AI Analysis Operation, AI Generation Operation, AI Output Lifecycle

## Ambiguous Edges - Review These
- `ADR-006: Canvas and Visualization Technology` → `Phase 1B: Specification Hardening`  [AMBIGUOUS]
  docs/roadmap/PHASE_1B_SPECIFICATION.md · relation: references

## Knowledge Gaps
- **82 isolated node(s):** `CLAUDE.md Constitutional Document`, `Phase-Gated Execution Control`, `Prisma Postgres Patterns Skill`, `ADR-002: Plugin-Oriented Node Architecture`, `ADR-004 Event-Driven Communication` (+77 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **12 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **What is the exact relationship between `ADR-006: Canvas and Visualization Technology` and `Phase 1B: Specification Hardening`?**
  _Edge tagged AMBIGUOUS (relation: references) - confidence is low._
- **Why does `Phase 1B: Specification Hardening` connect `Community 1` to `Community 0`, `Community 2`, `Community 3`, `Community 4`, `Community 7`?**
  _High betweenness centrality (0.318) - this node is a cross-community bridge._
- **Why does `Dependency Propagation` connect `Community 4` to `Community 0`, `Community 1`, `Community 2`, `Community 5`?**
  _High betweenness centrality (0.260) - this node is a cross-community bridge._
- **Why does `Engineering Artifact Taxonomy` connect `Community 0` to `Community 1`, `Community 4`, `Community 6`, `Community 7`, `Community 9`, `Community 10`?**
  _High betweenness centrality (0.161) - this node is a cross-community bridge._
- **Are the 2 inferred relationships involving `Phase 1B: Specification Hardening` (e.g. with `Phase 2 — System Architecture` and `Phase 1A Repository Foundation`) actually correct?**
  _`Phase 1B: Specification Hardening` has 2 INFERRED edges - model-reasoned connections that need verification._
- **What connects `CLAUDE.md Constitutional Document`, `Phase-Gated Execution Control`, `Prisma Postgres Patterns Skill` to the rest of the system?**
  _82 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Community 0` be split into smaller, more focused modules?**
  _Cohesion score 0.14 - nodes in this community are weakly interconnected._