# Export & Reporting System

**Community 7** · 13 nodes · cohesion=0.17

## Nodes

- **Export Pipelines** [`rationale`] — `docs/architecture/export-system.md`
- **Report Generation Architecture** [`rationale`] — `docs/architecture/export-system.md`
- **Export System Architecture** [`document`] — `docs/architecture/export-system.md`
- **Reusable Export Templates** [`rationale`] — `docs/architecture/export-system.md`
- **Traceability Model (referenced by Export System)** [`document`] — `docs/architecture/export-system.md`
- **Phase 10 — Exports & Reporting** [`document`] — `docs/roadmap/PHASE_10_EXPORTS.md`
- **Phase 8 — Collaboration** [`document`] — `docs/roadmap/PHASE_8_COLLABORATION.md`
- **Liveblocks Realtime Integration** [`rationale`] — `docs/roadmap/PHASE_8_COLLABORATION.md`
- **Artifact Snapshots & Rollback Operations** [`rationale`] — `docs/roadmap/PHASE_9_VERSIONING.md`
- **Phase 9 — Versioning & History** [`document`] — `docs/roadmap/PHASE_9_VERSIONING.md`
- **Revision Lifecycle** [`rationale`] — `docs/architecture/versioning-strategy.md`
- **Snapshot Strategy** [`rationale`] — `docs/architecture/versioning-strategy.md`
- **Versioning Strategy** [`document`] — `docs/architecture/versioning-strategy.md`

## Internal Edges

  - Export System Architecture --references--> Traceability Model (referenced by Export System) [EXTRACTED 1.0]
  - Export System Architecture --references--> Phase 10 — Exports & Reporting [EXTRACTED 1.0]
  - Export System Architecture --conceptually_related_to--> Report Generation Architecture [EXTRACTED 1.0]
  - Export System Architecture --conceptually_related_to--> Export Pipelines [EXTRACTED 1.0]
  - Export System Architecture --conceptually_related_to--> Reusable Export Templates [EXTRACTED 1.0]
  - Phase 10 — Exports & Reporting --references--> Export System Architecture [EXTRACTED 1.0]
  - Phase 8 — Collaboration --conceptually_related_to--> Liveblocks Realtime Integration [EXTRACTED 1.0]
  - Phase 8 — Collaboration --references--> Phase 9 — Versioning & History [EXTRACTED 1.0]
  - Artifact Snapshots & Rollback Operations --semantically_similar_to--> Snapshot Strategy [INFERRED 0.95]
  - Phase 9 — Versioning & History --conceptually_related_to--> Artifact Snapshots & Rollback Operations [EXTRACTED 1.0]
  - Phase 9 — Versioning & History --references--> Phase 10 — Exports & Reporting [EXTRACTED 1.0]
  - Phase 9 — Versioning & History --semantically_similar_to--> Versioning Strategy [INFERRED 0.95]
  - Versioning Strategy --conceptually_related_to--> Revision Lifecycle [EXTRACTED 1.0]
  - Versioning Strategy --conceptually_related_to--> Snapshot Strategy [EXTRACTED 1.0]

## Cross-Community Edges

  - Versioning Strategy --references--> Dependency Propagation Engine [EXTRACTED] (`docs/architecture/dependency-propagation.md`)
  - Versioning Strategy --conceptually_related_to--> Rollback Semantics [EXTRACTED] (`docs/architecture/versioning-strategy.md`)

[Back to index](index.md)
