# Node Design Skill

## Purpose

Standardize all node implementations across the platform.

The node system is the core abstraction of the application.

---

# Node Philosophy

Nodes are not simple UI cards.

Nodes are:
- engineering artifacts
- execution units
- graph entities
- dependency-aware objects
- traceability-aware objects

Nodes may behave as:
- static artifact containers
- processors
- calculators
- AI generators
- validation engines

---

# Standard Node Structure

Every node must define:
- metadata
- schema
- inputs
- outputs
- renderer
- execution logic
- validation rules
- execution states

---

# Required Node Concerns

Each node implementation must consider:
- persistence
- validation
- execution
- dependency propagation
- stale state detection
- graph relationships
- traceability

---

# Execution Model

Nodes must support:
- manual execution
- dependency-triggered execution
- async execution
- recalculation

Execution states:
- idle
- running
- success
- failed
- stale

---

# Visual State Requirements

Nodes should visually communicate:
- selected
- executing
- stale
- failed
- dependency-invalidated
- AI-generated

---

# Important Rules

Avoid:
- hardcoded node behavior
- node-specific framework hacks
- tightly coupled node logic

Favor:
- reusable abstractions
- plugin-oriented architecture
- strongly typed node contracts
- explicit node metadata and port definitions
- consistent execution and validation contracts
- node-specific UI that still honors shared graph semantics

## Heuristics

- treat each node as a domain object with behavior, not just a visual component
- keep execution logic separate from presentation when possible
- make dependency invalidation explicit instead of inferred from UI state
- prefer registration-based node discovery over scattered ad hoc wiring