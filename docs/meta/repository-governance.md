# Repository Governance

## Related Documents

* [AI Role Separation](ai-role-separation.md) — defines the behavioral boundary between orchestrator and implementer roles
* [Orchestration Model](orchestration-model.md) — defines the operational flows through which governance is applied
* [Information Boundaries](../../.claude/instructions/information-boundaries.md) — defines which governance documents are visible to each role

---

## Purpose

This document defines the governance system of the EngraphSW repository.

It does not define operational instructions or AI behavioral guidelines.

It defines:

* the authority model governing all decisions made inside the repository
* the mechanics of phase-gated execution
* the mechanics of architectural decision records
* the escalation protocol for governance conflicts
* the stability rules that protect the constitutional layer
* the cognitive layering map of repository documents

This document belongs to the orchestration layer.

It is not intended for routine implementation-time reasoning.

---

## Governance Layers

The repository is organized into discrete cognitive layers.

Each layer has distinct authority, scope, and intended audience.

### Layer 1 — Constitutional

**Documents:** `CLAUDE.md`

**Purpose:** Defines permanent repository identity, architectural philosophy, product constraints, and engineering doctrine.

**Authority:** Highest. Constitutional documents override all other documents.

**Stability:** Must evolve slowly and deliberately. Changes require explicit user decision.

**Audience:** All agents, all roles.

---

### Layer 2 — Governance

**Documents:** `docs/meta/`

**Purpose:** Defines how the repository governs itself — authority models, escalation paths, role separation, orchestration philosophy, and cognitive layering.

**Authority:** Below the constitution. Defines the rules under which all other layers operate.

**Stability:** High. Changes require orchestrator review and user approval.

**Audience:** Orchestrator. Not for routine implementer consultation.

---

### Layer 3 — Architecture

**Documents:** `docs/adr/`, `docs/architecture/`

**Purpose:** Records accepted architectural decisions and defines system design. Governs all implementation work.

**Authority:** Below governance. Implementation must comply with accepted ADRs and architecture documents.

**Stability:** Moderate. ADRs are accepted once and changed only through new ADRs. Architecture documents evolve with the system.

**Audience:** Implementer, orchestrator.

---

### Layer 4 — Domain

**Documents:** `docs/domain/`

**Purpose:** Defines SDLC artifact semantics, domain vocabulary, and engineering domain concepts.

**Authority:** Defines the semantic layer that both architecture and implementation must respect.

**Stability:** Moderate. Evolves as domain understanding deepens.

**Audience:** Implementer, orchestrator.

---

### Layer 5 — Roadmap

**Documents:** `docs/roadmap/`

**Purpose:** Defines phase sequencing, deliverables, and active execution scope.

**Authority:** Defines the scope boundary for all implementation activity. The active phase document is the only authority for current implementation scope.

**Stability:** Sequential. Phase documents are stable once written. Only `CURRENT_PHASE.md` changes, and only through explicit user phase advancement.

**Audience:** Implementer, orchestrator.

---

### Layer 6 — Operational

**Documents:** `.claude/agents/`, `.claude/instructions/`, `.claude/skills/`

**Purpose:** Defines AI role behaviors, operational constraints, and specialized engineering expertise.

**Authority:** Below all prior layers. Operational behavior must comply with governance and architecture.

**Stability:** Moderate. Evolves as AI workflow needs change.

**Audience:** AI agents (role-specific).

---

## Authority Hierarchy

When a conflict exists between layers, authority is resolved in this order:

```
User
|
Constitutional Layer (CLAUDE.md)
|
Governance Layer (docs/meta/)
|
Architecture Layer (docs/adr/, docs/architecture/)
|
Domain Layer (docs/domain/)
|
Roadmap Layer (docs/roadmap/)
|
Operational Layer (.claude/)
|
Implementer
```

No lower layer may override a higher layer.

Implementation convenience does not override architecture.

Architecture does not override governance.

Governance does not override the constitution.

---

## Phase Gate Authority

### Who Controls Phase Advancement

Phase advancement is the exclusive authority of the user.

No AI agent may autonomously advance or declare a phase complete.

