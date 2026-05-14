# React Flow Patterns Skill

## Purpose

Standardize scalable React Flow architecture patterns.

---

# Core Principles

The graph workspace must:
- scale to large graphs
- remain performant
- support extensibility
- support dynamic node types

---

# Architecture Guidelines

Separate:
- graph logic
- node rendering
- edge rendering
- execution logic
- persistence logic

---

# Recommended Patterns

Use:
- dynamic node registration
- reusable edge abstractions
- isolated graph stores
- memoized renderers
- viewport-aware rendering

---

# Performance Considerations

Optimize:
- rerender frequency
- graph traversal
- edge calculations
- node rendering

Avoid:
- unnecessary global state updates
- giant graph rerenders
- heavy node render logic

---

# Important Features

Support:
- minimap
- zoom/pan
- dynamic handles
- edge validation
- viewport persistence
- execution overlays

---

# Avoid

Avoid:
- hardcoded node mappings
- graph state in local component state
- UI-only graph modeling