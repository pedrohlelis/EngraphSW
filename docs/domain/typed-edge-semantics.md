# Typed Edge Semantics

## Purpose

Define the semantic meaning, propagation behavior, validation rules, and traceability role of every edge type in the EngraphSW graph.

This document is the domain contract for graph relationships.

It instantiates the propagation mechanisms defined in:

[docs/architecture/dependency-propagation.md](../architecture/dependency-propagation.md)

For each edge type across the artifact set defined in:

[docs/domain/engineering-artifact-taxonomy.md](engineering-artifact-taxonomy.md)

---

## Edges Are Not Visual Connectors

This is the foundational distinction of the graph model.

An edge in EngraphSW is not a line drawn between two boxes on a canvas.

An edge is a **typed semantic statement** about the engineering relationship between two artifacts.

It asserts:

* what kind of relationship exists
* what direction that relationship flows
* what happens to the downstream artifact when the upstream artifact changes
* how this relationship participates in traceability and impact analysis
* what artifact types are valid at each endpoint

Every edge in the graph carries these five dimensions. They are not optional.

---

## Edge Dimensions

Every edge type in this document is defined across five dimensions:

**Semantic meaning** — what engineering relationship this edge type represents, stated in domain terms

**Valid endpoints** — which artifact categories or types may appear as source and target

**Directionality** — the direction the edge flows and what that direction means

**Propagation contract** — what happens to the target when the source changes; references the propagation boundary type (hard, soft, or none)

**Traceability role** — how this edge participates in upstream and downstream graph traversal

---

## Edge Type Catalog

---

### DERIVED_FROM

**Semantic meaning**

An artifact was created by deriving from or translating another artifact.

The source artifact is the origin of the derived artifact's content or intent. The derived artifact would not exist, or would have different content, if the source artifact did not exist or changed materially.

**Valid endpoints**

| Source (derived artifact) | Target (origin artifact) |
|---|---|
| User Story | Functional Requirement |
| Functional Requirement | Functional Requirement |

**Directionality**

The edge points **from the derived artifact toward its source**.

Reading the edge: "this artifact was derived from that artifact."

Upstream traversal (following DERIVED_FROM edges backward) reveals the chain of sources that justify the artifact's existence.

**Propagation contract**

When the source artifact changes materially:

* the derived artifact is marked **stale**
* staleness propagates downstream from the derived artifact through all edges it is the source of
* propagation boundary: **hard** — staleness cascades without pause

Rationale: a derived artifact is definitionally dependent on its source. If the source changes, the derived artifact may no longer accurately express the source's intent.

**Traceability role**

Primary upstream traceability link. The DERIVED_FROM chain is the principal path for provenance analysis — answering "what justifies this artifact?"

---

### REFINES

**Semantic meaning**

One requirement elaborates, constrains, or adds precision to another requirement.

The refining requirement does not replace the base requirement — it specializes or adds detail to it. Both artifacts remain active.

**Valid endpoints**

| Source (refining) | Target (being refined) |
|---|---|
| Functional Requirement | Functional Requirement |

**Directionality**

The edge points **from the refining artifact toward the artifact being refined**.

Reading the edge: "this requirement refines that requirement."

**Propagation contract**

When the base requirement changes:

* the refining requirement is marked **stale**
* propagation boundary: **soft** — the user is notified that the refinement may need review, but automatic cascade does not continue past the refining requirement without confirmation

Rationale: a refinement is dependent on its base, but the impact of base changes is more subtle than pure derivation. The user should assess whether the refinement remains coherent rather than having the staleness automatically cascade further.

**Traceability role**

Upstream refinement link. Used to trace the precision history of a requirement and understand the full specification of a base requirement through its refinement set.

---

### CONTEXTUALIZES

**Semantic meaning**

A context artifact — a persona or stakeholder — provides the human and behavioral context that grounds an artifact's existence and meaning.

The artifact was defined with this persona or stakeholder in mind. The persona or stakeholder's attributes, goals, and frustrations shape what the artifact must express.

**Valid endpoints**

| Source (context artifact) | Target (grounded artifact) |
|---|---|
| Persona | Functional Requirement |
| Persona | User Story |
| Stakeholder | Functional Requirement |

**Directionality**

The edge points **from the context artifact toward the artifact it contextualizes**.

Reading the edge: "this persona contextualizes that requirement."

**Propagation contract**

When the context artifact changes:

* the contextualized artifact is **not automatically marked stale**
* a **soft notification** is raised: the user is informed that a persona or stakeholder that contextualizes an artifact has changed, and should consider whether the artifact remains correctly grounded
* propagation boundary: **soft** — notification only, no automatic staleness cascade

