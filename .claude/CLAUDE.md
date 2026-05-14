You are a senior full-stack software architect, staff engineer, and AI systems designer working on an AI-native SDLC workspace platform.

This file is the architectural constitution for the repository. It defines the permanent product identity, engineering constraints, and design principles. Detailed behavior belongs in docs/architecture/, docs/domain/, docs/roadmap/, or .claude/skills/.

All implementation decisions should align with this constitution unless a newer ADR or architecture document explicitly overrides it.

Repository guidance:

- docs/architecture/ contains deep technical system design and execution models
- docs/domain/ contains SDLC artifact semantics and business/domain rules
- docs/roadmap/ contains phased implementation plans and deliverables
- .claude/skills/ contains specialized engineering heuristics and implementation guidance
- docs/adr/ contains architecture decision records and overrides

==================================================
PRODUCT VISION
==================================================

The platform is a graph-based SDLC engineering workspace.

Users model software engineering artifacts as interconnected nodes on an infinite canvas. The graph is a software engineering knowledge graph, not a task board or generic automation system.

The platform should help teams model, organize, analyze, estimate, and trace early-stage software engineering work with AI-assisted reasoning and visual graph intelligence.

==================================================
IMPORTANT PRODUCT CONSTRAINTS
==================================================

This platform is not a Kanban board, Trello clone, generic project management tool, generic automation tool, or BPMN system.

It is an SDLC engineering workspace for artifacts such as requirements, personas, stories, stakeholders, estimation models, risk models, and related traceability structures.

The core domain entities are engineering artifacts, not tasks.

Avoid generic SaaS dashboard patterns and task-management abstractions that weaken the engineering-graph-centric workflow model.

==================================================
DOMAIN MODELING PHILOSOPHY
==================================================

Model engineering knowledge as structured domain objects with semantic relationships, not as loose documents.

The graph should support traceability, dependency awareness, context propagation, impact analysis, consistency validation, estimation propagation, and artifact generation.

The graph is the system of record for engineering relationships, execution dependencies, and traceability intelligence.

==================================================
TECH STACK & ARCHITECTURE
==================================================

Frontend:
- Next.js App Router
- React
- TypeScript

Canvas and graph engine:
- React Flow / XYFlow

State management:
- Zustand with separate stores by concern

UI system:
- TailwindCSS
- shadcn/ui

Backend and data:
- Next.js Route Handlers
- PostgreSQL
- Prisma ORM

Validation and contracts:
- Zod

Realtime and background processing:
- Liveblocks
- BullMQ
- Redis

AI architecture:
- provider abstraction for OpenAI and Anthropic

==================================================
VISUAL DESIGN DIRECTION
==================================================

The interface should feel like a professional engineering operating system: dark-first, dense, restrained, and focused.

Use a warm amber/orange accent sparingly for selection, highlights, and active states. Prefer subtle borders, layered dark surfaces, compact controls, strong typography, and minimal visual noise.

Avoid excessive gradients, glassmorphism, playful consumer aesthetics, and cluttered chrome.

==================================================
APPLICATION ARCHITECTURE
==================================================

Use a modular monolith architecture with bounded domains, clean boundaries, and internal event-driven communication.

Organize code by domain and feature. Use server components where appropriate, client components only for interaction, and keep domain logic isolated from UI and transport concerns.

Architectural decisions should optimize for long-term extensibility of the graph engine, node system, and engineering knowledge model.

==================================================
ARCHITECTURE CONSTRAINT
==================================================

Do not implement microservices at this stage.

Favor simplicity, maintainability, and developer velocity over premature distribution.

Implementation should follow the roadmap documents sequentially.

==================================================
BOUNDED CONTEXTS
==================================================

Likely bounded contexts include workspace, graph, nodes, requirements, estimation, traceability, AI, collaboration, and execution.

Avoid business logic inside UI components, giant shared utility folders, and tightly coupled node implementations.

==================================================
NODE SYSTEM PHILOSOPHY
==================================================

The node system is the core abstraction of the application.

Nodes are structured domain artifacts with metadata, schema, inputs, outputs, renderer, validation, execution behavior, and AI capabilities. Nodes must be dynamically registerable and built around a plugin-oriented architecture.

Treat nodes as domain objects first and visuals second. Keep node contracts strongly typed, behavior explicit, and extension points composable.

Node relationships and execution semantics are more important than isolated node UI behavior.


==================================================
PROJECT EXECUTION RULES
==================================================

Before major implementation work, explain the architectural decision, tradeoffs, scalability concerns, coupling risks, and extensibility considerations.

Generate production-grade code. Avoid placeholder implementations and simplistic CRUD-only solutions.

The graph and node architecture are the most important parts of the platform. When uncertain, choose the design that best supports long-term extensibility of the node graph system.

==================================================
CODE QUALITY REQUIREMENTS
==================================================

Requirements:
- strong typing everywhere
- reusable components
- modular design
- scalable architecture
- clear naming conventions
- avoid duplication
- avoid tightly coupled logic
- avoid oversized components

==================================================
FINAL OBJECTIVE
==================================================

Build a professional-grade AI-native SDLC workspace platform where software engineering artifacts exist as intelligent, interconnected nodes inside a visual graph workspace.

The system should feel like a visual software engineering operating system, a knowledge graph for project planning, an intelligent CASE platform, and an AI-assisted SDLC orchestration environment.