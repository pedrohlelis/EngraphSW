# Platform Module Structure

**Community 5** · 16 nodes · cohesion=0.19

## Nodes

- **exec_execution_logs Table** [`document`] — `docs/architecture/initial-schema.md`
- **AI Module** [`document`] — `docs/architecture/module-conventions.md`
- **Execution Module** [`document`] — `docs/architecture/module-conventions.md`
- **Graph Module** [`document`] — `docs/architecture/module-conventions.md`
- **Nodes Module** [`document`] — `docs/architecture/module-conventions.md`
- **Shared Module** [`document`] — `docs/architecture/module-conventions.md`
- **Traceability Module** [`document`] — `docs/architecture/module-conventions.md`
- **NodeCreatedEvent** [`document`] — `docs/architecture/module-conventions.md`
- **AIProviderRef** [`document`] — `docs/architecture/node-execution-model.md`
- **ExecuteNodeUseCase** [`document`] — `docs/architecture/node-execution-model.md`
- **NodeDefinition Interface** [`document`] — `docs/architecture/node-execution-model.md`
- **NodeExecutionContext** [`document`] — `docs/architecture/node-execution-model.md`
- **NodeRegistry Interface** [`document`] — `docs/architecture/node-execution-model.md`
- **Anthropic SDK** [`document`] — `docs/architecture/tech-stack.md`
- **BullMQ** [`document`] — `docs/architecture/tech-stack.md`
- **OpenAI SDK** [`document`] — `docs/architecture/tech-stack.md`

## Internal Edges

  - Execution Module --references--> Nodes Module [EXTRACTED 1.0]
  - Execution Module --references--> AI Module [EXTRACTED 1.0]
  - Execution Module --references--> Graph Module [EXTRACTED 1.0]
  - Execution Module --references--> Shared Module [EXTRACTED 1.0]
  - Execution Module --references--> ExecuteNodeUseCase [EXTRACTED 0.92]
  - Graph Module --references--> Shared Module [EXTRACTED 1.0]
  - Nodes Module --references--> AI Module [EXTRACTED 1.0]
  - Nodes Module --references--> Shared Module [EXTRACTED 1.0]
  - Nodes Module --references--> NodeRegistry Interface [EXTRACTED 0.92]
  - Traceability Module --references--> Graph Module [EXTRACTED 1.0]
  - Traceability Module --references--> Execution Module [EXTRACTED 1.0]
  - Traceability Module --references--> Shared Module [EXTRACTED 1.0]
  - NodeCreatedEvent --references--> Nodes Module [EXTRACTED 1.0]
  - AIProviderRef --references--> AI Module [EXTRACTED 0.92]
  - ExecuteNodeUseCase --calls--> NodeRegistry Interface [EXTRACTED 0.95]
  - ExecuteNodeUseCase --shares_data_with--> exec_execution_logs Table [EXTRACTED 0.9]
  - ExecuteNodeUseCase --calls--> NodeCreatedEvent [EXTRACTED 0.9]
  - NodeDefinition Interface --references--> NodeExecutionContext [EXTRACTED 0.95]
  - NodeExecutionContext --references--> AIProviderRef [EXTRACTED 0.95]
  - NodeRegistry Interface --references--> NodeDefinition Interface [EXTRACTED 0.95]
  - Anthropic SDK --references--> AI Module [EXTRACTED 0.95]
  - BullMQ --references--> Execution Module [EXTRACTED 0.92]
  - OpenAI SDK --references--> AI Module [EXTRACTED 0.95]

## Cross-Community Edges

  - Traceability Module --implements--> TraversalService [EXTRACTED] (`docs/architecture/traceability-model.md`)
  - NodeDefinition Interface --shares_data_with--> node_nodes Table [INFERRED] (`docs/architecture/initial-schema.md`)

[Back to index](index.md)
