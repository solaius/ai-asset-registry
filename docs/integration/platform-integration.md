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
| OCP + RHOAI | RHOAI + zero-cost RHCL | Auth (Authorino) + Rate Limiting (AI workloads only) for customer MCP servers |
| OCP + RHCL (full) | Full RHCL | All features: DNS, HA/DR/GLB, full rate limiting |
| Layered product with MCP Server | Needs RHOAI or RHCL | Depends on entitlement path |

### Permitted RHOAI Use of RHCL (Signed Agreement)

> Source: [Internal Support and Product Agreement](https://docs.google.com/document/d/11JTt4SHZJ1UFG9RWZsVL2t6MZqBFZ4Iuq4niXxtE59g) — signed agreement (Apr 2026).

- Unlimited tokens for AI use cases (MaaS only — MCP and Agents excluded)
- Token Metrics for AI use cases (MaaS only — MCP and Agents excluded)
- RHCL Rate limiting limited to AI workloads
- Authorino (separate agreement)
- Any additional features/use cases require RHCL subscription or updated agreement

## RHOAI-RHCL Version Compatibility

### RHCL Release Schedule
| RHCL Version | Date | OCP Support | Notes |
|---|---|---|---|
| 1.3.2 | Apr 8, 2026 | 4.19, 4.20, 4.21 | |
| 1.3.3 | Apr 30, 2026 | 4.19, 4.20, 4.21 | MCP Gateway 0.6.0 TP |
| 1.4 | Jun 11, 2026 | 4.19, 4.20, 4.21, 4.22 | |

### Known Version Gap
RHOAI 3.5 (Aug 2026) will support OCP 4.19, but RHCL likely won't. Configuration (RHOAI 3.5 + RHCL + OCP 4.19) is **untested with best-effort support**. OCP 4.19 maintenance ends Dec 2026. Customers advised to upgrade to OCP 4.20+.

### Cross-Team Support Workflow
- RHOAI gets RHCL early build images for integration testing
- RHOAI support opens JIRAs directly with RHCL Engineering ([CONNLINK](https://issues.redhat.com/projects/CONNLINK)) — bypasses RHCL support
- Both engineering teams collaborate on customer issues
- Pre-defined index image (tagged with OCP version/platform) for dependency operator testing

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
