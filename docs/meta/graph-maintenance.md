# Graph Maintenance Guide

## Related Documents

* [Repository Governance](repository-governance.md) — defines the governance layer that graph tooling supports
* [Orchestration Model](orchestration-model.md) — defines how the orchestrator uses graph analysis for governance assessment
* [Repository Cognition](repository-cognition.md) — defines the cognitive architecture that the knowledge graph operationalizes

---

## Purpose

This document explains how to use and maintain graphify in the EngraphSW repository.

It defines:

* what graphify analyzes and what it produces
* what counts as a graph node in this repository
* how to interpret EXTRACTED vs INFERRED edges
* how to run graphify after structural changes
* how to avoid common false-positive patterns

---

## What Graphify Analyzes

Graphify performs semantic graph analysis over the repository's document corpus.

It produces:

* a node graph of concepts, documents, and relationships
* community detection across related nodes
* identification of god nodes (high-connectivity concepts)
* inferred and extracted relationship edges
* coverage gaps and isolated node warnings

In EngraphSW, the intended analysis corpus is the `docs/` and `.claude/` directories — the repository's cognitive and governance layer.

---

## Scope Boundaries

### What Is Included

Graphify should analyze:

```
docs/
.claude/CLAUDE.md
.claude/agents/
.claude/instructions/
```

### What Is Excluded

Graphify respects `.graphifyignore` — a file at the repository root using the same syntax as `.gitignore`. The repository `.graphifyignore` excludes:

* `.obsidian/` — Obsidian editor configuration files. These are UI state, not repository artifacts. Graphify will extract UI component names (file-explorer, switcher, canvas, graph) and JSON structure keys (right, collapsed, direction) from these files and produce hundreds of phantom nodes.
* `graphify-out/` — graphify's own output directory. Analyzing its output would produce circular self-references.
* `.claude/settings.json` — Claude Code hook configuration. Graphify extracts hook names and tool references as phantom nodes.
* `node_modules/`, `.next/`, `dist/` — build and dependency artifacts.

**Do not use `.gitignore` for graphify exclusions.** `.gitignore` controls git tracking. `.graphifyignore` controls graphify's scan scope. They are independent files.

The repository `.gitignore` should follow the graphify-recommended pattern: exclude `graphify-out/manifest.json` and `graphify-out/cost.json` (local-only files), while committing `graphify-out/graph.json` and `graphify-out/GRAPH_REPORT.md` so all team members start with a current map.

**Do not remove `.obsidian/` from `.graphifyignore`.** It is the primary source of phantom node noise in this repository.

---

## What Counts as a Graph Node

A valid graph node in the EngraphSW repository is a **document or concept that currently exists as a file or explicitly defined artifact**.

Valid nodes:
* a file in `docs/` (an ADR, architecture doc, domain doc, meta doc)
* a concept explicitly defined within one of those files
* a `.claude/` agent or instruction document

Not valid nodes (at Phase 1A):
* UI components referenced in architecture documents but not yet implemented
* Module names referenced in planning documents but not yet existing as code
* Technology names mentioned in the tech stack (Next.js, Prisma, etc.)

Graphify may extract forward references from documentation as phantom nodes. This is expected behavior for a pre-implementation repository. Do not create documentation to satisfy phantom nodes — understand what file they originate from and verify the `.graphifyignore` is correctly scoped.

---

## EXTRACTED vs INFERRED Edges

Graphify produces two types of edges:

**EXTRACTED** — the relationship is stated or strongly implied by an explicit reference within the document content. Markdown links (`[text](path)`) are the strongest signal for extraction.

**INFERRED** — the relationship is model-reasoned from semantic proximity. The documents discuss related topics but do not explicitly link to each other.

### Why Explicit Links Matter

Inferred edges require verification. Extracted edges do not.

If two documents are genuinely related, they should carry explicit Markdown links to each other. This:

* converts INFERRED edges to EXTRACTED edges in the graph
* removes the verification burden from graph analysis
* improves community cohesion scores
* makes relationships auditable by humans, not just by the model

### When to Add Cross-References

Add an explicit link when:

* a document delegates a concern to another document ("see X for details")
* a document depends on a decision made in another document
* a document is the direct implementation of a concept defined elsewhere
* two documents together form a complete picture of a system

Do not add links for superficial proximity (both documents mention nodes, both mention propagation). Links should carry architectural meaning.

---

## Running Graphify

Run graphify from the repository root after any structural change to the document corpus:

```
graphify update .
```

Run after:
* adding a new document to `docs/`
* adding a new ADR
* adding a new agent or instruction file
* making significant content changes to existing governance documents

Do not run after:
* implementation code changes only (no doc changes)
* changes only to `.obsidian/` or `graphify-out/`

After running, check the new `GRAPH_REPORT.md` for:

1. Changes in the INFERRED edge count — a decrease means cross-references are being picked up correctly
2. Changes in the isolated node count — an increase may indicate new phantom nodes or genuinely missing cross-references
3. Community cohesion changes — improvements indicate the ADR metadata and cross-references are taking effect
4. New god nodes — a new high-connectivity concept may indicate a missing architecture document or an over-referenced concept

---

## Interpreting the Report

### God Nodes

God nodes are the most connected concepts in the repository.

In a well-structured repository, god nodes should correspond to genuine architectural anchors:
* foundational architecture documents (Modular Boundaries, Typed Edge Semantics)
* governance documents (Repository Governance, AI Role Separation)
* core domain concepts (Dependency Propagation, Graph-Native Architecture)

If a god node is an unexpected concept (a JSON key, an Obsidian command, a technology name), it indicates a scope calibration problem.

### Community Cohesion

Cohesion scores reflect how tightly connected nodes within a community are.

In a documentation repository with intentionally diverse ADRs, low cohesion in an ADR community is expected — each ADR is a distinct decision. Low cohesion is not inherently a problem.

Low cohesion becomes a problem when:
* documents that should be closely related are ending up in different communities
* a community contains both repository artifacts and phantom nodes (scope contamination)

### Isolated Nodes

Isolated nodes (<=1 connection) in the repository corpus fall into two categories:

**Legitimate isolation** — a new document that has not yet been cross-referenced. Add explicit links to resolve.

**Phantom nodes** — a concept extracted from documentation text that does not correspond to an existing file. Do not create documentation to resolve. Instead, check whether the `.graphifyignore` needs updating or whether the extracting document uses overly specific implementation terminology.

---

## ADR Graph Discipline

Each ADR includes a Metadata table with:

* **Domain** — the architectural concern category (e.g., Graph Architecture, Node System, AI Infrastructure)
* **Phase Introduced** — the phase in which this decision was made
* **Related ADRs** — explicit links to other ADRs this decision depends on or supersedes
* **Related Architecture** — links to architecture documents that implement this decision
* **Related Domain** — links to domain documents governed by this decision

Keep the Metadata table current when a new ADR is added or when an existing ADR's related documents change.

New ADRs should be linked from at least one existing ADR or architecture document before the graph is updated — an ADR with no incoming references is an isolated node.
