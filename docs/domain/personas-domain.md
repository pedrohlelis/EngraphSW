# Personas Domain

## Related Documents

* [Domain: Engineering Artifact Taxonomy](engineering-artifact-taxonomy.md) — classifies personas within the broader artifact hierarchy
* [Domain: Typed Edge Semantics](typed-edge-semantics.md) — defines the edge types used in the edge mapping table below

---

## Purpose

Define how user personas are modeled as reusable engineering context for requirements and story design.

## Artifact Semantics

- personas represent archetypes, not isolated user records
- each persona should capture goals, frustrations, behaviors, and relevant context
- persona data should remain structured enough for analysis and downstream generation

## Relationships

- personas connect to requirements, user stories, stakeholders, and risks
- relationships should express influence, relevance, and validation context
- persona links should help explain why an artifact exists or how it should behave

## Behaviors

- create, refine, version, and retire personas
- cluster or segment personas by project context
- support persona-derived artifact generation and validation

## Traceability Rules

- personas should be traceable to the assumptions and evidence that shaped them
- stories and requirements derived from personas should retain that lineage
- persona updates may invalidate dependent narratives or assumptions

## AI-Assisted Behaviors

- AI may generate draft personas from domain inputs and project context
- AI may surface missing attributes, contradictions, or weak assumptions
- AI outputs should be reviewable, editable, and attributable

## Future Extensibility

- support deeper behavioral models and persona variants
- support evidence-backed persona confidence levels
- support cross-project persona reuse where appropriate

---

## Field Schema

The following fields are stored as JSONB in the [node_nodes Table](../architecture/initial-schema.md) table's `content` column, for nodes of type `persona`. See [Initial Schema — node_nodes](../architecture/initial-schema.md) for the full table definition.

| Field | Type | Required | Constraints | Notes |
|---|---|---|---|---|
| `name` | String | Yes | Non-empty | Persona archetype name (e.g., "Technical Lead") |
| `role` | String | Yes | Non-empty | Job title or functional role |
| `bio` | String | No | — | Short narrative description |
| `goals` | String[] | Yes | Non-empty array | What this persona is trying to accomplish |
| `frustrations` | String[] | No | May be empty array | Pain points and friction areas |
| `behaviors` | String[] | No | May be empty array | Characteristic behaviors and patterns |
| `demographics` | Object | No | Free-form JSONB | Optional demographic or contextual attributes |
| `version` | Int | Yes | >= 1; default: 1 | Increments on content update |

---

## Edge Type Mapping

| Edge Type | Source | Target | Propagation | Notes |
|---|---|---|---|---|
| `CONTEXTUALIZES` | Persona | Requirement | None | Persona provides context for understanding this requirement |
| `CONTEXTUALIZES` | Persona | UserStory | None | Persona provides context for this story |
| `INFLUENCES` | Stakeholder | Persona | None | Stakeholder whose input shaped this persona definition |
| `DERIVED_FROM` | Persona | Stakeholder | Soft (stale signal) | Persona derived from stakeholder analysis |

Direction convention: edge points from Source to Target as listed. Propagation column follows the contract in [Typed Edge Semantics](typed-edge-semantics.md).
