# Risk Analysis Domain

## Related Documents

* [Domain: Engineering Artifact Taxonomy](engineering-artifact-taxonomy.md) — classifies risk artifacts within the broader artifact hierarchy
* [Domain: Typed Edge Semantics](typed-edge-semantics.md) — defines the edge types used in the edge mapping table below

---

## Purpose

Define how delivery, scope, and dependency risks are represented as structured engineering artifacts.

## Artifact Semantics

- risks should capture category, severity, likelihood, impact, and mitigation context
- risk records should remain analyzable and traceable across the graph
- the model should support both identified risks and inferred or emerging concerns

## Relationships

- risks connect to requirements, estimates, stakeholders, personas, and execution dependencies
- relationships should explain what creates the risk and what the risk may affect
- mitigation links should preserve the rationale behind recommended actions

## Behaviors

- identify, classify, score, and update risks
- support mitigation planning and risk ownership context
- propagate awareness when source artifacts change materially

## Traceability Rules

- risk rationale should remain linked to source evidence and impacted artifacts
- stale risk assessments should be visible when project context changes
- mitigation status should remain auditable over time

## AI-Assisted Behaviors

- AI may suggest risks, severity levels, or mitigations from project context
- AI may highlight hidden dependency risks or inconsistent assumptions
- AI-generated risk insight should be reviewable and attributable

## Future Extensibility

- support risk registers and portfolio-level reporting later
- support quantitative risk models and scenario analysis
- support cross-node impact analysis and mitigation tracking

---

## Field Schema

The following fields are stored as JSONB in the [`node_nodes`](../architecture/initial-schema.md) table's `content` column, for nodes of type `risk`. See [Initial Schema — node_nodes](../architecture/initial-schema.md) for the full table definition.

| Field | Type | Required | Constraints | Notes |
|---|---|---|---|---|
| `title` | String | Yes | Non-empty | Short risk label |
| `description` | String | Yes | Non-empty | Full description of the risk |
| `category` | Enum | Yes | `Technical` \| `Schedule` \| `Scope` \| `Resource` \| `External` \| `Compliance` | Risk classification |
| `likelihood` | Enum | Yes | `High` \| `Medium` \| `Low` | Probability of the risk materializing |
| `impact` | Enum | Yes | `Critical` \| `High` \| `Medium` \| `Low` | Severity of consequences if risk materializes |
| `severity` | Enum | Yes | `Critical` \| `High` \| `Medium` \| `Low` | Composite score (computed from likelihood + impact) |
| `status` | Enum | Yes | `Identified` \| `Mitigated` \| `Accepted` \| `Closed`; default: `Identified` | Risk lifecycle state |
| `mitigation` | String | No | — | Planned or enacted mitigation strategy |
| `owner` | String | No | — | Person or team responsible for managing this risk |
| `version` | Int | Yes | >= 1; default: 1 | Increments on content update |

---

## Edge Type Mapping

| Edge Type | Source | Target | Propagation | Notes |
|---|---|---|---|---|
| `IMPACTS` | Risk | Requirement | Soft (stale signal) | Risk threatens delivery or validity of this requirement |
| `IMPACTS` | Risk | Estimation | Soft (stale signal) | Risk may invalidate the accuracy of this estimate |
| `IMPACTS` | Risk | UserStory | Soft (stale signal) | Risk threatens delivery of this story |
| `MITIGATES` | Requirement | Risk | None | This requirement addresses or reduces this risk |
| `MITIGATES` | UserStory | Risk | None | This story delivers mitigation for this risk |
| `DERIVED_FROM` | Risk | Risk | Soft (stale signal) | Risk derived from or elaborating on another risk |
| `INFLUENCES` | Stakeholder | Risk | None | Stakeholder concern that surfaces or shapes this risk |

Direction convention: edge points from Source to Target as listed. Propagation column follows the contract in [Typed Edge Semantics](typed-edge-semantics.md).
