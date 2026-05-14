# AI Workflows Skill

## Purpose

Standardize AI-assisted engineering workflows.

---

# AI Philosophy

AI operations are engineering actions.

They must be:
- auditable
- reproducible
- observable
- structured

---

# Provider Architecture

Support:
- OpenAI
- Anthropic

Avoid:
- provider lock-in
- provider-specific domain logic

---

# AI Requirements

All AI operations should support:
- retry policies
- rate limiting
- token tracking
- provider fallback
- execution logging
- prompt versioning

---

# Prompt Design

Favor:
- modular prompts
- reusable prompt templates
- structured outputs
- deterministic workflows

Avoid:
- giant monolithic prompts
- hidden prompt logic
- unstructured outputs

---

# AI Execution

AI executions should:
- run asynchronously
- persist metadata
- support cancellation
- support observability

---

# Important Rules

AI should augment engineering workflows,
not replace domain structure or traceability integrity.

## Heuristics

- version prompts when behavior changes materially
- treat every AI call as an auditable engineering action
- record provider, model, prompt version, inputs, outputs, and retry history
- keep prompts modular so generation, validation, and summarization can evolve independently
- prefer structured outputs that can be validated before use
- use fallback and retry logic only in ways that preserve determinism and observability