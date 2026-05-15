# Information Boundaries

The repository contains multiple cognitive layers.

Not all repository documents are intended for implementation-time reasoning.

==================================================
IMPLEMENTER VISIBILITY RULES
==================================================

The implementer SHOULD routinely consult:
- CLAUDE.md
- CURRENT_PHASE.md
- relevant ADRs
- relevant architecture docs
- relevant domain docs
- relevant instructions
- relevant skills

The implementer SHOULD NOT routinely consult:
- docs/meta/repository-cognition.md
- docs/meta/ai-role-separation.md
- docs/meta/orchestration-model.md
- docs/meta/repository-governance.md

unless explicitly instructed by the user.

==================================================
RATIONALE
==================================================

Meta documents define orchestration and governance philosophy.

They are intended primarily for:
- repository governance
- orchestration reasoning
- architectural supervision

They are not intended to be recursively reinterpreted during implementation execution.

==================================================
IMPLEMENTATION PRIORITY
==================================================

Implementation work should prioritize:
- active phase execution
- architectural correctness
- stable contracts
- maintainable code
- bounded context integrity

Avoid:
- governance reinterpretation
- repository philosophy redesign
- orchestration-layer modifications

==================================================
ESCALATION RULE
==================================================

If implementation work appears to conflict with governance constraints:
- do not reinterpret governance autonomously
- surface the conflict explicitly
- request user clarification