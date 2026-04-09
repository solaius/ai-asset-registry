# MCP Catalog

## Overview
The governance and management layer of the MCP ecosystem within RHOAI. Manages the full lifecycle, relationships, and governance of MCP entities (tools, prompts, resources, workloads, agents).

## Role in Ecosystem
- Builds on the **Registry** (authoritative source)
- Complements the **Gateway** (runtime enforcement)
- Operates in a **federated and interoperable ecosystem** (customers may bring their own catalogs/registries)

## Core Requirements

### Managed Entities
- MCP Servers (sourced from Registry)
- Tools and Resources
- Prompts
- Workloads
- Workload-to-Tool/Resource mappings

### Governance & Trust
- Restrict workloads to approved tools only
- Disable/revoke tool usage globally or per workload
- Enterprise governance metadata (ownership, compliance tags, risk classification)
- Publisher verification and moderation

### Filtering & Simplification
- **Tool Masking**: Expose only necessary tools per workload
- **Input Simplification**: Break multi-purpose tools into clearer single-purpose variants
- **Output Filtering**: Reduce responses to relevant fields for LLMs

### Lifecycle Management
- **States**: Draft -> Review -> Approved -> Deployed
- Entity-level versioning with coexistence and deprecation
- Ingress -> Review/Approval -> Deployment flow

### Federation & Interoperability
- Sync with external registries (Docker MCP Catalog, GitHub MCP Registry, partner catalogs)
- Internal/private catalogs with federation support
- Exportable configs and APIs for customer-managed gateways

## Integration Points
- **Registry**: Provides authoritative server metadata; Catalog enriches with entities, mappings, governance
- **Gateway**: Consumes catalog-approved entities for runtime enforcement
- **External registries**: Docker, GitHub, Smithery, Microsoft, Databricks Unity Catalog
- **AI Hub (future)**: Broader catalog unifying MCP with models, datasets, prompts, agents

## Registry Integration Requirements
- API consumption with filtering by tool type, version, auth methods, trust level
- Full metadata schema ingestion and validation
- Trust tier display (certified, community, unverified)
- Version presentation with deprecation marking
- RBAC and approval workflow flow-through
- Health/status integration
- Multi-registry federation with conflict resolution
- Security signal enforcement (revocations, vulnerability flags, signature validation)

## Source Documents
- [MCP Catalog (Google Doc)](https://docs.google.com/document/d/1L3yVBHKJLwVJ2SzF5NunzXc8gFJNZKbMU6OWfPyB_fE)
- docs/starting-artifacts/mcp-artifacts/MCP Catalog.pdf
