# Estimation Domain

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
