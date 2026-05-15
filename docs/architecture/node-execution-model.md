# Node Execution Model

## Related Documents

* [ADR-002 Plugin-Oriented Node Architecture](../adr/002-plugin-node-architecture.md) — the architectural decision this model implements
* [Architecture: Modular Boundaries](modular-boundaries.md) — defines the `nodes/` and `execution/` module boundaries this model operates within
* [Architecture: Dependency Propagation](dependency-propagation.md) — defines how stale state propagates when node execution invalidates downstream nodes
* [Architecture: Initial Schema](initial-schema.md) — defines the `node_nodes`, `exec_execution_logs`, and `exec_staleness_records` tables that back execution state
* [Architecture: Module Conventions](module-conventions.md) — defines where execution services, use cases, and queue workers live in the file system

---

## Purpose

Define how nodes execute, recalculate, and transition through lifecycle states. This document defines the contracts an Implementer needs to build the node execution system: the `NodeDefinition` interface, the execution state machine, the execution context structure, and the plugin registration contract.

---

## NodeDefinition Interface

Every node type registered in the system implements the `NodeDefinition` interface. This is the typed contract a node plugin must fulfill. At persistence time, a node instance's `type` and `content` fields map directly to the [`node_nodes`](initial-schema.md) table columns.

```typescript
interface NodeDefinition<TContent = Record<string, unknown>, TOutput = Record<string, unknown>> {
  // Unique identifier for this node type (e.g., "requirement", "persona", "estimation")
  type: string;

  // Human-readable display name shown in the UI
  displayName: string;

  // Zod schema that validates the node's content field
  // Used at creation time, update time, and before execution
  contentSchema: ZodType<TContent>;

  // Describes the inputs this node consumes from upstream nodes
  // An empty array means the node has no upstream dependencies
  inputs: NodeInputDefinition[];

  // Describes the outputs this node produces for downstream nodes
  // An empty array means the node produces no output consumed by downstream nodes
  outputs: NodeOutputDefinition[];

  // Execute this node given its current content and upstream outputs
  // Returns the output this node produces, or throws on failure
  execute: (context: NodeExecutionContext<TContent>) => Promise<TOutput>;

  // Optional: validate that this node is ready to execute
  // Called before execute(); if it returns a non-empty array, execution is blocked
  canExecute?: (context: NodeExecutionContext<TContent>) => ValidationError[];

  // Optional: React component key for the canvas node renderer
  // Defaults to "DefaultNodeRenderer" if not provided
  rendererKey?: string;
}

interface NodeInputDefinition {
  key: string;              // identifies this input slot; matches output key of upstream node
  label: string;            // displayed in the UI
  required: boolean;
  allowedNodeTypes?: string[];  // if set, only these node types may connect to this input
}

interface NodeOutputDefinition {
  key: string;              // identifies this output slot; consumed by downstream node inputs
  label: string;
  type: 'text' | 'number' | 'json' | 'reference';
}

interface ValidationError {
  field: string;
  message: string;
}
```

---

## NodeExecutionContext

The data structure available to a node at execution time. Passed as the sole argument to `execute()` and `canExecute()`.

```typescript
interface NodeExecutionContext<TContent> {
  // The node's own persisted content (validated against contentSchema)
  content: TContent;

  // Outputs from connected upstream nodes, keyed by output slot key
  // Only populated for edges that exist in the current graph
  upstreamOutputs: Record<string, unknown>;

  // The ID of the node being executed
  nodeId: string;

  // The ID of the graph this node belongs to
  graphId: string;

  // AI provider reference — use to invoke AI operations within execute()
  // Routes through the ai/ module's abstraction; no direct provider SDK access
  ai: AIProviderRef;

  // Batch ID if this execution is part of a propagation batch
  batchId: string | null;
}

// Opaque reference to the AI provider — consumed by execute() implementations
// Actual interface defined by the ai/ module
interface AIProviderRef {
  generate(params: AIGenerateParams): Promise<AIGenerateResult>;
  analyze(params: AIAnalyzeParams): Promise<AIAnalyzeResult>;
}
```

---

## Execution State Machine

### States

| State | Meaning |
|---|---|
| `IDLE` | No execution in progress. No prior execution, or prior execution result is current. |
| `RUNNING` | Execution is actively processing. The node was triggered and has not yet resolved. |
| `SUCCESS` | Most recent execution completed successfully. Outputs are current and valid. |
| `FAILED` | Most recent execution ended with an error. Prior outputs (if any) are retained but marked invalid. |
| `STALE` | Outputs may no longer reflect current upstream context. Prior execution result still readable. |

### Transitions

