# MCP Lifecycle Operator

## Overview
Kubernetes operator for deploying and managing MCP server workloads. Creates Deployment + Service from MCPServer CRD definitions.

**Repository**: [kubernetes-sigs/mcp-lifecycle-operator](https://github.com/kubernetes-sigs/mcp-lifecycle-operator)

## MCPServer CRD
The operator watches MCPServer custom resources and creates the corresponding:
- Deployment (with specified container image, ports, env vars, secrets)
- Service (exposing the MCP server endpoint)

## Configuration
- Container images for MCP servers
- Port configuration
- Secret references for credentials
- Environment variables
- Resource limits

## Summit Demo (May 11-14, 2026)
Workflow: Catalog -> Deploy -> Gateway Register -> ConfigMap -> Gen AI Studio -> Tool Execution

## Supported MCP Servers
| Server | Status |
|---|---|
| OpenShift | Available |
| Ansible AAP | Available |
| Red Hat Satellite | WIP |
| Red Hat Insights | Available |
| Linux/RHEL | WIP |

## Registry Integration
The lifecycle operator is the **deployment primitive** - it deploys MCP services. The registry represents the governed MCP and its deployment/runtime associations. The operator does NOT own governance or lifecycle state.

## RHOAI Integration
- RHAIRFE-1456: Request to include MCP Lifecycle Operator in RHOAI 3.5
- Currently requires manual installation separate from RHOAI

## Source Documents
- docs/starting-artifacts/mcp-artifacts/MCP Lifecycle Operator - summit requirements and expected deliverables (2).pdf
