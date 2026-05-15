# EngraphSW — Naming Conventions

## Purpose

This document defines official terminology, naming standards, and semantic conventions across the EngraphSW platform.

Consistency is critical for:
- architecture clarity
- graph semantics
- AI consistency
- documentation quality
- UX coherence

---

# Core Vocabulary

| Concept | Official Term |
|---|---|
| Graph item | Node |
| Relationship | Edge |
| Engineering object | Artifact |
| Graph container | Workspace |
| Dependency flow | Propagation |
| Recalculation | Execution |
| Node connection point | Handle |
| Structured relationship | Traceability Link |
| AI interaction | AI Action |
| Graph update | Graph Mutation |

---

# Forbidden Terminology

Avoid generic project-management terminology such as:
- task
- ticket
- card
- board
- pipeline
- checklist
- automation recipe

Avoid no-code terminology such as:
- block
- magic flow
- zap
- automation chain

The platform models engineering artifacts and graph relationships, not generic productivity objects.

---

# Node Naming Rules

Node types should:
- be explicit
- use engineering terminology
- clearly communicate purpose

Preferred:
- Functional Requirements Node
- User Stories Node
- Function Point Analysis Node
- Risk Analysis Node

Avoid:
- Smart Node
- Dynamic Block
- AI Card
- Workflow Box

---

# File Naming Standards

Use:
- kebab-case for files and folders
- PascalCase for React components
- camelCase for variables/functions
- SCREAMING_SNAKE_CASE for constants

Examples:
- function-point-analysis-node.ts
- GraphCanvas.tsx
- propagateDependencies()
- DEFAULT_NODE_WIDTH

---

# ADR Naming

Architecture Decision Records should use:
- sequential numbering
- kebab-case naming

Example:
- 001-modular-monolith.md
- 002-plugin-node-architecture.md

---

# Event Naming Conventions

Events should:
- use past tense
- describe completed domain actions
- remain domain-oriented

Preferred:
- RequirementCreatedEvent
- NodeExecutionCompletedEvent
- ArtifactVersionCreatedEvent

Avoid:
- DoNodeStuffEvent
- GraphActionEvent
- GenericUpdateEvent

---

# Module Naming

Modules should represent bounded domains.

Preferred:
- graph
- nodes
- requirements
- estimation
- traceability
- execution

Avoid:
- shared-everything
- common-utils
- misc

---

# Database Naming

Prefer:
- singular model names
- explicit relationships
- semantic naming

Preferred:
- Node
- Edge
- Workspace
- ArtifactVersion

Avoid:
- TblNode
- DataBlob
- GenericEntity

---

# AI Naming Conventions

AI-generated operations should be described as:
- AI-assisted
- AI-generated
- AI-suggested
- AI-analyzed

Avoid language implying autonomous authority:
- self-thinking
- autonomous engineering
- self-building system

---

# Graph Terminology

The graph is:
- an engineering knowledge graph
- a traceability graph
- a dependency graph

The graph is NOT:
- a workflow board
- a task pipeline
- a generic flowchart

---

# UX Terminology

Prefer:
- Analyze Dependencies
- Generate Requirements
- Propagate Changes
- Trace Relationships

Avoid:
- Boost Productivity
- Magic Generate
- Smart Workflow
- Supercharge Planning