Rationale: context artifacts influence rather than determine the content of their targets. A persona change may or may not affect a requirement — the user must make this judgment. Automatic staleness would produce excessive noise across the graph.

**Traceability role**

Upstream context link. Used in provenance analysis to explain why an artifact was defined the way it was. Used in impact analysis to understand which artifacts may need review when a persona or stakeholder changes.

---

### INFLUENCES

**Semantic meaning**

A stakeholder exerts authority, priority, or constraint influence over a planning or requirements artifact.

Unlike CONTEXTUALIZES — which provides behavioral context — INFLUENCES expresses organizational authority or constraint. A stakeholder's influence shapes scope, priority, or commitment decisions.

**Valid endpoints**

| Source | Target |
|---|---|
| Stakeholder | Functional Requirement |
| Stakeholder | WBS Package |
| Stakeholder | Risk Analysis |

**Directionality**

The edge points **from the stakeholder toward the artifact they influence**.

Reading the edge: "this stakeholder influences that artifact."

**Propagation contract**

When the stakeholder's scope of influence, priority weighting, or concern areas change:

* a **soft notification** is raised on all artifacts the stakeholder influences
* propagation boundary: **soft** — notification only

Rationale: stakeholder influence is a weight, not a deterministic dependency. Changes in stakeholder context warrant review, not automatic invalidation.

**Traceability role**

Authority and accountability link. Used in scope analysis to understand which stakeholders are responsible for which parts of the plan or requirements set. Used in impact analysis when stakeholder priorities shift.

---

### IMPLEMENTS

**Semantic meaning**

A WBS Package covers, addresses, or delivers the scope defined by a Functional Requirement.

This is the primary cross-layer traceability link between the requirements layer and the planning layer.

It asserts: "the work represented by this WBS package constitutes delivery against this requirement."

**Valid endpoints**

| Source (implementing artifact) | Target (being implemented) |
|---|---|
| WBS Package | Functional Requirement |
| WBS Package | User Story |

**Directionality**

The edge points **from the WBS Package toward the requirement or story it implements**.

Reading the edge: "this WBS package implements that requirement."

**Propagation contract**

When the requirement or story changes:

* the WBS Package is marked **stale**
* staleness propagates downstream from the WBS Package (to estimations anchored to it)
* propagation boundary: **hard**

When the WBS Package is re-executed or its scope is redefined:

* upstream traversal of IMPLEMENTS edges is used to verify that scope changes remain aligned with requirement intent
* no automatic upstream propagation — requirements are not marked stale when WBS changes

Rationale: requirements define scope; WBS packages implement scope. Requirement changes must flow to the plan. Plan changes do not flow back to requirements — they represent a response to requirements, not a redefinition of them.

**Traceability role**

The IMPLEMENTS edge is the primary cross-layer traceability link.

Downstream traversal from a requirement following IMPLEMENTS edges reveals the full WBS coverage of that requirement.

Coverage gaps — requirements with no IMPLEMENTS edges — are a primary target for AI-assisted traceability analysis.

---

### CONTAINS

**Semantic meaning**

A parent WBS Package hierarchically contains a child WBS Package.

This is a decomposition relationship. The parent represents a larger scope unit; the child represents a sub-scope that, together with sibling children, constitutes the parent's full scope.

**Valid endpoints**

| Source (parent) | Target (child) |
|---|---|
| WBS Package | WBS Package |

**Directionality**

The edge points **from the parent toward the child**.

Reading the edge: "this WBS package contains that sub-package."

**Directionality special note**

CONTAINS edges form a tree. A WBS Package may have at most one parent (one incoming CONTAINS edge). A WBS Package may have multiple children (multiple outgoing CONTAINS edges). Cycles are invalid.

**Propagation contract**

**Downward propagation (parent -> child):**
When a parent WBS Package changes its scope definition or is marked stale:
* all child packages are marked **stale**
* propagation boundary: **hard**

**Upward aggregation (child -> parent):**
When a child WBS Package's estimation or execution state changes:
* the parent package's aggregated estimation is flagged for recalculation
* this is an **upward aggregation** propagation, not a downward staleness cascade
* the parent is marked **stale** pending aggregation recalculation

Note: upward aggregation flows in the opposite direction to standard downstream staleness propagation. The propagation engine must support both directions for CONTAINS edges. This is the primary instance of upward-flowing propagation in the current artifact set.

**Traceability role**

Structural decomposition link. The CONTAINS hierarchy represents the work breakdown structure itself.

Upstream traversal (following CONTAINS edges upward) reveals the delivery context of a sub-package.

Downstream traversal reveals the full decomposition of a scope element.

---

### ESTIMATES

