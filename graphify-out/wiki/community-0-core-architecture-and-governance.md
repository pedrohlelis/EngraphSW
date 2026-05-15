# Core Architecture & Governance

**Community 0** · 25 nodes · cohesion=0.15

## Nodes

- **ADR-003 Graph as System of Record** [`document`] — `docs/adr/003-graph-system-of-record.md`
- **EngraphSW Implementer Agent** [`document`] — `.claude/agents/implementer.md`
- **EngraphSW Repository Constitution** [`document`] — `.claude/CLAUDE.md`
- **AI-Native Engineering Philosophy** [`rationale`] — `.claude/CLAUDE.md`
- **Bounded Contexts** [`rationale`] — `.claude/skills/domain-driven-design/SKILL.md`
- **Dependency Propagation** [`rationale`] — `.claude/skills/graph-engineering/SKILL.md`
- **Engineering Artifacts as First-Class Entities** [`rationale`] — `.claude/CLAUDE.md`
- **Event-Driven Internal Communication** [`rationale`] — `.claude/skills/event-driven-patterns/SKILL.md`
- **Graph as System of Record** [`rationale`] — `docs/adr/003-graph-system-of-record.md`
- **Graph-Native SDLC Engineering Platform** [`rationale`] — `.claude/CLAUDE.md`
- **Layered Repository Cognition** [`rationale`] — `.claude/CLAUDE.md`
- **Node System Core Abstraction** [`rationale`] — `.claude/skills/node-design/SKILL.md`
- **Phase-Gated Execution Control** [`rationale`] — `.claude/agents/implementer.md`
- **Traceability Intelligence** [`rationale`] — `docs/adr/003-graph-system-of-record.md`
- **Visual Engineering Operating System** [`rationale`] — `.claude/CLAUDE.md`
- **Information Boundaries Instruction** [`document`] — `.claude/instructions/information-boundaries.md`
- **AI Workflows Skill** [`document`] — `.claude/skills/ai-workflows/SKILL.md`
- **Architecture Review Skill** [`document`] — `.claude/skills/architecture-review/SKILL.md`
- **Domain Driven Design Skill** [`document`] — `.claude/skills/domain-driven-design/SKILL.md`
- **Event Driven Patterns Skill** [`document`] — `.claude/skills/event-driven-patterns/SKILL.md`
- **Graph Engineering Skill** [`document`] — `.claude/skills/graph-engineering/SKILL.md`
- **Node Design Skill** [`document`] — `.claude/skills/node-design/SKILL.md`
- **React Flow Patterns Skill** [`document`] — `.claude/skills/react-flow-patterns/SKILL.md`
- **Technical Debt Review Skill** [`document`] — `.claude/skills/technical-debt-review/SKILL.md`
- **UI System Skill** [`document`] — `.claude/skills/ui-system/SKILL.md`

## Internal Edges

  - ADR-003 Graph as System of Record --rationale_for--> Graph as System of Record [EXTRACTED 1.0]
  - ADR-003 Graph as System of Record --references--> Traceability Intelligence [EXTRACTED 1.0]
  - ADR-003 Graph as System of Record --references--> Dependency Propagation [EXTRACTED 1.0]
  - EngraphSW Implementer Agent --references--> EngraphSW Repository Constitution [EXTRACTED 1.0]
  - EngraphSW Implementer Agent --conceptually_related_to--> Phase-Gated Execution Control [EXTRACTED 1.0]
  - EngraphSW Implementer Agent --conceptually_related_to--> Graph-Native SDLC Engineering Platform [EXTRACTED 1.0]
  - EngraphSW Implementer Agent --references--> Node System Core Abstraction [EXTRACTED 1.0]
  - EngraphSW Implementer Agent --references--> Information Boundaries Instruction [EXTRACTED 1.0]
  - EngraphSW Repository Constitution --conceptually_related_to--> Graph-Native SDLC Engineering Platform [EXTRACTED 1.0]
  - EngraphSW Repository Constitution --conceptually_related_to--> AI-Native Engineering Philosophy [EXTRACTED 1.0]
  - EngraphSW Repository Constitution --conceptually_related_to--> Layered Repository Cognition [EXTRACTED 1.0]
  - EngraphSW Repository Constitution --references--> Node System Core Abstraction [EXTRACTED 1.0]
  - EngraphSW Repository Constitution --references--> Bounded Contexts [EXTRACTED 1.0]
  - EngraphSW Repository Constitution --references--> Engineering Artifacts as First-Class Entities [EXTRACTED 1.0]
  - EngraphSW Repository Constitution --conceptually_related_to--> Visual Engineering Operating System [EXTRACTED 1.0]
  - AI-Native Engineering Philosophy --conceptually_related_to--> AI Workflows Skill [INFERRED 0.85]
  - Event-Driven Internal Communication --conceptually_related_to--> Dependency Propagation [INFERRED 0.85]
  - Graph as System of Record --conceptually_related_to--> Graph-Native SDLC Engineering Platform [INFERRED 0.95]
  - Layered Repository Cognition --conceptually_related_to--> Information Boundaries Instruction [INFERRED 0.95]
  - Traceability Intelligence --conceptually_related_to--> Engineering Artifacts as First-Class Entities [INFERRED 0.85]
  - Information Boundaries Instruction --references--> EngraphSW Implementer Agent [EXTRACTED 1.0]
  - AI Workflows Skill --conceptually_related_to--> AI-Native Engineering Philosophy [EXTRACTED 1.0]
  - AI Workflows Skill --conceptually_related_to--> Graph-Native SDLC Engineering Platform [INFERRED 0.85]
  - Architecture Review Skill --references--> Bounded Contexts [EXTRACTED 1.0]
  - Architecture Review Skill --conceptually_related_to--> Graph-Native SDLC Engineering Platform [EXTRACTED 1.0]
  - Architecture Review Skill --references--> Event-Driven Internal Communication [EXTRACTED 1.0]
  - Architecture Review Skill --semantically_similar_to--> Domain Driven Design Skill [INFERRED 0.75]
  - Domain Driven Design Skill --conceptually_related_to--> Bounded Contexts [EXTRACTED 1.0]
  - Domain Driven Design Skill --references--> Event-Driven Internal Communication [EXTRACTED 1.0]
  - Domain Driven Design Skill --conceptually_related_to--> Engineering Artifacts as First-Class Entities [INFERRED 0.85]

## Cross-Community Edges

  - ADR-003 Graph as System of Record --references--> adr_002_plugin_node [EXTRACTED] (``)
  - ADR-003 Graph as System of Record --references--> adr_004_event_driven [EXTRACTED] (``)
  - ADR-003 Graph as System of Record --references--> arch_dependency_propagation [EXTRACTED] (``)
  - ADR-003 Graph as System of Record --references--> arch_persistence_strategy [EXTRACTED] (``)
  - ADR-003 Graph as System of Record --references--> domain_typed_edge_semantics [EXTRACTED] (``)
  - EngraphSW Implementer Agent --references--> roadmap_current_phase [EXTRACTED] (``)
  - EngraphSW Repository Constitution --references--> concept_modular_monolith [EXTRACTED] (``)
  - Phase-Gated Execution Control --references--> roadmap_current_phase [EXTRACTED] (``)
  - Information Boundaries Instruction --references--> agent_orchestrator [INFERRED] (``)
  - Information Boundaries Instruction --references--> roadmap_current_phase [EXTRACTED] (``)
  - Architecture Review Skill --references--> concept_modular_monolith [EXTRACTED] (``)
  - Node Design Skill --conceptually_related_to--> concept_plugin_node_architecture [EXTRACTED] (``)

[Back to index](index.md)
