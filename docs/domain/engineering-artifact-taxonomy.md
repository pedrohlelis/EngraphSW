# Engineering Artifact Taxonomy

## Purpose

This document defines the domain constitution for the EngraphSW semantic graph.

It establishes:

* what qualifies as an engineering artifact in this system
* the artifact category system
* the platform's capability domains
* the relationship principles between artifact categories
* the structural distinction between artifacts and tasks
* the boundary that protects the system's semantic identity

This document is the authoritative reference for domain modeling decisions.

When a new artifact type is proposed, this document determines whether it belongs in the system, which category it occupies, and what relationships are permitted.

---

## Platform Capability Domains

EngraphSW is not a general-purpose canvas.

It is a semantic SDLC graph platform organized around five interconnected capability domains:

### 1. Requirements Engineering

Capturing, structuring, and tracing what a system must do and why.

Artifacts: Functional Requirements, User Stories, Personas, Stakeholders

### 2. Systems Modeling

Representing the dependencies, structures, and behaviors of engineering systems through graph relationships.

Artifacts: all node types participating in typed dependency and traceability edges

### 3. Project Planning Intelligence

Decomposing scope, anchoring estimation, and structuring delivery around engineering semantics — not task management.

Artifacts: WBS Packages, Estimation

### 4. Knowledge Graph Reasoning

Maintaining traceability, propagating impact, and enabling engineering intelligence across the full artifact graph.

Capabilities: typed edges, propagation engine, impact analysis, AI-assisted traversal

### 5. AI-Assisted Engineering

Augmenting artifact creation, analysis, and consistency checking through AI operations grounded in graph context.

Capabilities: AI-assisted generation, consistency analysis, traceability suggestions, estimation heuristics

These domains are not isolated features. They compose into a coherent semantic engineering workspace.

The graph is the medium through which they remain connected.

---

## What Is an Engineering Artifact

An engineering artifact is a structured domain entity that:

* represents a specific, identifiable concern within the SDLC
* carries stable identity across the lifecycle of the project
* participates in typed semantic relationships with other artifacts
* contributes to or is traceable to engineering decisions
* may be analyzed, validated, generated, or reasoned over
* has a lifecycle (created, active, stale, retired)

An engineering artifact is **not**:

* a transient note or comment
* an operational task assigned to a person
* a productivity or workflow record
* a generic data entry without semantic meaning
* a status indicator for team activity

The distinction is semantic, not visual.

A node that represents an engineering concern is an artifact.

A node that represents human work activity is not.

---

## Artifact Categories

### Category 1 — Context Artifacts

**Definition:** Represent the human, organizational, and behavioral context that gives requirements and decisions their meaning.

Context artifacts answer: *who* is affected, *who* has authority, and *why* a concern exists.

**Artifacts:**

| Artifact | Semantic Role |
|---|---|
| Persona | User archetype that provides behavioral and motivational context for requirements and story design |
| Stakeholder | Organizational actor that influences scope, priority, risk, and accountability |

**Characteristics:**
- typically upstream in the traceability graph
- provide context that justifies downstream requirements
- changes propagate downstream to requirements and stories
- do not directly produce deliverables

---

### Category 2 — Requirements Artifacts

**Definition:** Represent what the system must do or be, structured as traceable engineering statements.

Requirements artifacts answer: *what* the system must accomplish.

**Artifacts:**

| Artifact | Semantic Role |
|---|---|
| Functional Requirement | Structured statement of system capability, constraint, or behavioral expectation |
| User Story | Delivery-oriented expression of a requirement from a user perspective, grounded in acceptance criteria |

**Characteristics:**
- derive from context artifacts (personas, stakeholders)
- are primary sources for planning and estimation artifacts
- are the most fundamental traceability anchors in the graph
- changes propagate broadly downstream

---

### Category 3 — Planning Artifacts

**Definition:** Represent the decomposition, organization, and quantification of engineering scope and effort.

Planning artifacts answer: *how much*, *how organized*, and *how decomposed*.

**Artifacts:**

| Artifact | Semantic Role |
|---|---|
| WBS Package | Hierarchical decomposition of engineering scope into plannable delivery units |
| Estimation | Quantified representation of effort, complexity, or cost associated with a scope unit |

**Characteristics:**
- WBS packages form hierarchical structures (packages contain sub-packages)
- estimation anchors to WBS packages, requirements, or stories
- estimation aggregates upward through WBS hierarchy (bottom-up rollup)
- changes in upstream requirements propagate staleness into planning artifacts
- planning artifacts are not operational task systems

**Critical boundary:**

A WBS Package is a **scope decomposition artifact**, not a task management record.

It represents what needs to be built, not who is building it or when.

It must not accumulate: assignees, due dates, status workflows, comment threads, sprint assignments, or velocity metrics.

If a WBS node begins to look like a Jira ticket, it has semantically drifted.

---

### Category 4 — Risk Artifacts

**Definition:** Represent identified threats to engineering delivery, quality, scope, or system integrity.

Risk artifacts answer: *what could go wrong* and *what depends on getting it right*.

**Artifacts:**

| Artifact | Semantic Role |
|---|---|
| Risk Analysis | Structured record of an identified risk including severity, likelihood, impact scope, and mitigation context |

**Characteristics:**
- connect to requirements, WBS packages, estimates, and stakeholders
- represent impact relationships, not dependency relationships
- changes in connected artifacts may alter risk severity
- risk is bidirectional in impact: it affects and is affected by planning artifacts

---

### Future Artifact Categories

The following categories are architecturally anticipated but not currently in scope.

