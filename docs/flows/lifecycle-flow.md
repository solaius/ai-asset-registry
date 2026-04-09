# AI Asset Lifecycle Flows

## MCP Server Lifecycle (End-to-End)

### Stages
```
Sources -> Quarantine -> Registry -> Catalog -> Deployments -> Consumable
```

### Lifecycle States
```
Draft -> Candidate -> Verified -> Approved -> Published -> Deprecated -> Retired
```

### Current Flow (3.4 - Manual)
1. **Discover** in catalog (browsing available MCP servers)
2. **Deploy** through lifecycle operator (MCPServer CRD -> Deployment + Service)
3. **Register** in MCP Gateway (manual MCPServer CR creation)
4. **Configure** Studio access (manual ConfigMap update)
5. **Consume** through Gen AI Studio

**Problem**: Steps 3-4 are manual, ungoverned, and fragile.

### Target Flow (3.5 - Registry-Governed)
1. **Discover/Evaluate** MCP servers from sources
2. **Govern** in registry (metadata, lifecycle state, certification)
3. **Surface** through catalog when approved
4. **Deploy** via lifecycle operator with registry association
5. **Register** in gateway automatically (informed by registry state)
6. **Consume** through AAA/Studio via governed paths

## Asset Flow (Generic - 8 Stages)

1. **Ingress**: Asset enters the ecosystem (internal dev, ISV, community, external registry)
2. **Validation**: Security scanning, SBOM, provenance checks, quarantine
3. **Registration**: Metadata-first registration in the appropriate backend registry
4. **Governance**: Lifecycle state management, approval workflows, policy linkage
5. **Promotion**: Movement from dev registry to prod registry with lineage
6. **Deployment**: Runtime deployment via appropriate operator/mechanism
7. **Runtime**: Gateway-mediated access with enforcement, monitoring, audit
8. **Consumption**: Available through AAA/Studio to entitled users

## Catalog Flow vs Registry Flow

### Models (Current - Catalog-First)
```
Catalog (discovery) -> Registry (governance) -> Deployment -> Consumption
```

### MCP Servers (Proposed - Registry-First)
```
Registry (governance) -> Catalog (surfacing) -> Deployment -> Consumption
```

**Note**: Dan Kuc flagged this as potentially opposite to the model flow. This difference reflects that MCP servers require governance *before* being surfaced for discovery, while models may be discovered first and then registered for governance.

## Summit Demo Flow (May 2026)
```
Catalog -> Deploy (Lifecycle Operator) -> Gateway Register -> ConfigMap -> Gen AI Studio -> Tool Execution
```
