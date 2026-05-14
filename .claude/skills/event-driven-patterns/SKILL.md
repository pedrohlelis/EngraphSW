# Event Driven Patterns Skill

## Purpose

Standardize internal event-driven communication.

---

# Event Philosophy

Modules should communicate through:
- domain events
- explicit contracts
- isolated workflows

Avoid:
- hidden module coupling
- direct dependency chains

---

# Event Naming

Use:
- RequirementCreatedEvent
- NodeExecutedEvent
- ArtifactVersionCreatedEvent

Favor:
- past-tense event names
- domain-oriented naming

---

# Event Rules

Events should:
- represent meaningful domain actions
- avoid leaking infrastructure details
- remain strongly typed

---

# Propagation

Use events for:
- dependency invalidation
- execution propagation
- traceability updates
- AI workflow triggers

---

# Avoid

Avoid:
- event spaghetti
- overly generic events
- synchronous dependency chains

---

# Important Considerations

Consider:
- event ordering
- idempotency
- retry behavior
- observability

## Heuristics

- use past-tense domain event names that describe completed actions
- keep payloads small, typed, and domain-focused
- use events to signal propagation, invalidation, and lifecycle changes rather than direct cross-module calls
- design handlers to be idempotent and safe to retry
- preserve causal history when one event triggers downstream recalculation or AI work
- avoid synchronous event chains that turn the modular monolith into hidden coupling