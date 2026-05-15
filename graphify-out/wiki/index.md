# EngraphSW Knowledge Graph Wiki

Agent-crawlable index of all graph communities. Each article covers one community cluster.

**Graph:** 199 nodes, 293 edges, 27 communities

## Communities

- [Core Architecture & Governance](community-0-core-architecture-and-governance.md) — 25 nodes, cohesion=0.15
- [Graph Propagation & Event System](community-1-graph-propagation-and-event-system.md) — 22 nodes, cohesion=0.10
- [Canvas State & Rendering](community-2-canvas-state-and-rendering.md) — 19 nodes, cohesion=0.11
- [Architecture Decisions & Canvas Tech](community-3-architecture-decisions-and-canvas-tech.md) — 18 nodes, cohesion=0.13
- [SDLC Artifact Domain Model](community-4-sdlc-artifact-domain-model.md) — 18 nodes, cohesion=0.14
- [Platform Module Structure](community-5-platform-module-structure.md) — 16 nodes, cohesion=0.19
- [Graph Edge Semantics](community-6-graph-edge-semantics.md) — 14 nodes, cohesion=0.18
- [Export & Reporting System](community-7-export-and-reporting-system.md) — 13 nodes, cohesion=0.17
- [Brand Identity & Design Language](community-8-brand-identity-and-design-language.md) — 12 nodes, cohesion=0.17
- [Core Artifact Schema](community-9-core-artifact-schema.md) — 9 nodes, cohesion=0.53
- [AI Intelligence Layer](community-10-ai-intelligence-layer.md) — 7 nodes, cohesion=0.33
- [Repository Cognition & Meta](community-11-repository-cognition-and-meta.md) — 7 nodes, cohesion=0.57
- [AI Operation Lifecycle](community-12-ai-operation-lifecycle.md) — 3 nodes, cohesion=0.67
- [AI Provider Architecture](community-13-ai-provider-architecture.md) — 2 nodes, cohesion=1.00
- [Frontend Framework](community-14-frontend-framework.md) — 2 nodes, cohesion=1.00
- [Constitutional Document](community-15-constitutional-document.md) — 1 nodes, cohesion=1.00
- [Database Skill](community-16-database-skill.md) — 1 nodes, cohesion=1.00
- [Event-Driven ADR](community-17-event-driven-adr.md) — 1 nodes, cohesion=1.00
- [AI Traceability](community-18-ai-traceability.md) — 1 nodes, cohesion=1.00
- [AI Estimation](community-19-ai-estimation.md) — 1 nodes, cohesion=1.00
- [AI Audit Table](community-20-ai-audit-table.md) — 1 nodes, cohesion=1.00
- [Event Outbox Table](community-21-event-outbox-table.md) — 1 nodes, cohesion=1.00
- [Persistence Strategy](community-22-persistence-strategy.md) — 1 nodes, cohesion=1.00
- [Testing Framework](community-23-testing-framework.md) — 1 nodes, cohesion=1.00
- [Naming Conventions](community-24-naming-conventions.md) — 1 nodes, cohesion=1.00
- [Active Phase Tracker](community-25-active-phase-tracker.md) — 1 nodes, cohesion=1.00
- [Phase 3 Canvas](community-26-phase-3-canvas.md) — 1 nodes, cohesion=1.00

## God Nodes (High-Degree Hubs)

- **Typed Edge Semantics Document** (degree=16) — `docs/domain/typed-edge-semantics.md`
- **Dependency Propagation Engine** (degree=14) — `docs/architecture/dependency-propagation.md`
- **node_nodes Table** (degree=13) — `docs/architecture/initial-schema.md`
- **Zustand** (degree=10) — `docs/adr/007-state-management.md`
- **Engineering Artifact Taxonomy** (degree=10) — `docs/domain/engineering-artifact-taxonomy.md`
- **EngraphSW Repository Constitution** (degree=8) — `.claude/CLAUDE.md`
- **Graph-Native SDLC Engineering Platform** (degree=7) — `.claude/CLAUDE.md`
- **useCanvasStore** (degree=7) — `docs/architecture/canvas-engine.md`
- **Execution Module** (degree=7) — `docs/architecture/module-conventions.md`
- **Dependency Propagation** (degree=6) — `.claude/skills/graph-engineering/SKILL.md`

## Surprising Connections

- {'source': 'Graph as System of Record', 'target': 'Graph-Native SDLC Engineering Platform', 'source_files': ['docs/adr/003-graph-system-of-record.md', '.claude/CLAUDE.md'], 'confidence': 'INFERRED', 'relation': 'conceptually_related_to', 'why': 'inferred connection - not explicitly stated in source; connects across different repos/directories'}
- {'source': 'Traceability Intelligence', 'target': 'Engineering Artifacts as First-Class Entities', 'source_files': ['docs/adr/003-graph-system-of-record.md', '.claude/CLAUDE.md'], 'confidence': 'INFERRED', 'relation': 'conceptually_related_to', 'why': 'inferred connection - not explicitly stated in source; connects across different repos/directories'}
- {'source': 'Execution Slice', 'target': 'ExecuteNodeUseCase', 'source_files': ['docs/architecture/canvas-engine.md', 'docs/architecture/node-execution-model.md'], 'confidence': 'INFERRED', 'relation': 'references', 'why': 'inferred connection - not explicitly stated in source; bridges separate communities; peripheral node `Execution Slice` unexpectedly reaches hub `ExecuteNodeUseCase`'}
- {'source': 'EngraphSW Orchestrator Agent', 'target': 'Artifact vs Task Boundary', 'source_files': ['.claude/agents/orchestrator.md', 'docs/domain/engineering-artifact-taxonomy.md'], 'confidence': 'INFERRED', 'relation': 'conceptually_related_to', 'why': 'inferred connection - not explicitly stated in source; connects across different repos/directories'}
- {'source': 'Artifact Lifecycle (Created, Active, Stale, Retired)', 'target': 'Node Staleness Mechanism', 'source_files': ['docs/domain/engineering-artifact-taxonomy.md', 'docs/architecture/dependency-propagation.md'], 'confidence': 'INFERRED', 'relation': 'semantically_similar_to', 'why': 'inferred connection - not explicitly stated in source; semantically similar concepts with no structural link; peripheral node `Artifact Lifecycle (Created, Active, Stale, Retired)` unexpectedly reaches hub `Node Staleness Mechanism`'}