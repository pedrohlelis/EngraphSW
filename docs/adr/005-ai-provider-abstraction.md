# ADR-005 — AI Provider Abstraction

## Status

Accepted

## Metadata

| Field | Value |
|---|---|
| Domain | AI Infrastructure |
| Phase Introduced | Phase 1A |
| Related ADRs | [ADR-001 Modular Monolith](001-modular-monolith.md), [ADR-004 Event-Driven Communication](004-event-driven-communication.md) |
| Related Architecture | [AI Architecture](../architecture/ai-architecture.md), [Modular Boundaries](../architecture/modular-boundaries.md) |

---

# Context

EngraphSW is an AI-native SDLC engineering platform.

AI capabilities are not peripheral features. They are core to the platform's value proposition:

- AI-assisted requirement generation
- AI-assisted traceability analysis
- AI-assisted estimation
- AI-assisted risk analysis
- AI-assisted dependency reasoning
- AI-assisted graph intelligence

The platform currently targets two AI providers:

- Anthropic (Claude)
- OpenAI (GPT)

Both providers expose fundamentally similar interfaces but differ in:

- API shape and authentication
- Model capabilities and context windows
- Token pricing and rate limits
- Streaming behavior
- Tool use and function calling conventions
- Reliability and latency characteristics

Directly coupling AI operations to a specific provider at the call site would create:

- vendor lock-in at the implementation level
- inability to switch or fallback providers without broad code changes
- inability to test AI logic independently of provider behavior
- inconsistent prompt execution semantics across the system
- no clear ownership boundary for AI operational concerns

The system requires AI operations to be:

- provider-agnostic at the call site
- auditable and traceable
- composable across the graph execution model
- resilient to provider-level failures
- observable for token usage and cost

---

# Decision

The platform will implement an AI provider abstraction layer.

All AI operations within the system must route through this abstraction. No module may call an AI provider SDK directly at the implementation level.

## Abstraction Contract

The provider abstraction must define:

- a unified interface for completion requests
- a unified interface for streaming completions
- a unified interface for structured output (tool use / function calling)
- a standard request context model carrying: prompt, model hint, temperature, token limits
- a standard response model carrying: content, usage metrics, provider identity, latency

## Provider Registration

Providers must be registered rather than hardcoded.

The system must support:

- multiple concurrently registered providers
- per-request provider selection via model hint or explicit provider key
- fallback provider configuration

## Prompt Modularity

Prompts must not be embedded inline at call sites.

Prompts should be:

- defined as composable named prompt templates
- separated from execution logic
- versioned with the system

## Operational Requirements

The abstraction layer must support:

- token usage tracking per request
- latency tracking per request
- provider identity logging per request
- request and response audit logging
- rate limit handling with retry semantics
- error normalization across providers

## Scope Boundary

The AI abstraction layer is a bounded context within the modular monolith.

It owns:

- provider registration and configuration
- request routing
- response normalization
- prompt template management
- usage and audit logging

It does not own:

- domain-specific AI logic (belongs to the node or domain module that invokes it)
- prompt content for specific engineering operations (belongs to the relevant domain module)
- graph execution orchestration (belongs to the execution context)

---

# Consequences

## Positive

- Provider independence at all call sites
- Clean swap or addition of providers without implementation changes
- Consistent operational behavior across all AI operations
- Observable token usage and cost
- Testable AI logic independent of provider
- Resilience through fallback configuration
- Prompt logic owned by domain modules rather than entangled with infrastructure

## Negative

- Abstraction layer requires design and maintenance investment
- Lowest-common-denominator risk: abstraction may constrain provider-specific capabilities
- Provider-specific advanced features (e.g., extended thinking, specialized modalities) require explicit abstraction extension
- Adds an indirection layer to every AI call

---

# Alternatives Considered

## Direct Provider SDK Usage at Call Sites

Rejected because:

- creates vendor lock-in throughout the codebase
- makes provider migration a cross-cutting change
- prevents consistent operational observability
- couples domain logic to infrastructure

## Single Provider Only (Anthropic or OpenAI)

Rejected because:

- eliminates provider resilience
- creates business risk from provider pricing or availability changes
- constrains future capability selection
- reduces architectural flexibility

## External AI Gateway Service

Rejected because:

- premature operational complexity at current scale
- introduces distributed systems overhead before justified
- contradicts the modular monolith strategy (ADR-001)
- can be revisited if operational scale later justifies extraction

---

# Long-Term Implications

The AI abstraction layer is expected to evolve into a more sophisticated AI orchestration context as the platform matures.

Future capabilities may include:

- multi-step AI workflow orchestration
- AI reasoning chains over graph context
- graph-aware prompt construction using traceability and dependency data
- semantic caching of AI responses where appropriate
- provider capability negotiation based on request requirements

The abstraction boundary established by this decision must be maintained as the platform evolves.

Provider-specific capabilities may be exposed through the abstraction as explicit extensions, not as bypasses.

This ADR should be reviewed if the platform introduces model-specific features (such as extended reasoning or multimodal inputs) that cannot be expressed within the unified interface.

---

# Related Decisions

- [ADR-001 — Modular Monolith Architecture](001-modular-monolith.md)
- [ADR-002 — Plugin-Oriented Node Architecture](002-plugin-node-architecture.md)
- [ADR-004 — Event-Driven Communication](004-event-driven-communication.md)
