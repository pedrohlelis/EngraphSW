# AI Intelligence Layer

**Community 10** · 7 nodes · cohesion=0.33

## Nodes

- **AI Audit Record** [`concept`] — `docs/architecture/ai-architecture.md`, loc=## Observability and Audit ### Audit Record
- **Graph-Aware Context Construction** [`concept`] — `docs/architecture/ai-architecture.md`, loc=## Graph-Aware Context Construction
- **Domain AI Logic Layer** [`concept`] — `docs/architecture/ai-architecture.md`, loc=## Two-Layer AI Architecture ### Layer 2
- **AI Graph Reasoning** [`concept`] — `docs/architecture/ai-architecture.md`, loc=## AI Operation Types ### Graph Reasoning
- **AI Infrastructure Layer (ai/ module)** [`concept`] — `docs/architecture/ai-architecture.md`, loc=## Two-Layer AI Architecture ### Layer 1
- **Prompt Template Registry** [`concept`] — `docs/architecture/ai-architecture.md`, loc=## Prompt Architecture
- **AI Provider Strategy** [`concept`] — `docs/architecture/ai-architecture.md`, loc=## Provider Strategy

## Internal Edges

  - AI Audit Record --references--> AI Infrastructure Layer (ai/ module) [EXTRACTED 1.0]
  - Domain AI Logic Layer --calls--> AI Infrastructure Layer (ai/ module) [EXTRACTED 1.0]
  - Domain AI Logic Layer --references--> Graph-Aware Context Construction [EXTRACTED 1.0]
  - AI Graph Reasoning --references--> Graph-Aware Context Construction [EXTRACTED 1.0]
  - AI Infrastructure Layer (ai/ module) --references--> Prompt Template Registry [EXTRACTED 1.0]
  - AI Infrastructure Layer (ai/ module) --references--> AI Audit Record [EXTRACTED 1.0]
  - AI Infrastructure Layer (ai/ module) --references--> AI Provider Strategy [EXTRACTED 1.0]
  - Prompt Template Registry --shares_data_with--> Domain AI Logic Layer [EXTRACTED 1.0]

## Cross-Community Edges

  - Graph-Aware Context Construction --references--> modular_boundaries_graph [EXTRACTED] (``)
  - Domain AI Logic Layer --shares_data_with--> modular_boundaries_requirements [INFERRED] (``)
  - Domain AI Logic Layer --shares_data_with--> modular_boundaries_estimation [INFERRED] (``)
  - Domain AI Logic Layer --shares_data_with--> modular_boundaries_user_stories [INFERRED] (``)
  - Domain AI Logic Layer --shares_data_with--> modular_boundaries_personas [INFERRED] (``)
  - Domain AI Logic Layer --shares_data_with--> modular_boundaries_risk [INFERRED] (``)
  - AI Infrastructure Layer (ai/ module) --implements--> modular_boundaries_ai [EXTRACTED] (``)

[Back to index](index.md)
