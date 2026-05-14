# Domain Driven Design Skill

## Purpose

Maintain clear domain boundaries and business modeling consistency.

---

# Core Philosophy

The platform models software engineering knowledge.

Business concepts must be treated as first-class domain entities.

---

# Bounded Contexts

Examples:
- workspace
- graph
- nodes
- requirements
- estimation
- traceability
- AI
- collaboration

---

# Domain Modeling Rules

Favor:
- rich domain models
- explicit domain services
- event-driven interactions
- ubiquitous language

Avoid:
- anemic models
- utility-driven architecture
- leaking infrastructure into domain logic

---

# Aggregates

Protect aggregate boundaries.

Avoid:
- uncontrolled cross-module mutation
- implicit coupling

---

# Events

Prefer domain events for:
- execution propagation
- dependency invalidation
- artifact lifecycle changes

---

# Repository Rules

Repositories should:
- encapsulate persistence concerns
- avoid business logic leakage
- expose domain-friendly operations