### How Phases Advance

1. The active phase is declared complete by the user.
2. The user explicitly authorizes advancement to the next phase.
3. `CURRENT_PHASE.md` is updated to reflect the new active phase.
4. The orchestrator acknowledges the phase change and updates reasoning accordingly.

### Phase Scope Enforcement

The active phase document defines the only authorized implementation scope.

Future phase documents may be read for architectural awareness.

Future phase documents must not be implemented early.

Phase scope enforcement is a governance constraint. It cannot be overridden by implementation necessity or architectural convenience.

### Phase Completion Assessment

The orchestrator is responsible for assessing phase completion criteria.

Assessment must reference the completion criteria defined in the active phase document.

The orchestrator may identify gaps and surface them for user review.

The orchestrator must not declare phases complete without user confirmation.

---

## ADR Authority

### What ADRs Govern

Architecture Decision Records (ADRs) capture accepted architectural decisions.

Once accepted, an ADR governs all implementation work affected by that decision.

Implementation must comply with accepted ADRs.

### When an ADR Is Required

An ADR is required when:

* a fundamental architectural approach is chosen
* a significant architectural alternative is rejected
* an existing architectural decision is changed
* a new bounded context or system boundary is introduced
* an integration strategy with external systems is decided

An ADR is not required for:

* implementation details within an established architecture
* library choices within an established technology stack
* UI patterns within established visual direction

### ADR Acceptance

ADRs are accepted through explicit user decision.

The orchestrator may draft ADRs for review.

An ADR in draft status does not yet govern implementation.

An ADR with status Accepted is binding.

### Changing an Accepted ADR

Accepted ADRs may only be changed by:

* creating a new superseding ADR
* explicitly marking the old ADR as superseded

ADRs must never be silently modified after acceptance.

---

## Escalation Protocol

### When Escalation Is Required

An escalation is required when:

* implementation work appears to conflict with a governance constraint
* an implementation decision has architectural implications that exceed current phase scope
* a governance document is ambiguous or internally inconsistent
* the active ADR set does not cover a significant implementation decision

### How to Escalate

1. The implementer surfaces the conflict explicitly — it must not be resolved silently.
2. The implementer does not reinterpret governance autonomously.
3. The orchestrator reviews the conflict against the authority hierarchy.
4. If unresolved, the conflict is escalated to the user for decision.
5. The user's decision is recorded in a new ADR or governance document update as appropriate.

### What Must Never Happen

* Governance must not be rewritten to resolve implementation conflicts.
* Implementation shortcuts must not silently override architectural constraints.
* Phase scope must not expand through implicit decisions.
* Authority must not be assumed — it must be traceable.

---

## Constitutional Stability Rules

The following elements of the repository may not change without explicit deliberate decision:

* product identity (EngraphSW as a graph-native SDLC engineering platform)
* core architectural approach (modular monolith, graph as system of record)
* engineering doctrine (strong typing, bounded contexts, composability)
* visual philosophy (dark-first, restrained, engineering-focused)
* AI philosophy (explainable, auditable, reviewable)
* phase gate authority (user holds exclusive advancement authority)

These are constitutional truths defined in `CLAUDE.md`.

They evolve slowly and only through user decision.

No ADR, architecture document, or implementation pressure overrides them.

---

## Governance Evolution

Governance documents may evolve as the repository matures.

Evolution is permitted when:

* the repository has genuinely changed in ways that make existing governance inaccurate
* new architectural decisions require new governance structures
* the orchestrator identifies a gap or integrity failure in the governance system

Evolution is NOT permitted when:

* implementation pressure drives governance changes
* convenience shortcuts require governance reinterpretation
* AI agents seek to expand their own operational scope

All governance document changes must be intentional, traceable, and documented.

---

## Governance Integrity Checks

The governance system maintains integrity when:

* all documents referenced in governance exist
* no document contradicts a higher-layer document
* phase boundaries are enforced and traceable
* ADR status reflects actual architectural decisions
* escalation paths are clear and actionable

Integrity failures must be surfaced to the orchestrator and resolved before implementation advances.