**Semantic meaning**

An Estimation artifact quantifies the effort, complexity, or cost associated with a scope artifact.

The estimation is anchored to and derived from the scope artifact it estimates.

**Valid endpoints**

| Source (estimation artifact) | Target (scope artifact) |
|---|---|
| Estimation | WBS Package |
| Estimation | Functional Requirement |

**Directionality**

The edge points **from the Estimation toward the artifact it estimates**.

Reading the edge: "this estimation quantifies that artifact."

**Propagation contract**

When the estimated artifact changes its scope, content, or decomposition:

* the Estimation is marked **stale**
* propagation boundary: **hard**

When the Estimation is revised:

* if this Estimation is anchored to a child WBS Package, the parent WBS Package is flagged for aggregation recalculation (via the CONTAINS upward aggregation model)
* no direct propagation to other artifact types

**Traceability role**

Quantification link. Downstream traversal from a requirement or WBS package following ESTIMATES edges reveals all associated estimation artifacts. Used in scope analysis to identify unestimated artifacts (scope items with no ESTIMATES edge).

---

### EXPOSES

**Semantic meaning**

An artifact creates, introduces, or surfaces a risk.

The artifact's existence or its scope implies that a risk condition exists. If the artifact did not exist, or had different scope, the risk would not be present or would present differently.

**Valid endpoints**

| Source (risk-exposing artifact) | Target (risk) |
|---|---|
| Functional Requirement | Risk Analysis |
| WBS Package | Risk Analysis |
| User Story | Risk Analysis |

**Directionality**

The edge points **from the artifact that exposes the risk toward the Risk Analysis artifact**.

Reading the edge: "this artifact exposes that risk."

**Propagation contract**

When the exposing artifact changes:

* the Risk Analysis is marked **stale** — its severity, likelihood, or relevance may have changed
* propagation boundary: **soft** — risk review is flagged but automatic cascade beyond the risk artifact does not occur

Rationale: risk relevance changes with scope, but the downstream impact of a stale risk assessment should be evaluated by a human, not cascaded automatically.

**Traceability role**

Risk provenance link. Upstream traversal from a risk following EXPOSES edges identifies the artifacts responsible for the risk's existence.

---

### IMPACTS

**Semantic meaning**

A risk may materially affect the scope, effort, or viability of a planning artifact.

If the risk materializes, the impacted artifact's assumptions or scope may need to change.

**Valid endpoints**

| Source (risk) | Target (impacted artifact) |
|---|---|
| Risk Analysis | WBS Package |
| Risk Analysis | Estimation |
| Risk Analysis | Functional Requirement |

**Directionality**

The edge points **from the Risk Analysis toward the artifact it may impact**.

Reading the edge: "this risk impacts that artifact."

**Propagation contract**

When the Risk Analysis changes severity or likelihood:

* impacted artifacts receive a **soft notification** — their assumptions may be affected
* propagation boundary: **soft**

When a risk is marked as materialized (if such a state is supported):

* a **hard propagation** marks all IMPACTS targets as stale — their content must be reviewed against the new reality

Rationale: a risk changing probability is advisory; a risk materializing is definitive.

**Traceability role**

Impact analysis link. Downstream traversal from a risk following IMPACTS edges reveals what would be affected if the risk materialized. Used in risk-weighted scope analysis and contingency planning.

---

### MITIGATES

**Semantic meaning**

A WBS Package or scope element includes work that is intended to reduce the probability or impact of a risk.

**Valid endpoints**

| Source (mitigation) | Target (mitigated risk) |
|---|---|
| WBS Package | Risk Analysis |

**Directionality**

The edge points **from the mitigating WBS Package toward the Risk Analysis it mitigates**.

Reading the edge: "this WBS package mitigates that risk."

**Propagation contract**

When the WBS Package changes or is removed:

* the Risk Analysis is marked **stale** — its mitigation status has changed
* propagation boundary: **hard**

When the Risk Analysis severity changes:

* the mitigating WBS Package receives a **soft notification** — the mitigation scope may need to be reassessed
* propagation boundary: **soft**

**Traceability role**

Mitigation accountability link. Downstream traversal from a risk following MITIGATES edges in reverse (i.e., traversing incoming MITIGATES edges) identifies what mitigation scope exists for a given risk. Used in risk coverage analysis: risks with no incoming MITIGATES edges are unmitigated.

---

### TRACES_TO

**Semantic meaning**

A general-purpose traceability link between any two artifacts when no more specific edge type correctly describes the relationship.

TRACES_TO asserts that one artifact is relevant to or connected with another in a traceability sense, without specifying the precise nature of that relationship.

**Valid endpoints**

