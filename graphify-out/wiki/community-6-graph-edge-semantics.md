# Graph Edge Semantics

**Community 6** · 14 nodes · cohesion=0.18

## Nodes

- **Propagation Boundaries (Hard and Soft)** [`rationale`] — `docs/architecture/dependency-propagation.md`
- **CONTAINS Edge Type (WBS Hierarchy)** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **CONTEXTUALIZES Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **DERIVED_FROM Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **ESTIMATES Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **EXPOSES Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **Edge Five Dimensions (Semantic, Endpoints, Direction, Propagation, Traceability)** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **IMPACTS Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **IMPLEMENTS Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **INFLUENCES Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **MITIGATES Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **REFINES Edge Type** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **TRACES_TO Edge Type (General Traceability)** [`rationale`] — `docs/domain/typed-edge-semantics.md`
- **Typed Edge Semantics Document** [`document`] — `docs/domain/typed-edge-semantics.md`

## Internal Edges

  - Propagation Boundaries (Hard and Soft) --rationale_for--> DERIVED_FROM Edge Type [INFERRED 0.85]
  - Propagation Boundaries (Hard and Soft) --rationale_for--> REFINES Edge Type [INFERRED 0.85]
  - Propagation Boundaries (Hard and Soft) --rationale_for--> CONTEXTUALIZES Edge Type [INFERRED 0.85]
  - Propagation Boundaries (Hard and Soft) --rationale_for--> IMPLEMENTS Edge Type [INFERRED 0.85]
  - Typed Edge Semantics Document --conceptually_related_to--> Edge Five Dimensions (Semantic, Endpoints, Direction, Propagation, Traceability) [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> DERIVED_FROM Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> REFINES Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> CONTEXTUALIZES Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> INFLUENCES Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> IMPLEMENTS Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> CONTAINS Edge Type (WBS Hierarchy) [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> ESTIMATES Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> EXPOSES Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> IMPACTS Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> MITIGATES Edge Type [EXTRACTED 1.0]
  - Typed Edge Semantics Document --conceptually_related_to--> TRACES_TO Edge Type (General Traceability) [EXTRACTED 1.0]

## Cross-Community Edges

  - Propagation Boundaries (Hard and Soft) --conceptually_related_to--> Invalidation Cascade [EXTRACTED] (`docs/architecture/dependency-propagation.md`)
  - Typed Edge Semantics Document --references--> Dependency Propagation Engine [EXTRACTED] (`docs/architecture/dependency-propagation.md`)
  - Typed Edge Semantics Document --references--> Engineering Artifact Taxonomy [EXTRACTED] (`docs/domain/engineering-artifact-taxonomy.md`)

[Back to index](index.md)
