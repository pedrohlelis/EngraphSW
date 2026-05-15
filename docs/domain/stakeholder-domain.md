# Stakeholder Domain

## Related Documents

* [Domain: Engineering Artifact Taxonomy](engineering-artifact-taxonomy.md) — classifies stakeholders within the broader artifact hierarchy
* [Domain: Typed Edge Semantics](typed-edge-semantics.md) — defines the edge types used in the edge mapping table below

---

## Purpose

Define how stakeholders are represented as context-bearing actors that influence scope, priorities, and risk.

## Artifact Semantics

- stakeholders represent people, groups, or decision-making entities
- each stakeholder should capture role, influence, interest, and concern areas
- stakeholder data should be structured enough for planning and impact analysis

## Relationships

- stakeholders connect to requirements, risks, personas, and scope decisions
- relationships should express influence, accountability, and dependency context
- stakeholder links should clarify why a constraint or priority exists

## Behaviors

- register, update, and segment stakeholders
- model influence-interest or similar decision frameworks
- support stakeholder-driven analysis of scope and risk

## Traceability Rules

- stakeholder influence should be traceable to decisions, requirements, and risks
- changes in stakeholder context may affect prioritization and scope interpretation
- relationships should preserve rationale for downstream decisions

## AI-Assisted Behaviors

- AI may infer stakeholder groupings or missing relationships from project context
- AI may suggest concerns, influence patterns, or decision impacts
- AI outputs should remain explainable and editable

## Future Extensibility

- support richer stakeholder models and governance views
- support organizational hierarchy and decision lineage later
- support cross-project stakeholder reuse where relevant

---

## Field Schema

The following fields are stored as JSONB in the [`node_nodes`](../architecture/initial-schema.md) table's `content` column, for nodes of type `stakeholder`. See [Initial Schema — node_nodes](../architecture/initial-schema.md) for the full table definition.

| Field | Type | Required | Constraints | Notes |
|---|---|---|---|---|
| `name` | String | Yes | Non-empty | Stakeholder name or group name |
| `role` | String | Yes | Non-empty | Functional role or title |
| `organization` | String | No | — | Organization or team affiliation |
| `influence` | Enum | Yes | `High` \| `Medium` \| `Low` | Degree of influence over scope and decisions |
| `interest` | Enum | Yes | `High` \| `Medium` \| `Low` | Level of stake in project outcomes |
| `concerns` | String[] | No | May be empty array | Primary concerns and expectations |
| `contactInfo` | String | No | — | Contact reference (email, Slack handle, etc.) |
| `version` | Int | Yes | >= 1; default: 1 | Increments on content update |

---

## Edge Type Mapping

| Edge Type | Source | Target | Propagation | Notes |
|---|---|---|---|---|
| `INFLUENCES` | Stakeholder | Requirement | None | Stakeholder whose concern drives or shapes this requirement |
| `INFLUENCES` | Stakeholder | Persona | None | Stakeholder input that shaped a persona definition |
| `IMPACTS` | Risk | Stakeholder | None | Risk that may affect this stakeholder's interests |
| `CONTEXTUALIZES` | Stakeholder | Requirement | None | Stakeholder provides context for understanding a requirement |

Direction convention: edge points from Source to Target as listed. Propagation column follows the contract in [Typed Edge Semantics](typed-edge-semantics.md).
