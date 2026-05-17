# Functional Requirements Domain

## Related Documents

* [Domain: Engineering Artifact Taxonomy](engineering-artifact-taxonomy.md) — classifies functional requirements within the broader artifact hierarchy
* [Domain: Typed Edge Semantics](typed-edge-semantics.md) — defines the edge types used in the edge mapping table below

---

## Purpose

Define how functional requirements are represented as structured engineering artifacts in the workspace graph.

## Artifact Semantics

- requirements are first-class domain objects, not free-form notes
- each requirement should support stable identity, category, priority, and acceptance criteria
- requirement content should remain structured enough for analysis, traceability, and generation

## Relationships

- requirements may relate to personas, user stories, stakeholders, risks, and estimates
- relationships should capture derivation, dependency, refinement, and coverage
- requirement links must be directional and queryable

## Behaviors

- create, edit, version, compare, and retire requirements
- classify requirements by type, priority, and scope
- propagate changes to dependent artifacts when relevant
- support requirement decomposition and consolidation

## Traceability Rules

- every requirement should be traceable back to its source context where possible
- downstream artifacts should reflect requirement changes explicitly
- traceability links should preserve provenance and impact context

## AI-Assisted Behaviors

- AI may draft, refine, classify, or validate requirements
- AI suggestions should remain grounded in existing domain context and traceability paths
- AI-generated requirements must remain reviewable and editable by the user

## Future Extensibility

- support richer requirement taxonomies over time
- support requirement quality checks and consistency analysis
- support cross-artifact requirement coverage reports

---

## Field Schema

The following fields are stored as JSONB in the [node_nodes Table](../architecture/initial-schema.md) table's `content` column, for nodes of type `requirement`. See [Initial Schema — node_nodes](../architecture/initial-schema.md) for the full table definition.

| Field | Type | Required | Constraints | Notes |
|---|---|---|---|---|
| `title` | String | Yes | Non-empty | Short name displayed on the node |
| `description` | String | Yes | Non-empty | Full requirement statement |
| `category` | Enum | Yes | `Functional` \| `NonFunctional` | Classifies the requirement type |
| `priority` | Enum | Yes | `Critical` \| `High` \| `Medium` \| `Low` | Delivery priority |
| `status` | Enum | Yes | `Draft` \| `Active` \| `Deprecated`; default: `Draft` | Lifecycle state |
| `acceptanceCriteria` | String[] | No | May be empty array | Testable conditions for completion |
| `source` | String | No | — | Where this requirement originated (stakeholder, session, etc.) |
| `version` | Int | Yes | >= 1; default: 1 | Increments on content update |

---

## Edge Type Mapping

The following table defines which typed edges connect functional requirements to other artifact types.

| Edge Type | Source | Target | Propagation | Notes |
|---|---|---|---|---|
| `DERIVED_FROM` | Requirement | Requirement | Soft (stale signal) | Parent requirement that this one was derived from |
| `REFINES` | Requirement | Requirement | Soft (stale signal) | Higher-level requirement that this one refines |
| `CONTEXTUALIZES` | Persona | Requirement | None | Persona that contextualizes this requirement |
| `CONTEXTUALIZES` | Stakeholder | Requirement | None | Stakeholder whose concern shapes this requirement |
| `TRACES_TO` | UserStory | Requirement | None | User story that explicitly traces to this requirement |
| `ESTIMATES` | Estimation | Requirement | Soft (stale signal) | Estimation artifact scoped to this requirement |
| `IMPACTS` | Risk | Requirement | Soft (stale signal) | Risk that may affect delivery of this requirement |
| `MITIGATES` | Requirement | Risk | None | This requirement addresses a known risk |

Direction convention: edge points from Source to Target as listed. Propagation column follows the contract in [Typed Edge Semantics](typed-edge-semantics.md).
