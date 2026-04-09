# AI Asset Registry - Core Concepts

## AI Asset
A governed unit within the platform representing a component of an AI solution: agents, MCP servers, prompts, models, guardrails, knowledge sources, skills.

## Registry
The governance and lifecycle management capability. Manages metadata and references: identity, versioning, lifecycle state, policy linkage, lineage, approvals, auditability. Does **not** store the actual asset payload.

## Catalog
The discoverability and consumption capability. Helps users browse, search, filter, and select appropriate assets. Separate from but complementary to the registry.

## Plugin
Extension mechanism allowing the registry framework to support new AI asset types. Each plugin defines its own metadata, configuration, relationships, lifecycle, and governance requirements while participating in the shared framework.

## Unified Interface
Common experience for interacting with multiple AI asset registries - APIs, CRDs, UI, CLI. Consistent product experience even when underlying registries differ.

## Federated Registry Model
Multiple backend registries exist, but the product presents them through a common framework. Different asset types use appropriate backends while participating in a broader governance model.

## Metadata-First Governance
Registry focuses on descriptive, operational, and governance metadata:
- Identity, version, owner, source
- Endpoint or location reference
- Lifecycle state
- Policy association
- Access model
- Relationships to other assets
- Audit and observability context

## Dev Registry vs Prod Registry
- **Dev Registry**: Supports experimentation, iterative updates, local-to-cluster workflows, earlier lifecycle stages
- **Prod Registry**: Stronger requirements around promotion, approval, immutability, governance, security, policy enforcement

## Inner Loop to Outer Loop
Progression from local/early development into shared, governed, production-ready environments. Registry supports this via asset portability, promotion, lineage tracking.

## Policy Linkage
Association of an AI asset with the policies/controls that apply to it. Registry carries the metadata; enforcement occurs through gateways, policy engines, controllers.

## Gateway Enforcement
Runtime controls through gateway technologies (MCP Gateway, inference gateway, future agent gateway). Registry provides the metadata and policy context; gateways enforce.

## Relationship Model
Meaningful connections between AI assets: agent -> model, prompt, MCP server, guardrail, knowledge source. Critical for governance, lifecycle coordination, and composed solutions.

## AI Available Assets (AAA)
RBAC-scoped consumption surface for AI Engineers. Shows only the assets a user is entitled to consume based on governance state and access policies.
