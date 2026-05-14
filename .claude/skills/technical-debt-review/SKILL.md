# Technical Debt Review Skill

## Purpose

Continuously identify architectural drift and maintain long-term codebase quality.

---

# Review Goals

Detect:
- hidden coupling
- duplicated logic
- graph inconsistencies
- oversized modules
- scalability risks
- execution bottlenecks

---

# Review Areas

Analyze:
- domain boundaries
- graph architecture
- node complexity
- dependency propagation
- event architecture
- persistence patterns

---

# Important Questions

Check:
- does this scale?
- is this reusable?
- is this tightly coupled?
- does this preserve graph integrity?
- does this preserve node extensibility?

---

# Anti-Patterns

Watch for:
- giant utility folders
- business logic in UI
- hardcoded node behavior
- duplicated execution logic
- graph leakage across modules

---

# Technical Debt Philosophy

Favor:
- incremental architectural improvement
- explicit contracts
- modular abstractions
- maintainable complexity

Avoid:
- premature rewrites
- overengineering
- architectural drift