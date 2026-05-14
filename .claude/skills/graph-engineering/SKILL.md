# Graph Engineering Skill

## Purpose

Ensure graph architecture consistency across the platform.

The graph is the central system model.

---

# Graph Philosophy

The graph represents:
- engineering relationships
- execution dependencies
- traceability
- knowledge propagation

The graph is NOT just visual UI state.

---

# Core Graph Components

The graph contains:
- nodes
- edges
- handles/ports
- execution metadata
- dependency metadata
- traceability metadata

---

# Relationship Rules

Relationships must support:
- directional dependencies
- typed edges
- validation rules
- execution propagation

---

# Dependency Propagation

Changes in upstream nodes should:
- invalidate downstream dependencies
- mark stale execution states
- trigger recalculation workflows

---

# Important Considerations

Always consider:
- graph consistency
- cyclic dependencies
- execution ordering
- propagation scalability
- graph traversal efficiency

---

# Avoid

Avoid:
- graph logic inside UI components
- edge semantics hidden in render logic
- hardcoded relationship assumptions

---

# Favor

Favor:
- explicit graph contracts
- reusable graph services
- scalable traversal logic
- typed relationships