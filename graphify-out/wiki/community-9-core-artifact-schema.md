# Core Artifact Schema

**Community 9** · 9 nodes · cohesion=0.53

## Nodes

- **Estimation Artifact** [`document`] — `docs/domain/estimation-domain.md`
- **Requirement Artifact** [`document`] — `docs/domain/functional-requirements-domain.md`
- **exec_staleness_records Table** [`document`] — `docs/architecture/initial-schema.md`
- **node_nodes Table** [`document`] — `docs/architecture/initial-schema.md`
- **Persona Artifact** [`document`] — `docs/domain/personas-domain.md`
- **Risk Artifact** [`document`] — `docs/domain/risk-analysis-domain.md`
- **Stakeholder Artifact** [`document`] — `docs/domain/stakeholder-domain.md`
- **Prisma ORM** [`document`] — `docs/architecture/tech-stack.md`
- **User Story Artifact** [`document`] — `docs/domain/user-stories-domain.md`

## Internal Edges

  - Estimation Artifact --conceptually_related_to--> Requirement Artifact [EXTRACTED 1.0]
  - Estimation Artifact --conceptually_related_to--> User Story Artifact [EXTRACTED 1.0]
  - Estimation Artifact --shares_data_with--> node_nodes Table [INFERRED 0.85]
  - Requirement Artifact --conceptually_related_to--> Risk Artifact [EXTRACTED 1.0]
  - Requirement Artifact --shares_data_with--> node_nodes Table [INFERRED 0.85]
  - node_nodes Table --shares_data_with--> exec_staleness_records Table [EXTRACTED 1.0]
  - Persona Artifact --conceptually_related_to--> Requirement Artifact [EXTRACTED 1.0]
  - Persona Artifact --conceptually_related_to--> User Story Artifact [EXTRACTED 1.0]
  - Persona Artifact --conceptually_related_to--> Stakeholder Artifact [EXTRACTED 0.9]
  - Persona Artifact --shares_data_with--> node_nodes Table [INFERRED 0.85]
  - Risk Artifact --conceptually_related_to--> Estimation Artifact [EXTRACTED 1.0]
  - Risk Artifact --conceptually_related_to--> Requirement Artifact [EXTRACTED 1.0]
  - Risk Artifact --conceptually_related_to--> User Story Artifact [EXTRACTED 1.0]
  - Risk Artifact --conceptually_related_to--> Stakeholder Artifact [EXTRACTED 1.0]
  - Risk Artifact --shares_data_with--> node_nodes Table [INFERRED 0.85]
  - Stakeholder Artifact --conceptually_related_to--> Requirement Artifact [EXTRACTED 1.0]
  - Stakeholder Artifact --conceptually_related_to--> Persona Artifact [EXTRACTED 1.0]
  - Stakeholder Artifact --conceptually_related_to--> Risk Artifact [EXTRACTED 1.0]
  - Stakeholder Artifact --shares_data_with--> node_nodes Table [INFERRED 0.85]
  - Prisma ORM --references--> node_nodes Table [EXTRACTED 0.9]
  - User Story Artifact --conceptually_related_to--> Requirement Artifact [EXTRACTED 1.0]
  - User Story Artifact --conceptually_related_to--> Risk Artifact [EXTRACTED 1.0]
  - User Story Artifact --shares_data_with--> node_nodes Table [INFERRED 0.85]

## Cross-Community Edges

  - node_nodes Table --shares_data_with--> exec_execution_logs Table [EXTRACTED] (`docs/architecture/initial-schema.md`)
  - node_nodes Table --shares_data_with--> graph_edges Table [EXTRACTED] (`docs/architecture/initial-schema.md`)

[Back to index](index.md)