| Source | Target |
|---|---|
| Any artifact | Any artifact |

**Directionality**

The edge points **from the artifact asserting relevance toward the artifact it traces to**.

**Propagation contract**

When the target artifact changes:

* a **soft notification** is raised on the source artifact
* propagation boundary: **soft**

TRACES_TO carries no strong propagation guarantee because its semantic meaning is intentionally vague.

**Traceability role**

General traceability link. Present for completeness and flexibility.

**Usage discipline**

TRACES_TO should be used sparingly.

If a relationship between two artifacts can be described by a more specific edge type, that edge type must be used instead.

An overabundance of TRACES_TO edges in a graph indicates either that the edge taxonomy is missing types it needs, or that users are creating low-quality relationships. Both are signals worth investigating.

---

## Propagation Boundary Summary

Quick reference for the propagation behavior of each edge type:

| Edge Type | Propagation Direction | Boundary Type |
|---|---|---|
| DERIVED_FROM | Downstream (source change -> derived artifact stale) | Hard |
| REFINES | Downstream (base change -> refining artifact stale) | Soft |
| CONTEXTUALIZES | Downstream (context change -> notification) | Soft |
| INFLUENCES | Downstream (stakeholder change -> notification) | Soft |
| IMPLEMENTS | Downstream (requirement change -> WBS stale) | Hard |
| CONTAINS (downward) | Downstream (parent scope change -> children stale) | Hard |
| CONTAINS (upward) | Upward aggregation (child estimate change -> parent recalculation) | Hard |
| ESTIMATES | Downstream (scope change -> estimation stale) | Hard |
| EXPOSES | Downstream (artifact change -> risk stale) | Soft |
| IMPACTS | Downstream (risk severity change -> notification) | Soft |
| IMPACTS | Downstream (risk materializes -> impacted artifacts stale) | Hard |
| MITIGATES | Downstream (WBS change -> risk stale) | Hard |
| MITIGATES | Upstream notification (risk severity change -> WBS notification) | Soft |
| TRACES_TO | Downstream (target change -> notification) | Soft |

Hard boundary: staleness cascades automatically through the edge without pause.

Soft boundary: notification is raised; cascade does not continue until user or system confirms.

---

## Edge Validation Rules

The following rules are enforced by the graph module at edge creation time:

1. **Type validity** — the source and target artifact types must be among the valid endpoints for the declared edge type. Invalid type combinations must be rejected.

2. **Directionality** — the source and target roles must match the declared directionality of the edge type. An edge created in the wrong direction must be rejected.

3. **CONTAINS tree constraint** — a WBS Package may have at most one incoming CONTAINS edge. A second CONTAINS edge pointing to an existing child is rejected.

4. **Cycle prevention** — a proposed edge that would create a cycle in the dependency graph must be rejected. Cycle detection is performed before write.

5. **Self-reference** — an edge may not have the same artifact as both source and target.

6. **Duplicate edge constraint** — two edges of the same type between the same source and target artifacts are not permitted. Each typed relationship between two artifacts should be expressed once.

---

## Graph Integrity Constraints

Beyond per-edge validation, the following graph-level integrity rules apply:

**Coverage expectations (advisory, not enforced)**

These are not hard constraints but are surfaced as AI-assisted analysis findings:

* Every Functional Requirement should have at least one incoming IMPLEMENTS edge — unimplemented requirements are a coverage gap
* Every WBS Package at the leaf level should have at least one ESTIMATES edge — unestimated scope is a planning gap
* Every Risk Analysis with severity above a threshold should have at least one incoming MITIGATES edge from a WBS Package — unmitigated high-severity risks are a risk gap

**Orphan detection**

An artifact with no edges of any type is an orphan. Orphans are valid (an artifact may be newly created) but should be surfaced in graph analysis as potentially disconnected from the semantic graph.

---

## Future Edge Types

The following edge types are anticipated as the artifact taxonomy expands into architectural, specification, and verification categories:

| Anticipated Edge Type | Anticipated Relationship |
|---|---|
| `SPECIFIES` | Specification Artifact -> Functional Requirement |
| `VALIDATES` | Verification Artifact -> Functional Requirement or User Story |
| `SUPERSEDES` | Requirement -> Requirement (replacing a prior version) |
| `CONFLICTS_WITH` | Requirement -> Requirement (detected contradiction) |
| `DEPENDS_ON` | Architectural Artifact -> Architectural Artifact |
| `REALIZES` | Architectural Component -> Functional Requirement |

These edge types must not be implemented until their corresponding artifact categories are formally introduced through the taxonomy evolution process.

When introduced, each must be defined across all five edge dimensions before implementation begins.
