# AI Asset Registry - System Boundaries

## Component Responsibilities

### MCP Registry (NEW)
**Role**: Governance backbone / system of record
- Manages identity, metadata, lifecycle state, certification, trust, auditability
- Connects catalog, deployment, gateway, and Studio into one governed lifecycle
- Does NOT replace any other component

### Catalog (Kubeflow)
**Role**: Curated, policy-aware discovery surface
- Surfaces approved/discoverable assets for browsing and selection
- Reflects registry-vetted assets
- Does NOT become the governance source of truth

### MCP Gateway (Kuadrant)
**Role**: Runtime connectivity and enforcement
- Routing, policy enforcement, authorization, guardrails, observability for MCP traffic
- Informed by registry-governed state
- Does NOT itself perform governance

### MCP Lifecycle Operator (kubernetes-sigs)
**Role**: Deployment primitive
- Deploys MCP server workloads via MCPServer CRD -> Deployment + Service
- Registry represents the governed MCP and its deployment associations
- Does NOT own governance or lifecycle state

### AAA & Gen AI Studio
**Role**: Consumption surfaces
- Where AI engineers see and use MCP assets they're entitled to consume
- Should reflect registry- and catalog-driven truth
- Does NOT bypass registry governance

## Integration Flow (Target State)

```
Sources (ISV, internal, community)
    |
    v
[Quarantine / Validation]
    |
    v
[MCP REGISTRY] <-- system of record
    |
    +---> Catalog (surfacing approved MCPs for discovery)
    |
    +---> Lifecycle Operator (deployment)
    |
    +---> MCP Gateway (runtime enforcement, informed by registry state)
    |
    +---> AAA / Studio (consumption, governed paths)
```

## Backend Registry Mapping

| Asset Type | Registry Backend | Catalog Backend |
|---|---|---|
| MCP Servers | MLflow | Kubeflow |
| Agents | MLflow | Kubeflow |
| Models | Kubeflow Hub (fka Model Registry) -> MLflow (future) | Kubeflow Hub |
| Prompts | MLflow Prompt Registry | Kubeflow |
| Skills | MLflow (prototype) | Kubeflow |
| Guardrails | TBD | Kubeflow |
