# User Stories Domain

## Related Documents

* [Domain: Engineering Artifact Taxonomy](engineering-artifact-taxonomy.md) — classifies user stories within the broader artifact hierarchy
* [Domain: Typed Edge Semantics](typed-edge-semantics.md) — defines the edge types used in the edge mapping table below

---

## Purpose

Define how user stories are modeled as traceable delivery-oriented expressions of domain intent.

## Artifact Semantics

- user stories express user value in a concise, structured format
- stories should preserve the relationship between intent, context, and acceptance criteria
- story artifacts should remain analyzable for refinement and estimation

## Relationships

- stories connect to functional requirements, personas, stakeholders, and estimates
- relationships should express derivation, coverage, and prioritization context
- stories may also link to risks or execution dependencies when relevant

## Behaviors

- generate, edit, split, merge, and prioritize stories
- map stories back to requirements and personas
- support completeness checks and refinement workflows

## Traceability Rules

- every story should explain which upstream artifacts justify it
- story changes should remain visible to dependent estimation and analysis nodes
- derived stories should preserve provenance and rationale

## AI-Assisted Behaviors

- AI may suggest stories from requirements and persona context
- AI may refine story wording, acceptance criteria, and prioritization cues
- AI-generated stories must remain human-reviewable and traceable

## Future Extensibility

- support story maps, epics, and larger delivery structures later
- support consistency checks across story sets
- support story-level analytics and coverage reporting

---

## Field Schema

The following fields are stored as JSONB in the [node_nodes Table](../architecture/initial-schema.md) table's `content` column, for nodes of type `user-story`. See [Initial Schema — node_nodes](../architecture/initial-schema.md) for the full table definition.

| Field | Type | Required | Constraints | Notes |
|---|---|---|---|---|
| `title` | String | Yes | Non-empty | Short display label |
| `roleDescription` | String | Yes | Non-empty | "As a [role]" — who this story serves |
| `goal` | String | Yes | Non-empty | "I want to [goal]" — what the user wants |
| `benefit` | String | Yes | Non-empty | "So that [benefit]" — why this matters |
| `acceptanceCriteria` | String[] | No | May be empty array | Testable conditions for story completion |
| `priority` | Enum | No | `Must` \| `Should` \| `Could` \| `Wont` (MoSCoW) | Delivery priority |
| `status` | Enum | Yes | `Draft` \| `Active` \| `Done` \| `Archived`; default: `Draft` | Lifecycle state |
| `storyPoints` | Int | No | >= 0 | Estimated complexity in story points |
| `version` | Int | Yes | >= 1; default: 1 | Increments on content update |

---

## Edge Type Mapping

| Edge Type | Source | Target | Propagation | Notes |
|---|---|---|---|---|
| `DERIVED_FROM` | UserStory | Requirement | Soft (stale signal) | Requirement this story was derived from |
| `REFINES` | UserStory | UserStory | Soft (stale signal) | Larger story that this one breaks down or refines |
| `CONTEXTUALIZES` | Persona | UserStory | None | Persona whose context shapes this story |
| `TRACES_TO` | UserStory | Requirement | None | Explicit traceability assertion to a requirement |
| `ESTIMATES` | Estimation | UserStory | Soft (stale signal) | Estimation artifact covering this story |
| `IMPACTS` | Risk | UserStory | Soft (stale signal) | Risk that threatens delivery of this story |
| `MITIGATES` | UserStory | Risk | None | This story addresses or mitigates a risk |

Direction convention: edge points from Source to Target as listed. Propagation column follows the contract in [Typed Edge Semantics](typed-edge-semantics.md).
