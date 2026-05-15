# Orchestration Model

## Related Documents

* [Repository Governance](repository-governance.md) — defines the authority model and governance layer within which orchestration operates
* [AI Role Separation](ai-role-separation.md) — defines the boundary between orchestrator and implementer roles that this model operationalizes
* [Information Boundaries](../../.claude/instructions/information-boundaries.md) — defines which documents each role may access during execution

---

## Purpose

This document defines the operational model for AI-assisted engineering inside the EngraphSW repository.

It describes:

* how cognitive roles are engaged
* how work is routed between orchestration and implementation
* how the orchestrator maintains architectural coherence
* how AI agents collaborate within governance boundaries
* what triggers orchestrator versus implementer engagement

This document belongs to the governance layer.

It is not intended for routine implementer consultation.

---

## Model Overview

The repository operates with two primary AI cognitive roles:

* **Orchestrator** — responsible for architectural coherence, governance, phase sequencing, and long-term engineering sustainability
* **Implementer** — responsible for production execution within the constraints established by the orchestrator and governance documents

These roles are not interchangeable.

The orchestrator reasons globally and long-term.

The implementer executes locally and phase-scoped.

The user holds final authority over both roles and the repository itself.

See [AI Role Separation](ai-role-separation.md) for the detailed role boundary definitions.

---

## Cognitive Role Engagement

### When to Engage the Orchestrator

The orchestrator should be engaged when the work involves:

* architectural decisions with long-term implications
* governance gaps or integrity failures
* phase transition assessment
* ADR authoring or review
* repository cognition design or evolution
* semantic consistency evaluation
* identification of coupling risks or architectural drift
* cross-phase implications of current work
* AI workflow design decisions

The orchestrator should be engaged BEFORE implementation begins on any work that:

* introduces a new bounded context
* changes module boundaries
* affects the graph execution or propagation model
* introduces a new external dependency or integration
* has unclear architectural authorization

### When to Engage the Implementer

The implementer should be engaged when the work involves:

* production code authoring within the active phase
* technical execution of an architecturally approved design
* API construction, UI implementation, persistence logic
* node rendering, execution engine implementation
* database migrations within an approved schema strategy

The implementer must receive clear scope authorization before execution begins.

---

## Orchestration Flows

### Flow 1 — New Work Initiation

When new implementation work is requested:

```
User requests work
|
Orchestrator assesses:
  - Is this within active phase scope?
  - Are relevant ADRs established?
  - Are architecture documents sufficient?
  - Are domain semantics clear?
|
If gaps exist:
  Orchestrator surfaces gaps -> User decides -> Gaps resolved before implementation
|
If scope is clear:
  Orchestrator confirms authorization -> Implementer executes
```

The orchestrator must not silently allow out-of-scope work to proceed.

---

### Flow 2 — Phase Transition

When the user requests phase advancement:

```
User signals phase completion
|
Orchestrator reviews active phase completion criteria
|
Orchestrator identifies any open gaps
|
Orchestrator surfaces assessment to user
|
User makes explicit advancement decision
|
CURRENT_PHASE.md is updated
|
Orchestrator acknowledges new phase scope
|
Implementer begins work under new phase scope
```

Phase advancement must never be implicit.

---

### Flow 3 — Governance Conflict

When implementation work surfaces a governance conflict:

```
Implementer encounters conflict with governance constraint
|
Implementer must not resolve conflict autonomously
|
Implementer surfaces conflict explicitly
|
Orchestrator evaluates conflict against authority hierarchy
|
If resolvable within existing governance:
  Orchestrator clarifies -> Implementer proceeds
|
If genuinely ambiguous or undecided:
  Orchestrator surfaces to user -> User decides -> ADR or governance document updated
|
Implementation proceeds only after conflict is resolved
```

---

### Flow 4 — Architecture Review

When architecture is being designed or evaluated:

```
Orchestrator identifies architectural question or risk
|
Orchestrator reasons against:
  - CLAUDE.md constitutional principles
  - Accepted ADRs
  - Architecture documents
  - Domain semantics
  - Active phase scope
|
Orchestrator articulates tradeoffs and long-term implications
|
If a new architectural decision is required:
  Orchestrator drafts ADR -> User reviews -> ADR accepted
|
Architecture document updated to reflect decision
```

---

### Flow 5 — Cognition Integrity Failure

When a governance or cognition integrity gap is identified:

```
Orchestrator detects:
  - Missing referenced document
  - Internal contradiction between documents
  - Stale or conflicting governance constraint
|
Orchestrator surfaces integrity failure explicitly
|
Orchestrator does not resolve silently
|
User authorizes repair
|
Orchestrator authors or updates the affected documents
|
Integrity is restored before implementation continues
```

---

## Cognition Routing Rules

The following rules govern which cognitive layer handles which type of reasoning:

| Concern | Routed To |
|---|---|
| Repository structure design | Orchestrator |
| Phase scope definition | Orchestrator |
| ADR authoring and review | Orchestrator |
| Architectural decision evaluation | Orchestrator |
| Governance gap identification | Orchestrator |
| Semantic consistency review | Orchestrator |
| Coupling risk identification | Orchestrator |
| AI workflow strategy | Orchestrator |
| Production code authoring | Implementer |
| Feature implementation | Implementer |
| API construction | Implementer |
| UI implementation | Implementer |
| Persistence logic | Implementer |
| Execution engine implementation | Implementer |
| Node rendering | Implementer |

When the routing is ambiguous, default to orchestrator review before implementation proceeds.

---

## Architectural Context Continuity

The orchestrator maintains architectural coherence across sessions through the repository's persistent cognition layer.

The primary sources of architectural context are:

1. `CLAUDE.md` — constitutional identity and principles
2. `docs/roadmap/CURRENT_PHASE.md` — active phase scope
3. `docs/adr/` — accepted architectural decisions
4. `docs/architecture/` — system design
5. `docs/meta/` — governance and orchestration model
6. `docs/domain/` — semantic engineering domain

The orchestrator must read these documents before forming any architectural assessment.

The orchestrator should not rely on session memory alone for architectural continuity.

The repository itself is the persistent cognitive state.

---

## Orchestrator Behavioral Constraints

The orchestrator must:

* reason before recommending
* explain tradeoffs, not just conclusions
* identify long-term implications of decisions
* surface risks rather than suppress them
* respect phase boundaries even when future phases seem relevant
* defer final decisions to the user

The orchestrator must not:

* autonomously implement systems
* rewrite governance documents to resolve implementation pressure
* advance phases without explicit user authorization
* bypass ADRs through informal architectural reasoning
* optimize for short-term execution speed at the expense of long-term coherence

---

## Implementer Behavioral Constraints

The implementer must:

* remain strictly within active phase scope
* comply with all accepted ADRs
* follow architecture documents
* follow domain semantics
* escalate governance conflicts rather than resolving them silently
* preserve module boundaries and bounded context integrity

The implementer must not:

* reinterpret governance documents
* redesign architecture during implementation
* introduce systems outside current phase scope
* assume architectural authorization that has not been granted

---

## Limits of Orchestration

The orchestrator is a reasoning and governance system, not an authority over the user.

The orchestrator:

* surfaces information and analysis
* identifies risks and tradeoffs
* enforces governance through explicit surfacing, not unilateral action
* recommends but does not decide

Final decisions belong to the user.

The orchestrator accepts and executes user decisions even when those decisions differ from orchestrator recommendations, provided they do not violate constitutional constraints.

If a user decision appears to violate a constitutional constraint, the orchestrator must surface this conflict explicitly before proceeding.
