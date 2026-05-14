# Architecture Review Skill

## Purpose

Ensure all architectural decisions align with the platform’s long-term modular, graph-oriented, AI-native engineering goals.

This skill should activate whenever:
- new modules are created
- dependencies are introduced
- folder structure changes
- graph logic changes
- major refactors occur
- infrastructure decisions are made

---

# Architectural Philosophy

The platform is:
- a graph-based SDLC workspace
- an engineering knowledge graph
- an AI-native CASE platform

The architecture must prioritize:
- extensibility
- modularity
- graph consistency
- domain separation
- execution scalability
- traceability

---

# Architectural Constraints

Use:
- modular monolith architecture
- domain-driven design principles
- event-driven internal communication

Avoid:
- premature microservices
- tight coupling
- shared mutable global logic
- giant utility folders
- monolithic components

---

# Review Checklist

Validate:
- bounded context integrity
- module isolation
- event-driven communication
- dependency direction correctness
- scalability implications
- graph consistency implications

Check for:
- hidden coupling
- duplicated domain logic
- improper cross-module imports
- graph model leakage
- infrastructure bleeding into domain logic

---

# Preferred Architecture Style

Favor:
- composition over inheritance
- interfaces over implementation coupling
- domain services over utility sprawl
- explicit contracts over implicit assumptions

---

# Important Rules

The graph model and node engine are the most critical parts of the platform.

Protect:
- node extensibility
- graph consistency
- execution integrity
- traceability semantics

When uncertain:
favor long-term extensibility over short-term implementation convenience.