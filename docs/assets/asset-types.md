# AI Asset Types

## Primary Assets (Current Priority)

### MCP Servers
Runtime integrations and tool-serving capabilities. May be internally developed, partner-provided, or third-party sourced. Require strong governance around enablement, connectivity, security, policy, lifecycle, and gateway alignment.

**Registry needs**: Identity, version, source, endpoints, transport, tool/resource definitions, auth info, lifecycle state, certification, trust tier, health context.

### Agents
Composed runtime capabilities that reference other governed assets (models, prompts, MCP servers, skills, guardrails, knowledge sources). Important for governing both the agents themselves and the relationships between their component assets.

**Registry needs**: Identity, version, framework, referenced assets, lifecycle state, deployment associations, evaluation results, cost tracking.

### Models
Foundational AI assets. Currently managed via Kubeflow Model Registry; future alignment with MLflow-based direction.

**Registry needs**: Identity, version, format, serving endpoint, lineage, evaluation metrics, deployment state.

### Prompts
Reusable platform assets (not just embedded implementation details). MLflow already has Prompt Registry support.

**Registry needs**: Identity, version, template content, variables, associated agents/workflows, lifecycle state.

### Skills
Reusable AI-enabled capabilities attachable to agents or workflows. Packaging format not yet standardized.

**Registry needs**: Identity, version, packaging format, inputs/outputs, associated agents, lifecycle state.

**Open question**: OCI artifacts vs markdown files vs zip bundles for packaging.

### Guardrails
Safety, behavioral, or governance boundaries for AI interactions. RHOAI 3.4 ships MCP guardrail Dev Preview.

**Registry needs**: Identity, version, rule definitions, associated assets, enforcement points, lifecycle state.

### Knowledge Sources
Governed information sources for RAG, agents, retrieval workflows. May include documents, repositories, connectors, structured systems.

**Registry needs**: Identity, version, source reference, access model, trust/governance context, refresh schedule.

## Secondary/Future Assets

| Asset Type | Description | Registry Relevance |
|---|---|---|
| Notebooks | Development/experimentation assets | Governed workflows, reference implementations |
| Pipelines | Repeatable AI workflows | Lifecycle, reuse, governance |
| Evaluators | Assessment/validation/benchmarking | Governed evaluation criteria |
| Skill Packs | Grouped capabilities (multiple skills) | Packaging and governance beyond individual skills |
| Code Snippets | Reusable AI-related logic | Shared governed implementation fragments |
| Workflows | Composed assets defining multi-component outcomes | Lifecycle, relationship tracking |
