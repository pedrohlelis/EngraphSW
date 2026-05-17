# Estimation Domain

## Related Documents

* [Domain: Engineering Artifact Taxonomy](engineering-artifact-taxonomy.md) — classifies estimation artifacts within the broader artifact hierarchy
* [Domain: Typed Edge Semantics](typed-edge-semantics.md) — defines the edge types used in the edge mapping table below

---

## Purpose

Define how engineering effort, complexity, and cost are represented as structured analytical artifacts.

## Artifact Semantics

- estimation artifacts should be derived from source requirements and related graph context
- estimates may represent function points, complexity, effort, cost, or confidence bands
- the model should remain structured enough for recalculation and comparison

## Relationships

- estimates connect to requirements, stories, personas, risks, and stakeholder scope
- relationships should explain what drives the estimate and what depends on it
- estimation links must support both source attribution and downstream impact analysis

## Behaviors

- compute, recalculate, compare, and version estimates
- capture assumptions, drivers, and calculation inputs
- propagate changes when upstream artifacts materially change

## Traceability Rules

- every estimate should retain the source artifacts used to derive it
- estimate updates should preserve prior calculations for auditability
- stale estimates should be identifiable when source context changes

## AI-Assisted Behaviors

- AI may assist with estimation heuristics, function point classification, and narrative explanations
- AI outputs should be tied to explicit inputs and assumptions
- AI-generated estimates should remain reviewable by domain experts

## Future Extensibility

- support multiple estimation methodologies
- support calibration data and historical comparisons
- support portfolio-level estimation analytics later

---

## Field Schema

The following fields are stored as JSONB in the [node_nodes Table](../architecture/initial-schema.md) table's `content` column, for nodes of type `estimation`. See [Initial Schema — node_nodes](../architecture/initial-schema.md) for the full table definition.

| Field | Type | Required | Constraints | Notes |
|---|---|---|---|---|
| `title` | String | Yes | Non-empty | Label for this estimation artifact |
| `methodology` | Enum | Yes | `FunctionPoint` \| `StoryPoint` \| `Complexity` \| `Custom` | Estimation approach used |
| `totalPoints` | Float | No | >= 0 | Computed or manually entered size value |
| `effortDays` | Float | No | >= 0 | Effort estimate in person-days |
| `costEstimate` | Float | No | >= 0 | Cost estimate (currency-agnostic; context set by workspace) |
| `confidence` | Enum | Yes | `High` \| `Medium` \| `Low` | How confident the estimate is in its inputs |
| `assumptions` | String[] | No | May be empty array | Key assumptions underpinning this estimate |
| `calculationInputs` | Object | No | Free-form JSONB | Structured inputs for the estimation methodology used |
| `notes` | String | No | — | Narrative context or caveats |
| `version` | Int | Yes | >= 1; default: 1 | Increments on content update |

---

## Edge Type Mapping

| Edge Type | Source | Target | Propagation | Notes |
|---|---|---|---|---|
| `ESTIMATES` | Estimation | Requirement | None | This estimation is scoped to that requirement |
| `ESTIMATES` | Estimation | UserStory | None | This estimation is scoped to that user story |
| `DERIVED_FROM` | Estimation | Estimation | Soft (stale signal) | Re-estimate derived from or superseding a prior one |
| `IMPACTS` | Risk | Estimation | Soft (stale signal) | Risk that may affect estimate accuracy or delivery |

Direction convention: edge points from Source to Target as listed. Propagation column follows the contract in [Typed Edge Semantics](typed-edge-semantics.md).
