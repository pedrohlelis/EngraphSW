# SDLC Artifact Domain Model

**Community 4** · 18 nodes · cohesion=0.14

## Nodes

- **Engineering Artifact Taxonomy** [`document`] — `docs/domain/engineering-artifact-taxonomy.md`
- **Platform Capability Domains (5 Domains)** [`rationale`] — `docs/domain/engineering-artifact-taxonomy.md`
- **Context Artifacts Category (Persona, Stakeholder)** [`rationale`] — `docs/domain/engineering-artifact-taxonomy.md`
- **Graph Semantic Principles (Artifacts as Nodes)** [`rationale`] — `docs/domain/engineering-artifact-taxonomy.md`
- **Planning Artifacts Category (WBS, Estimation)** [`rationale`] — `docs/domain/engineering-artifact-taxonomy.md`
- **Requirements Artifacts Category (FR, User Story)** [`rationale`] — `docs/domain/engineering-artifact-taxonomy.md`
- **Risk Artifacts Category (Risk Analysis)** [`rationale`] — `docs/domain/engineering-artifact-taxonomy.md`
- **Stream 5 — Domain Specification Hardening** [`rationale`] — `docs/roadmap/PHASE_1B_SPECIFICATION.md`
- **Functional Requirements Node** [`rationale`] — `docs/roadmap/PHASE_5_SDLC_NODES.md`
- **Personas Node** [`rationale`] — `docs/roadmap/PHASE_5_SDLC_NODES.md`
- **Phase 5 — Initial SDLC Nodes** [`document`] — `docs/roadmap/PHASE_5_SDLC_NODES.md`
- **Stakeholder Node** [`rationale`] — `docs/roadmap/PHASE_5_SDLC_NODES.md`
- **User Stories Node** [`rationale`] — `docs/roadmap/PHASE_5_SDLC_NODES.md`
- **Phase 6 — Function Point Analysis** [`document`] — `docs/roadmap/PHASE_6_FUNCTION_POINT_ANALYSIS.md`
- **Function Point Node** [`rationale`] — `docs/roadmap/PHASE_6_FUNCTION_POINT_ANALYSIS.md`
- **Phase 7 — AI Features** [`document`] — `docs/roadmap/PHASE_7_AI_FEATURES.md`
- **AI Generation Workflows** [`rationale`] — `docs/roadmap/PHASE_7_AI_FEATURES.md`
- **AI Provider Abstraction (OpenAI + Anthropic)** [`rationale`] — `docs/roadmap/PHASE_7_AI_FEATURES.md`

## Internal Edges

  - Engineering Artifact Taxonomy --conceptually_related_to--> Platform Capability Domains (5 Domains) [EXTRACTED 1.0]
  - Engineering Artifact Taxonomy --conceptually_related_to--> Context Artifacts Category (Persona, Stakeholder) [EXTRACTED 1.0]
  - Engineering Artifact Taxonomy --conceptually_related_to--> Requirements Artifacts Category (FR, User Story) [EXTRACTED 1.0]
  - Engineering Artifact Taxonomy --conceptually_related_to--> Planning Artifacts Category (WBS, Estimation) [EXTRACTED 1.0]
  - Engineering Artifact Taxonomy --conceptually_related_to--> Risk Artifacts Category (Risk Analysis) [EXTRACTED 1.0]
  - Engineering Artifact Taxonomy --conceptually_related_to--> Graph Semantic Principles (Artifacts as Nodes) [EXTRACTED 1.0]
  - Stream 5 — Domain Specification Hardening --references--> Engineering Artifact Taxonomy [EXTRACTED 1.0]
  - Functional Requirements Node --semantically_similar_to--> Requirements Artifacts Category (FR, User Story) [INFERRED 0.95]
  - Personas Node --semantically_similar_to--> Context Artifacts Category (Persona, Stakeholder) [INFERRED 0.95]
  - Phase 5 — Initial SDLC Nodes --conceptually_related_to--> Functional Requirements Node [EXTRACTED 1.0]
  - Phase 5 — Initial SDLC Nodes --conceptually_related_to--> Personas Node [EXTRACTED 1.0]
  - Phase 5 — Initial SDLC Nodes --conceptually_related_to--> User Stories Node [EXTRACTED 1.0]
  - Phase 5 — Initial SDLC Nodes --conceptually_related_to--> Stakeholder Node [EXTRACTED 1.0]
  - Phase 5 — Initial SDLC Nodes --references--> Phase 6 — Function Point Analysis [EXTRACTED 1.0]
  - Stakeholder Node --semantically_similar_to--> Context Artifacts Category (Persona, Stakeholder) [INFERRED 0.95]
  - User Stories Node --semantically_similar_to--> Requirements Artifacts Category (FR, User Story) [INFERRED 0.95]
  - Phase 6 — Function Point Analysis --conceptually_related_to--> Function Point Node [EXTRACTED 1.0]
  - Phase 6 — Function Point Analysis --references--> Phase 7 — AI Features [EXTRACTED 1.0]
  - Function Point Node --semantically_similar_to--> Planning Artifacts Category (WBS, Estimation) [INFERRED 0.85]
  - Phase 7 — AI Features --conceptually_related_to--> AI Provider Abstraction (OpenAI + Anthropic) [EXTRACTED 1.0]
  - Phase 7 — AI Features --conceptually_related_to--> AI Generation Workflows [EXTRACTED 1.0]
  - AI Generation Workflows --semantically_similar_to--> Platform Capability Domains (5 Domains) [INFERRED 0.75]

## Cross-Community Edges

  - Engineering Artifact Taxonomy --references--> Typed Edge Semantics Document [EXTRACTED] (`docs/domain/typed-edge-semantics.md`)
  - Engineering Artifact Taxonomy --conceptually_related_to--> Artifact vs Task Boundary [EXTRACTED] (`docs/domain/engineering-artifact-taxonomy.md`)
  - Engineering Artifact Taxonomy --conceptually_related_to--> Artifact Lifecycle (Created, Active, Stale, Retired) [EXTRACTED] (`docs/domain/engineering-artifact-taxonomy.md`)
  - Stream 5 — Domain Specification Hardening --references--> Typed Edge Semantics Document [EXTRACTED] (`docs/domain/typed-edge-semantics.md`)
  - Phase 6 — Function Point Analysis --references--> Node Staleness Mechanism [EXTRACTED] (`docs/architecture/dependency-propagation.md`)
  - Phase 7 — AI Features --references--> Phase 8 — Collaboration [EXTRACTED] (`docs/roadmap/PHASE_8_COLLABORATION.md`)

[Back to index](index.md)
