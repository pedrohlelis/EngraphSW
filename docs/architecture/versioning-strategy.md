# Versioning Strategy

## Related Documents

* [Architecture: Persistence Strategy](persistence-strategy.md) — defines the append-only execution history and soft-deletion rules that underpin version durability
* [Architecture: Dependency Propagation](dependency-propagation.md) — rollback triggers invalidation cascades; versioning must coordinate with propagation semantics
* [Architecture: Traceability Model](traceability-model.md) — version events should be traceable through the graph alongside artifact relationships
* [Initial Schema](initial-schema.md) — defines the `version_snapshots` table that persists snapshot records
* [Phase 9 — Versioning & History](../roadmap/PHASE_9_VERSIONING.md) — the implementation phase that delivers this strategy

---

## Purpose

Define how artifact revisions are created, compared, restored, and audited across the workspace.

## Revision Lifecycle

- every meaningful artifact change can create a new revision
- revisions should preserve prior state instead of overwriting history
- revision metadata should capture author, timestamp, source action, and relationship context

## Rollback Semantics

- rollback restores a previous revision as a deliberate action
- rollback should create a new revision entry rather than erasing history
- dependent artifacts may require invalidation or recomputation after rollback; rollback triggers an [Invalidation Cascade](dependency-propagation.md) through downstream graph nodes

## Snapshot Strategy

- snapshots capture artifact state at a point in time for auditability and recovery
- snapshots may be full or incremental depending on artifact type
- snapshot storage should support comparison and restoration workflows

The `version_snapshots` table that implements snapshot persistence is defined in [Initial Schema](initial-schema.md).

## Auditability

- version history should explain what changed and why
- revision diffs should be available where meaningful
- version events should be traceable through the graph and execution logs

## AI-Assisted Behavior

- AI may summarize revision deltas and highlight risky changes
- AI suggestions must reference concrete revision evidence
- AI-generated revisions should remain attributable and reviewable

## Future Extensibility

- support branch-like revision paths if needed later
- support artifact comparison reports
- support restore, fork, and promotion workflows
- support version lineage across connected nodes and derived artifacts