They are defined here to shape how the current taxonomy evolves without contradiction.

**Category 5 — Architectural Artifacts**

Represent system structure, component definitions, and technical decisions.

Anticipated artifacts: Architecture Decision Records as nodes, Component Definitions, Interface Contracts, System Boundaries

**Category 6 — Specification Artifacts**

Represent technical contracts produced by or derived from requirements.

Anticipated artifacts: API Specifications, Data Schemas, Interface Definitions, Behavior Contracts

**Category 7 — Verification Artifacts**

Represent evidence that requirements have been satisfied.

Anticipated artifacts: Test Plans, Acceptance Criteria Suites, Validation Records, Coverage Maps

These categories must be introduced through formal domain modeling when their phase arrives. They must not be prototyped informally during earlier phases.

---

## Artifact Inventory

The complete current artifact inventory:

| Artifact | Category | Module |
|---|---|---|
| Persona | Context | `personas/` |
| Stakeholder | Context | `stakeholders/` |
| Functional Requirement | Requirements | `requirements/` |
| User Story | Requirements | `user-stories/` |
| WBS Package | Planning | `wbs/` |
| Estimation | Planning | `estimation/` |
| Risk Analysis | Risk | `risk/` |

The `wbs/` module is registered here as the authoritative bounded context for WBS Package artifacts. Its implementation scope is defined in the relevant roadmap phase.

---

## Artifact Relationship Principles

The following principles govern which artifact categories may relate and in what directions.

Specific edge types, semantics, and propagation rules are defined in:

[docs/domain/typed-edge-semantics.md](typed-edge-semantics.md)

### General Principles

**Context -> Requirements**
Context artifacts provide the human and organizational grounding for requirements. Personas and stakeholders are upstream of requirements in the traceability graph.

**Requirements -> Planning**
Requirements are the primary source for WBS decomposition and estimation. A WBS package should be traceable to the requirements it implements.

**Requirements -> Risk**
Requirements can expose risks. Risks can challenge requirements. This relationship is bidirectional in semantic impact.

**Planning -> Planning (hierarchical)**
WBS packages may contain sub-packages forming a decomposition hierarchy. Estimation anchors to WBS packages and aggregates upward.

**Risk -> Planning**
Risks impact WBS packages and estimates. A risk that materializes may alter the scope or cost of associated planning artifacts.

**Any -> Any (traceability)**
Traceability edges may exist between artifacts of any category when the relationship is semantically justified. Traceability is not constrained to category boundaries.

### Prohibited Relationships

- Artifacts must not relate to each other through operational task semantics
- WBS packages must not relate to team members, sprints, or assignment records
- Context artifacts must not be used as execution targets

---

## The Artifact vs Task Boundary

This boundary is one of the most important constraints in the system.

It must be actively protected as the platform grows.

| Dimension | Artifact | Task |
|---|---|---|
| What it represents | An engineering concern | A unit of human work |
| Identity | Stable semantic entity | Operational record |
| Lifecycle | Created -> Active -> Stale -> Retired | Open -> In Progress -> Done |
| Primary relationships | Traceability, dependency, impact | Assignment, scheduling, status |
| Graph role | Node in the semantic graph | Row in a work tracking system |
| AI role | Analysis, generation, consistency | Automation, assignment, routing |
| Drift indicator | Gains assignees, due dates, status | — |

When a proposed feature adds operational workflow semantics to an artifact node, it must be evaluated against this table.

If it shifts the artifact toward the task column, it is scope drift.

---

## Artifact Lifecycle Principles

All engineering artifacts share a common lifecycle model:

**Created** — artifact exists and has initial content

**Active** — artifact is current and reflects known engineering context

**Stale** — artifact's content may no longer reflect upstream reality (triggered by propagation)

**Retired** — artifact is no longer active in the current scope but is preserved for traceability history

Artifacts are never permanently deleted from the semantic graph.

Retirement preserves the traceability history that depended on the artifact.

Hard deletion breaks traceability provenance and is not permitted for artifacts that have participated in relationships.

---

## Graph Semantic Principles

The following principles govern how artifacts participate in the graph:

**Artifacts are nodes.** Their semantic relationships are edges. The graph is the system of record for both.

**Edges carry meaning.** A connection between two artifacts is not a visual link — it is a typed semantic statement about their engineering relationship.

**Direction matters.** Every edge has a defined direction that reflects the flow of derivation, dependency, or impact.

**Traceability is first-class.** The graph must support upstream and downstream traversal across all artifact types at any time.

**Staleness is semantic.** A stale artifact is an engineering signal, not a system error. It means the artifact requires review or recalculation in light of upstream changes.

**AI operates on the graph.** AI assistance is grounded in the artifact graph — it reasons over existing nodes, edges, and traceability paths. It does not operate on isolated records.

---

## Taxonomy Evolution

This taxonomy evolves through deliberate domain modeling, not opportunistic feature addition.

A new artifact type may be introduced when:

* it represents a distinct engineering concern not covered by an existing artifact
* it has clear relationships to existing artifacts
* its category is identifiable within this taxonomy
* its module ownership is clear
* it does not introduce task management semantics

New artifact proposals must be evaluated against:

* the artifact definition (does it represent an engineering concern?)
* the artifact vs task boundary (does it remain on the artifact side?)
* the category system (which category does it belong to?)
* the relationship principles (what relationships are valid?)
* the module map (which module owns it?)

A proposed artifact that cannot be clearly placed within this taxonomy indicates either a new category is forming (which must be explicitly recognized) or the artifact does not belong in the system.
