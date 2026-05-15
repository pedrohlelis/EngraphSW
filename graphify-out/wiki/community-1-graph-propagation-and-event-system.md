# Graph Propagation & Event System

**Community 1** · 22 nodes · cohesion=0.10

## Nodes

- **Artifact Lifecycle (Created, Active, Stale, Retired)** [`rationale`] — `docs/domain/engineering-artifact-taxonomy.md`
- **ADR-004 Event-Driven Communication (referenced by Dep. Propagation)** [`document`] — `docs/architecture/dependency-propagation.md`
- **Batch Propagation Semantics** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **Cycle Detection (DAG Enforcement)** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **Downstream Propagation (Forward Traversal)** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **Invalidation Cascade** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **Dependency Propagation Engine** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **Recalculation Model** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **Node Staleness Mechanism** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **Typed Edge Semantics (referenced by Dependency Propagation)** [`document`] — `docs/architecture/dependency-propagation.md`
- **Upstream Traversal (Reverse Traversal)** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **Phase 1B — Specification Hardening & Implementation Readiness** [`document`] — `docs/roadmap/PHASE_1B_SPECIFICATION.md`
- **Stream 1 — Graph Health Closure** [`rationale`] — `docs/roadmap/PHASE_1B_SPECIFICATION.md`
- **Stream 2 — Missing Critical Documents** [`rationale`] — `docs/roadmap/PHASE_1B_SPECIFICATION.md`
- **Stream 3 — ADR Completion (ADR-006, ADR-007)** [`rationale`] — `docs/roadmap/PHASE_1B_SPECIFICATION.md`
- **Stream 4 — Architecture Specification Hardening** [`rationale`] — `docs/roadmap/PHASE_1B_SPECIFICATION.md`
- **Phase 2 — System Architecture** [`document`] — `docs/roadmap/PHASE_2_SYSTEM_ARCHITECTURE.md`
- **Base Node System (BaseNode, NodeDefinition, ExecutionContext)** [`rationale`] — `docs/roadmap/PHASE_4_NODE_FRAMEWORK.md`
- **Execution Engine (Phase 4)** [`rationale`] — `docs/roadmap/PHASE_4_NODE_FRAMEWORK.md`
- **Phase 4 — Node Framework** [`document`] — `docs/roadmap/PHASE_4_NODE_FRAMEWORK.md`
- **Node Execution States (idle, running, success, failed, stale)** [`rationale`] — `docs/roadmap/PHASE_4_NODE_FRAMEWORK.md`
- **Rollback Semantics** [`rationale`] — `docs/architecture/versioning-strategy.md`

## Internal Edges

  - Artifact Lifecycle (Created, Active, Stale, Retired) --semantically_similar_to--> Node Staleness Mechanism [INFERRED 0.95]
  - Invalidation Cascade --conceptually_related_to--> Node Staleness Mechanism [EXTRACTED 1.0]
  - Dependency Propagation Engine --conceptually_related_to--> Node Staleness Mechanism [EXTRACTED 1.0]
  - Dependency Propagation Engine --conceptually_related_to--> Invalidation Cascade [EXTRACTED 1.0]
  - Dependency Propagation Engine --conceptually_related_to--> Recalculation Model [EXTRACTED 1.0]
  - Dependency Propagation Engine --conceptually_related_to--> Cycle Detection (DAG Enforcement) [EXTRACTED 1.0]
  - Dependency Propagation Engine --conceptually_related_to--> Batch Propagation Semantics [EXTRACTED 1.0]
  - Dependency Propagation Engine --conceptually_related_to--> Downstream Propagation (Forward Traversal) [EXTRACTED 1.0]
  - Dependency Propagation Engine --conceptually_related_to--> Upstream Traversal (Reverse Traversal) [EXTRACTED 1.0]
  - Dependency Propagation Engine --references--> Typed Edge Semantics (referenced by Dependency Propagation) [EXTRACTED 1.0]
  - Dependency Propagation Engine --references--> ADR-004 Event-Driven Communication (referenced by Dep. Propagation) [EXTRACTED 1.0]
  - Phase 1B — Specification Hardening & Implementation Readiness --references--> Phase 2 — System Architecture [EXTRACTED 1.0]
  - Phase 1B — Specification Hardening & Implementation Readiness --conceptually_related_to--> Stream 1 — Graph Health Closure [EXTRACTED 1.0]
  - Phase 1B — Specification Hardening & Implementation Readiness --conceptually_related_to--> Stream 2 — Missing Critical Documents [EXTRACTED 1.0]
  - Phase 1B — Specification Hardening & Implementation Readiness --conceptually_related_to--> Stream 3 — ADR Completion (ADR-006, ADR-007) [EXTRACTED 1.0]
  - Phase 1B — Specification Hardening & Implementation Readiness --conceptually_related_to--> Stream 4 — Architecture Specification Hardening [EXTRACTED 1.0]
  - Stream 4 — Architecture Specification Hardening --references--> Dependency Propagation Engine [EXTRACTED 1.0]
  - Phase 2 — System Architecture --references--> Dependency Propagation Engine [EXTRACTED 1.0]
  - Phase 2 — System Architecture --references--> Phase 4 — Node Framework [INFERRED 0.85]
  - Phase 4 — Node Framework --conceptually_related_to--> Base Node System (BaseNode, NodeDefinition, ExecutionContext) [EXTRACTED 1.0]
  - Phase 4 — Node Framework --conceptually_related_to--> Execution Engine (Phase 4) [EXTRACTED 1.0]
  - Phase 4 — Node Framework --conceptually_related_to--> Node Execution States (idle, running, success, failed, stale) [EXTRACTED 1.0]
  - Node Execution States (idle, running, success, failed, stale) --semantically_similar_to--> Node Staleness Mechanism [INFERRED 0.95]
  - Rollback Semantics --conceptually_related_to--> Invalidation Cascade [INFERRED 0.85]

## Cross-Community Edges

  - Dependency Propagation Engine --conceptually_related_to--> Propagation Boundaries (Hard and Soft) [EXTRACTED] (`docs/architecture/dependency-propagation.md`)
  - Phase 1B — Specification Hardening & Implementation Readiness --conceptually_related_to--> Stream 5 — Domain Specification Hardening [EXTRACTED] (`docs/roadmap/PHASE_1B_SPECIFICATION.md`)
  - Stream 4 — Architecture Specification Hardening --references--> Typed Edge Semantics Document [EXTRACTED] (`docs/domain/typed-edge-semantics.md`)
  - Phase 4 — Node Framework --references--> Phase 5 — Initial SDLC Nodes [EXTRACTED] (`docs/roadmap/PHASE_5_SDLC_NODES.md`)

[Back to index](index.md)
