# AI Architecture

## Purpose

Define how AI capabilities are structured, governed, and integrated within the EngraphSW platform.

This document establishes the AI architecture — how AI operations are organized, where they live, how they are grounded in graph context, and how they remain explainable and auditable.

It does not define provider implementation details or prompt content.

---

## AI Philosophy

AI in EngraphSW is an engineering augmentation system, not an autonomous authority.

AI must be:

* **explainable** — every AI output must be traceable to its inputs and reasoning context
* **auditable** — all AI operations are logged with inputs, outputs, provider identity, and timing
* **reviewable** — AI-generated artifacts and suggestions require explicit user acceptance before becoming part of the graph
* **composable** — AI capabilities are modular, scoped to specific artifact operations, and combined through the graph

AI must not:

* make autonomous decisions that modify the graph without user review
* produce outputs that cannot be traced to grounded context
* operate as a black box without observable inputs and outputs
* replace engineering judgment — it augments it

See [ADR-005 — AI Provider Abstraction](../adr/005-ai-provider-abstraction.md).

---

## Two-Layer AI Architecture

AI capability in the platform is organized across two distinct layers.

### Layer 1 — AI Infrastructure (`ai/` module)

The `ai/` module owns the platform-level AI infrastructure:

* provider registration and routing
* request execution and response normalization
* streaming support
* token usage and latency tracking
* prompt template registry
* audit logging of all AI operations

This layer is domain-agnostic. It executes AI operations; it does not understand their engineering meaning.

See the `ai/` module definition in [modular-boundaries.md](modular-boundaries.md).

### Layer 2 — Domain AI Logic (artifact modules)

Each artifact domain module owns its AI capabilities:

* what AI operations are available on its artifact type
* the prompt content and templates for those operations
* the graph context selection strategy for those operations
* how AI outputs are validated against domain constraints
* how AI suggestions are integrated into the artifact lifecycle

This layer is domain-specific. It knows what a good requirement, story, or WBS package looks like and how to construct a meaningful AI request for one.

The domain layer invokes the infrastructure layer. The infrastructure layer has no knowledge of the domain.

---

## AI Operation Types

### Generation

Creating artifact content from graph context and user intent.

Examples:
- drafting functional requirements from persona and stakeholder context
- generating user stories from a requirement
- producing a WBS decomposition from a requirements set
- drafting a risk analysis from project context

Generation operations produce candidate artifacts. Candidates are staged for user review before being committed to the graph.

Generated artifacts must be structurally valid for their type before staging.

### Analysis

Evaluating existing artifacts for quality, consistency, or coverage.

Examples:
- checking requirement completeness against acceptance criteria standards
- identifying missing traceability links in a requirements set
- detecting inconsistencies between a persona and its derived stories
- analyzing WBS package coverage against a requirements scope

Analysis operations produce observations, not graph mutations. Observations are surfaced to the user. Graph changes resulting from analysis require explicit user action.

### Traceability Assistance

Reasoning over the graph to suggest relationships or identify gaps.

Examples:
- suggesting which requirements a new user story should trace to
- identifying requirements that have no downstream WBS coverage
- surfacing artifacts whose traceability chains are broken or orphaned
- recommending missing impact relationships between risks and WBS packages

Traceability assistance operates on the graph structure, not on isolated artifact content. It requires access to upstream and downstream graph context.

### Estimation Assistance

Providing heuristic support for function point analysis and effort quantification.

Examples:
- classifying functional requirements by function point category
- suggesting complexity weighting for WBS packages
- explaining estimation variance against historical patterns
- identifying estimation gaps in a scope decomposition

Estimation assistance outputs are advisory. They must be reviewed and accepted by a domain expert before influencing estimation artifact values.

### Graph Reasoning

Higher-order inference over the graph as a knowledge structure.

Examples:
- summarizing the engineering context of a subgraph for user review
- explaining the propagation chain that caused a staleness event
- identifying scope coverage gaps across the full artifact graph
- answering questions grounded in the current graph state

Graph reasoning requires the broadest context window and the most sophisticated prompt construction. It is the most expensive AI operation category and should be used selectively.

---

## Graph-Aware Context Construction

AI operations in EngraphSW are grounded in graph context, not in isolated record lookups.

This is the key architectural distinction from generic AI integrations.

### Context Selection

For any AI operation, the relevant graph context must be assembled before the prompt is constructed.

Context selection uses graph traversal:

* upstream traversal retrieves the sources and justification for the target artifact
* downstream traversal identifies what depends on and derives from the target
* lateral traversal identifies related artifacts in the same scope or category

Context selection is scoped. The full graph is not passed to the AI provider. Only the contextually relevant subgraph is assembled.

The traversal depth and scope depend on the operation type:

* generation operations typically need shallow upstream context (1–2 hops)
* analysis operations may need the full upstream chain for the target artifact
* graph reasoning operations may traverse multiple artifact categories

### Context Composition

Assembled context is composed into a structured prompt input alongside:

* the operation type and intent
* the target artifact's current state
* any explicit user instructions or constraints
* the prompt template for the operation

Context composition is the responsibility of the domain module, not the `ai/` infrastructure module.

### Context Window Discipline

The graph context must be constructed with token budget awareness.

