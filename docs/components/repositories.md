# Repository Reference

## Working Repositories

### kubernetes-sigs/mcp-lifecycle-operator
**Purpose**: Kubernetes operator providing declarative lifecycle management for MCP servers via MCPServer CRD.

**Key Details**:
- MCPServer CRD (v1alpha1): container image, port, HTTP path, env vars, storage mounts, replicas, security contexts, health probes
- Creates Deployment + ClusterIP Service per MCPServer resource
- Service URL: `http://<name>.<namespace>.svc.cluster.local:<port>/<path>`
- Lifecycle phases: Pending, Running, Failed
- Built with controller-runtime and Kubebuilder v4.11.1
- **No direct integration** with registries, catalogs, or gateways currently
- Actively maintained (last commit April 2026)

**Registry relevance**: This is the deployment primitive. The registry should represent governed MCPs with their deployment associations, but the operator doesn't currently integrate with any registry or catalog.

---

### Kuadrant/mcp-gateway
**Purpose**: Envoy-based MCP aggregation platform providing secure, policy-enforced gateway for multiple MCP servers.

**Architecture** (single binary, three components):
- **MCP Router**: Envoy ext_proc (gRPC :50051) - parses JSON-RPC, validates payloads, sets routing headers
- **MCP Broker**: HTTP service (:8080/mcp) - aggregates tools, handles initialize/tools/list, identity-based filtering
- **MCP Controller**: K8s controller watching MCPServerRegistration CRDs, discovering backends via HTTPRoutes

**Key CRDs**:
- MCPServerRegistration: Registers server with gateway (toolPrefix, targetRef to HTTPRoute)
- MCPVirtualServer: Curated tool collections accessed via X-Mcp-Virtualserver headers

**Features**:
- Tool federation with namespace prefixes to avoid naming conflicts
- Identity-aware filtering via signed JWT (x-authorized-tools header)
- Per-tool auth via Kuadrant AuthPolicy
- OpenTelemetry integration (mcp_tool_calls_total, mcp_tool_call_duration_seconds)
- Session management (gateway-level + backend MCP sessions)
- Operator-based installation (OLM bundle)

**Integration**: Gateway API native, Istio as Gateway provider (no mesh), Kuadrant for auth. No tight coupling to registries - designed for flexibility.

---

### kubeflow/model-registry (being renamed to kubeflow/hub)
**Purpose**: Metadata management system for ML assets, evolving into a broader AI asset hub.

**Architecture**:
- GORM with MySQL/PostgreSQL backends
- Logical hierarchy: RegisteredModel -> ModelVersion -> ModelArtifacts
- REST API at `/api/model_registry/v1alpha3/` with OpenAPI specs
- Generic `customProperties` (typed key-value metadata) for extensibility
- Storage pointers: URI + storage_key + storage_path for cloud-agnostic references (S3, GitHub, HuggingFace)

**Key Evolution - "Kubeflow Hub" (KEP-0003)**:
- Being renamed from model-registry to kubeflow/hub
- Container images moving to `ghcr.io/kubeflow/hub/*`
- Catalog service already supports MCP servers alongside models (`assetType` query parameter)
- Future plans: datasets, prompts, notebooks as additional asset types
- Umbrella for AI asset management beyond just models

**Current Capabilities**:
- RegisteredModels, ModelVersions, ModelArtifacts
- InferenceServices, Experiments, ExperimentRuns
- Metrics, Parameters, generic Artifacts
- SQL-like filtering (LIKE, IN, AND/OR), pagination, sorting
- Python SDK for high-level API access

**Integration**:
- Integrated into OpenShift AI (RHOAI)
- Compatible with KServe InferenceService specs
- No direct MLflow integration, but supports MLflow metadata patterns
- Catalog service provides read-only federated discovery

**Registry relevance**: The Kubeflow Hub evolution and catalog service's MCP server support are directly relevant. The `assetType` filtering and generic artifact model provide the extensibility foundation for the AI Asset Registry proposal. This is where the catalog-side work happens.

---

## Reference Repositories

### opendatahub-io/architecture-context
**Purpose**: Automated architecture documentation pipeline for RHOAI/OpenDataHub.

**Key Details**:
- CI/CD tool that clones ~49 ODH/RHOAI component repos
- Uses Claude agents to generate structured architecture summaries
- Produces platform-level docs, Mermaid diagrams, C4 models
- Tracks by RHOAI version (2.6 through 3.4-ea.2)
- 6-phase pipeline: fetch, parse-manifests, generate-architecture, collect, aggregate, generate-diagrams
- Documents Model Registry component including MCP server catalog support

**Registry relevance**: Provides automated documentation of the full RHOAI component inventory (45 components across 11 domains in 3.4). Useful for understanding the broader platform context the registry operates in.

---

### opendatahub-io/ai-helpers
**Purpose**: Collaborative marketplace for AI plugins, skills, commands, and agents for multiple AI platforms.

**Key Details**:
- Tool types: Skills (agentskills.io format), Commands, Agents, Gemini Gems
- Centralized category registry (categories.yaml)
- Installable via Claude Code marketplace
- Includes MCP-related tools: TorchTalk MCP, JIRA MCP
- RHOAI-connected: RHOAIENG project examples, Konflux components, OCI CVE checking

**Registry relevance**: Functions as a decentralized asset registry and discovery system - tools cataloged, versioned through Git, distributed via marketplace plugins. Models one approach to asset management and distribution.
