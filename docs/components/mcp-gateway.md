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
- RHOAI subscription: Auth (Authorino) + Rate Limiting
- Full RHCL purchase: All features including DNS, HA/DR/GLB
- Catalog/Appendix 1 update: August 2026

## Registry Integration
The gateway should be informed by registry-governed state rather than requiring separate manual registration. Target: registry approval -> gateway configuration flows automatically.

## Source Documents
- [MCP Gateway on OpenShift (Google Doc)](https://docs.google.com/document/d/1liziAy55qQBd80qRN63WTZjhu5mJRQ4-OlogP_edftQ)
- [MCP Gateway Delivery (Google Doc)](https://docs.google.com/document/d/1asuSND63alcJGIExLH-VvBzevtKleuuWge39lO5Su7Q)
