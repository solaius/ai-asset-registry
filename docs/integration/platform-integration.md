# Platform Integration

## OpenShift AI Integration

### Current State (3.4)
- Model Registry exists (Kubeflow-based)
- MCP Lifecycle Operator deploys MCP servers (separate install)
- MCP Gateway available (requires RHCL prerequisite)
- Gen AI Studio consumes via ConfigMap
- No unified governance layer connecting these

### Target State (3.5+)
- MCP Registry as governance backbone
- Catalog surfaces registry-approved assets
- Gateway informed by registry state (reduced manual config)
- Studio/AAA consumes via governed paths
- RHOAI deployment includes MCP Gateway (RHAIRFE-1457) and Lifecycle Operator (RHAIRFE-1456)

## Deployment Architecture

### Component Deployment
| Component | Namespace | Installation |
|---|---|---|
| MCP Gateway (Broker + Controller) | mcp-system | Helm chart or ArgoCD |
| Istio Gateway | gateway-system | OpenShift Service Mesh |
| Connectivity Link (Kuadrant) | kuadrant-system | Operator via OLM |
| MCP Servers | user namespaces | Lifecycle Operator |

### Prerequisite Chain
```
OpenShift Service Mesh -> Connectivity Link (Kuadrant) -> MCP Gateway -> MCP Servers
```

## SKU & Entitlement Matrix

| Customer Scenario | Entitlements | MCP Gateway Features |
|---|---|---|
| OCP only | Zero-cost RHCL SKU | Auth + Rate Limiting for platform MCP servers |
| OCP + RHOAI | RHOAI + zero-cost RHCL | Auth (Authorino) + Rate Limiting for customer MCP servers |
| OCP + RHCL (full) | Full RHCL | All features: DNS, HA/DR/GLB, full rate limiting |
| Layered product with MCP Server | Needs RHOAI or RHCL | Depends on entitlement path |

## OCP 5.0 Considerations
- OLM 1.0 removes dependency declarations
- Need alternative strategy for installing RHCL when MCP Gateway is deployed
- MCP Gateway may need to be installable as platform component
- See: [OpenShift 5 Guidance for OLM-managed Operators](https://docs.google.com/document/d/1humaZXIe2yvRA0zjObyMBrIHWDmQXqO_p0SAGD9uX3k)

## MLflow / Databricks Integration

### Upstream Process
- Design approval required before coding (~1 month timeline)
- PRs must be small and focused
- Need focused design documents extracted from comprehensive requirements

### Current Upstream Alignment
- MLflow Prompt Registry: exists and working
- MLflow GenAI: expanding capabilities (metadata-first, externally stored models/agents)
- Skills registry prototype: Databricks building, target end of April 2026
- Plugin architecture: Kubeflow PR #2219 for genericizing asset types

### What Needs Upstream Proposal
- MCP server registry support in MLflow
- Agent registry support in MLflow
- Expanded lifecycle states beyond None/Staging/Production/Archived
- Plugin framework for arbitrary asset types
