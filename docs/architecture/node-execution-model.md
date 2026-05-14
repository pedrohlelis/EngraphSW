# Node Execution Model

## Purpose

Define how nodes execute, recalculate, and transition through lifecycle states inside the graph.

## Execution States

- idle: no active execution is in progress
- running: execution is actively processing
- success: execution completed successfully
- failed: execution ended with an error or invalid result
- stale: outputs may no longer reflect current upstream context

## Lifecycle

- nodes may execute manually or in response to dependency changes
- execution should record inputs, outputs, status, and timing metadata
- lifecycle transitions should be explicit and auditable

## Recalculation Semantics

- recalculation should happen when upstream data changes or dependency links are invalidated
- recalculation should preserve prior execution history where possible
- downstream nodes should clearly indicate when their state no longer matches source context

## Stale-State Behavior

- stale state signals that a node may need recomputation or review
- stale state should not erase prior results
- stale state should propagate through dependent nodes when appropriate

## Async Execution Concepts

- long-running work should support asynchronous execution
- async execution should allow retries, cancellation, and progress reporting where relevant
- execution results should remain observable across background processing boundaries

## Future Extensibility

- support queued and dependency-ordered execution later
- support replay, diagnostics, and execution comparison
- support richer node-specific execution policies