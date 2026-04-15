# MCP Gateway

## Overview
Envoy-based gateway for Model Context Protocol servers, built by the Kuadrant team. Provides runtime connectivity, enforcement, and observability for MCP traffic on OpenShift/Kubernetes.

**Repository**: [Kuadrant/mcp-gateway](https://github.com/Kuadrant/mcp-gateway)

## Architecture
- **Broker**: Central MCP endpoint that aggregates tools from registered MCP servers
- **Router (ext_proc)**: Envoy external processing filter for request routing
- **Controller**: Kubernetes controller watching MCPServer CRDs and HTTPRoutes
- **Gateway API**: Uses Kubernetes Gateway API (not Ingress) for routing

## Key CRDs
- **MCPServer**: Registers an MCP server with the gateway (toolPrefix, targetRef to HTTPRoute)
- **MCPGatewayExtension**: Configures gateway-level behavior and references

## Features
- Virtual MCP servers (aggregated tool view)
- Identity-aware tool filtering (allow/deny per tool)
- Per-tool authentication and authorization (via Authorino/RHCL)
- Per-tool metrics and audit logging
- Streaming HTTP MCP proxy
- OAuth 2.0 protected resource support

## Deployment on OpenShift
- **Prerequisites**: Gateway API support (GA in OCP 4.19+), OpenShift Service Mesh 3, Connectivity Link (Kuadrant)
- **Installation**: Helm chart (`ghcr.io/kagenti/charts/mcp-gateway`) or ArgoCD
- **Namespace**: `mcp-system` for gateway, `gateway-system` for Istio Gateways
- **Verification**: POST to `/mcp` endpoint with `initialize` JSON-RPC method

## Production Considerations
- DNS management via OpenShift Cluster Ingress Operator or ExternalDNS
- TLS certificate management at Gateway level or via Kuadrant TLSPolicy
- Cross-namespace ReferenceGrants for MCPGatewayExtension -> Gateway references
- Future: BackendTLSPolicy for TLS reencryption

## SKU & Entitlement
- Ships with OCP for platform-internal MCP servers
- RHOAI subscription: Auth (Authorino) + Rate Limiting (AI workloads only)
- Full RHCL purchase: All features including DNS, HA/DR/GLB
- Catalog/Appendix 1 update: August 2026
- **Note**: RHOAI entitlement for unlimited tokens and token metrics is scoped to MaaS only — MCP and Agent workloads were explicitly excluded from this scope in the signed agreement

## MCP Gateway in RHCL
- **MCP Gateway 0.6.0 TP**: Ships in RHCL 1.3.3 (April 30, 2026)
- **RHCL 1.4 GA**: June 11, 2026 (adds OCP 4.22 support)
- **Known gap**: RHOAI 3.5 (Aug 2026) supports OCP 4.19 but RHCL likely won't — untested/best-effort only

## RHOAI-RHCL Support Agreement

> Source: [Internal Support and Product Agreement](https://docs.google.com/document/d/11JTt4SHZJ1UFG9RWZsVL2t6MZqBFZ4Iuq4niXxtE59g) — signed agreement (Apr 2026).

- RHOAI deploys specific RHCL component images (Case 1 of agreement)
- RHOAI gets early build images for integration testing
- Support issues routed directly to RHCL Engineering via [CONNLINK](https://issues.redhat.com/projects/CONNLINK) JIRA project
- Pre-defined index image with OCP version tagging required for testing
- Both teams acknowledge OCP version support may differ due to technical requirements (e.g., Gateway API availability)

## Registry Integration
The gateway should be informed by registry-governed state rather than requiring separate manual registration. Target: registry approval -> gateway configuration flows automatically.

## Source Documents
- [MCP Gateway on OpenShift (Google Doc)](https://docs.google.com/document/d/1liziAy55qQBd80qRN63WTZjhu5mJRQ4-OlogP_edftQ)
- [MCP Gateway Delivery (Google Doc)](https://docs.google.com/document/d/1asuSND63alcJGIExLH-VvBzevtKleuuWge39lO5Su7Q)
- [Internal Support and Product Agreement (Google Doc)](https://docs.google.com/document/d/11JTt4SHZJ1UFG9RWZsVL2t6MZqBFZ4Iuq4niXxtE59g)
