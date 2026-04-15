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

## Partner MCP Catalog (3.4 DP - Delivered)

The first instantiation of the MCP Catalog shipped in RHOAI 3.4 DP (code freeze April 10, 2026) with partner and community MCP servers for a Summit splash.

> Source: [Partners MCP servers in RHOAI Catalog](https://docs.google.com/document/d/1Z2rA0fiAC2Zt_AWnond_Ogi3-2cqEzElN-U76M1x740) — decided outcomes.

### What Shipped
- **5 Partners**: Confluent, Dynatrace, Terraform (HashiCorp), Microsoft Azure, EnterpriseDB (EDB)
- **2 Community**: MariaDB, MongoDB
- **3 Red Hat**: OpenShift, Ansible AAP, + 1 other
- **Quay org**: `quay.io/rhoai-partner-mcp`
- **Upstream**: `opendatahub-io/model-metadata-collection` (PR merged Red Hat, Partner, Community sources)

### Technical Requirements for Partner MCPs
- Streamable HTTP transport only (no stdio)
- On-cluster deployment, local hosting only (no remote for DP)
- UBI-based container image in Quay
- Core metadata: name, description, provider, logo, license (SPDX), licenseLink, readme
- MCP-specific: tools[], transportType, deploymentMode, artifacts[], endpoints[]
- Security indicators: verifiedSource, sast, secureEndpoint, readOnlyTools

### Partner Pipeline
1. Curation (invitation-only, strategic categories)
2. BDM outreach with consent letter
3. Partner signs consent form
4. Technical attestation (Ecosystem Engineering)
5. Image build and push to Quay
6. Catalog integration and testing (RH AI)

### Post-Summit Plans
- Azure plugins beyond MCP (Agentic Packs)
- Oracle and CyberArk revisit (consent given but failed technical pipeline)
- Quickstarts with Dynatrace and Azure/ARO prioritized
- Deprecation of FY25 web-based catalogs
- Partner pipeline scaling with lighter-weight support model
- Community server positioning and criteria

## Source Documents
- [MCP Catalog (Google Doc)](https://docs.google.com/document/d/1L3yVBHKJLwVJ2SzF5NunzXc8gFJNZKbMU6OWfPyB_fE)
- [Partners MCP servers in RHOAI Catalog (Google Doc)](https://docs.google.com/document/d/1Z2rA0fiAC2Zt_AWnond_Ogi3-2cqEzElN-U76M1x740)
- docs/starting-artifacts/mcp-artifacts/MCP Catalog.pdf
- docs/starting-artifacts/meeting-transcriptions/Sharon_Peter 1_1 - 3.4 confirmation 13-04-2026.txt