Large graphs may produce context that exceeds provider context windows.

Context selection strategies must prioritize:

* direct relationships over transitive ones when budget is constrained
* higher-confidence relationships over speculative ones
* the user's immediate intent over comprehensive coverage

Context window management is a domain-layer responsibility. The `ai/` module tracks token usage but does not make context selection decisions.

---

## Prompt Architecture

### Prompts Are Owned by Domain Modules

The `ai/` module maintains a prompt template registry.

Prompt content — the actual instructions, structure, and artifact-type-specific guidance — is owned by the artifact domain module that uses it.

Domain modules register their prompts with the `ai/` module at initialization.

Prompts are never hardcoded at call sites.

### Prompt Structure

Each prompt template defines:

* the operation type it supports
* the expected context inputs (what graph context it consumes)
* the output structure (what shape of response is expected)
* any constraints or validation rules for the output
* the minimum provider capability required

### Prompt Versioning

Prompts are versioned.

Changes to prompt content produce a new prompt version. Prior versions are retained for audit purposes — to explain why an AI output looked the way it did at a given time.

AI audit records reference the prompt version used at execution time.

---

## AI Execution Model

### Synchronous Operations

Short-duration AI operations execute synchronously within the request lifecycle.

Appropriate for:
* single-artifact generation with shallow context
* quick analysis and validation checks
* traceability suggestions on small subgraphs

Synchronous operations must respect reasonable latency budgets. An AI call that exceeds acceptable latency thresholds for a synchronous context should be converted to an asynchronous operation.

### Asynchronous Operations

Long-duration AI operations execute as background jobs via BullMQ.

Appropriate for:
* generation across large artifact sets
* full graph reasoning operations
* batch analysis across multiple artifacts
* operations that require multiple sequential AI calls

Asynchronous operations:
* are submitted as jobs to the execution queue
* produce their outputs to a staging area for user review
* notify the user upon completion through the platform's notification model
* are cancellable by the user during execution

### Streaming

Streaming is supported for generation operations where real-time output display is valuable.

Streaming requires streaming-capable provider configuration through the `ai/` module.

Streamed outputs are buffered and committed as a complete record upon stream completion. Partial outputs are not committed to the graph.

---

## AI Output Lifecycle

AI outputs follow a consistent lifecycle before entering the graph:

```
AI Operation Executes
|
Output Produced and Validated Against Domain Constraints
|
Output Staged as Candidate (not committed to graph)
|
User Reviews Candidate
|
User Accepts -> Candidate Committed to Graph as Artifact or Annotation
User Rejects -> Candidate Discarded
User Edits -> Modified Candidate Committed
```

AI outputs must never bypass user review and be committed directly to the graph.

The only exception is explicit user configuration that pre-authorizes a specific automated operation — which must itself be an explicit, auditable user decision.

---

## Observability and Audit

Every AI operation produces an audit record.

### Audit Record

Each record captures:

* operation ID
* operation type
* invoking module and artifact
* provider used
* model used
* prompt version
* token usage (input and output)
* latency
* operation result (success, failed, cancelled)
* output hash (for integrity verification)
* timestamp

Audit records are immutable and append-only.

### Usage Tracking

Token usage is tracked per operation, per module, and per workspace.

Usage tracking supports:

* cost visibility
* per-workspace consumption analysis
* provider budget management
* anomaly detection

---

## Provider Strategy

The platform supports two primary AI providers:

| Provider | Role |
|---|---|
| Anthropic (Claude) | Primary provider for complex reasoning, generation, and graph intelligence operations |
| OpenAI (GPT) | Secondary provider, available for specific operation types or as fallback |

Provider selection per operation is configured through the `ai/` module.

Domain modules may declare a preferred provider for specific operation types based on capability requirements.

The `ai/` module handles fallback routing when the preferred provider is unavailable or rate-limited.

Provider credentials are infrastructure configuration. They must not appear in application code or prompt templates.

---

## AI and Module Boundaries

AI capability observes the same module boundary rules as all other platform concerns.

* The `ai/` module provides the infrastructure; domain modules provide the intelligence
* Domain modules invoke the `ai/` module through its application service interface
* AI-generated events follow the outbox pattern for reliable publishing
* AI audit records are persisted in `ai_*` tables owned by the `ai/` module
* Domain modules must not bypass the `ai/` module to call provider SDKs directly

---

## Future Extensibility

The AI architecture should evolve to support:

* **Multi-step AI workflows** — chained operations where the output of one AI step becomes the input of the next, guided by graph traversal
* **AI reasoning chains over graph context** — structured reasoning over the semantic graph rather than single-shot prompting
* **Semantic caching** — caching AI responses for equivalent graph contexts to reduce redundant provider calls
* **Provider capability negotiation** — selecting providers based on declared capability requirements rather than static configuration
* **AI-assisted propagation reasoning** — using AI to explain or summarize propagation chains for user comprehension
* **Cross-workspace AI operations** — AI reasoning over artifact relationships that span multiple workspaces

These capabilities build on the two-layer architecture established here.

The domain-AI separation must be preserved as these capabilities are introduced — more sophisticated AI operations remain the responsibility of domain modules invoking a more capable infrastructure layer.