| From | To | Trigger |
|---|---|---|
| `IDLE` | `RUNNING` | User triggers execution or system triggers execution via dependency change |
| `SUCCESS` | `RUNNING` | User re-executes or dependency change triggers recalculation |
| `FAILED` | `RUNNING` | User retries execution |
| `STALE` | `RUNNING` | User triggers recalculation of stale node |
| `RUNNING` | `SUCCESS` | `execute()` resolves without error |
| `RUNNING` | `FAILED` | `execute()` throws or returns invalid output |
| `SUCCESS` | `STALE` | An upstream node's output changes (see [Dependency Propagation](dependency-propagation.md)) |
| `FAILED` | `STALE` | An upstream node's output changes while the node is in FAILED state |
| `STALE` | `STALE` | Staleness propagates transitively from a deeper upstream change |
| `RUNNING` | `STALE` | An upstream change occurs while execution is already in flight (rare; handle as abort + STALE) |

### Invariants

- A node in `RUNNING` state must have exactly one active `exec_execution_logs` record with no `completed_at`
- Transitioning to `RUNNING` creates a new `exec_execution_logs` record
- Transitioning to `SUCCESS` or `FAILED` sets `completed_at` on the active log record
- Transitioning to `STALE` creates an `exec_staleness_records` record
- Prior execution outputs are never erased; they remain in `exec_execution_logs` history

---

## Plugin Registration Contract

Node types are registered with the node registry at application startup. The registry is owned by the `nodes/` module.

```typescript
// Interface for the node type registry
interface NodeRegistry {
  // Register a node type. Called once at startup for each built-in and plugin node type.
  // Throws if a node type with the same key is already registered.
  register(definition: NodeDefinition): void;

  // Retrieve a registered node type by key.
  // Throws if the type is not registered.
  get(type: string): NodeDefinition;

  // Check whether a node type is registered.
  has(type: string): boolean;

  // Return all registered node type keys.
  list(): string[];
}
```

### Registration Location

Built-in node type registrations live in `modules/nodes/domain/NodeTypeRegistry.ts`.

Registration calls happen in the application initialization sequence before any request handlers are active.

Third-party plugin registration (future) follows the same contract but is loaded from a plugin manifest at startup.

### What registration does NOT do

Registration does not:
- create database records
- allocate resources
- perform side effects beyond populating the in-memory registry

The registry is stateless infrastructure populated at startup. It does not persist to the database.

---

## Execution Flow

The execution sequence for a single node:

```
1. Application layer calls ExecuteNodeUseCase with { nodeId }
2. UseCase loads node from repository
3. UseCase fetches upstream outputs for each connected input slot
4. UseCase calls registry.get(node.node_type).canExecute(context)
   - If validation errors returned: abort, mark FAILED, log error
5. UseCase transitions node to RUNNING; creates exec_execution_log record
6. UseCase calls registry.get(node.node_type).execute(context)
   - On success: transition to SUCCESS; store output in log; publish NodeExecutionCompletedEvent
   - On error: transition to FAILED; store error in log; publish NodeExecutionFailedEvent
7. Dependency propagation handles downstream staleness (see dependency-propagation.md)
```

Batch execution (multiple nodes from a propagation cascade) follows the same per-node flow but groups log records under a shared `batch_id` and respects topological execution order.

---

## Async Execution

Long-running node executions (AI-assisted operations, complex analyses) use the BullMQ queue in the `execution/` module rather than synchronous processing.

For async execution:

1. `ExecuteNodeUseCase` enqueues a job in the execution queue instead of calling `execute()` directly
2. Node transitions to `RUNNING` immediately
3. The execution worker picks up the job, calls `execute()`, and completes the state transition
4. The client polls or subscribes to real-time execution state updates

Async executions support:
- **Retry**: configurable retry count on transient failure, tracked in `exec_execution_logs.retry_count`
- **Cancellation**: a cancelled job transitions the node from RUNNING back to its prior state (or FAILED if no prior state)
- **Progress reporting**: workers may emit progress events consumed by the real-time layer

---

## Integration with Dependency Propagation

When a node transitions to `SUCCESS`, the execution system emits `NodeExecutionCompletedEvent`.

The `traceability/` module subscribes to this event and triggers staleness propagation for all downstream nodes that depend on this node's output. Staleness propagation is not the responsibility of the `execution/` module — it hands off via event.

See [Dependency Propagation](dependency-propagation.md) for the propagation algorithm, batch semantics, and staleness boundary rules.

---

## Stale State Behavior

- Stale state is a signal, not an error
- A stale node retains its prior outputs; they are not erased
- Stale state persists across session boundaries (see `exec_status` column on `node_nodes`)
- Stale state propagates transitively through the dependency graph
- The user must explicitly trigger re-execution to resolve stale state (no automatic background recomputation)
- Stale nodes display a visual indicator in the canvas (implementation: canvas-engine.md)

---

## Future Extensibility

- **Queued and dependency-ordered execution**: batch execution across a dependency subgraph, respecting topological order
- **Replay and diagnostics**: re-run a node with a historical `input_snapshot` from `exec_execution_logs`
- **Execution comparison**: diff outputs across two execution log records for the same node
- **Node-specific execution policies**: per-node type retry behavior, timeout, and AI model selection
