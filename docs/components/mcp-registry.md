# MCP Registry

## Overview
The authoritative governance layer for MCP servers in RHOAI. System of record that brings governance, lifecycle control, trust, and visibility so MCP assets can move cleanly from discovery through approval, deployment, runtime use, and consumption.

**Target**: RHOAI 3.5 Dev Preview

## Problem It Solves
In 3.4, the MCP ecosystem is fragmented and manual:
1. Discover in catalog
2. Deploy through lifecycle operator
3. **Manually** register in MCP Gateway
4. **Manually** update ConfigMap
5. Consume through Studio

The registry fills the governance gap connecting these steps.

## Purpose
System of record for MCP servers (internal or external). Governs:
- Ingestion and lifecycle
- Security and certification context
- Versioning
- Policy-aware visibility

## Core Boundaries
| Component | Responsibility |
|---|---|
| **Registry** | Governance, lifecycle, metadata, trust |
| **Catalog** | Discovery, browsing, selection |
| **Gateway** | Runtime connectivity and enforcement |
| **Lifecycle Operator** | Deployment |
| **AAA / Studio** | Consumption |

## MVP Requirements (3.5 Dev Preview)
- Governed registration of MCP servers
- Metadata-first MCP records
- Lifecycle state tracking (Draft -> Candidate -> Verified -> Approved -> Published -> Deprecated -> Retired)
- Certification and approval visibility
- Support for internal and external MCP representation
- RBAC-aware visibility and governance
- Conceptual integration with catalog, deployment, gateway, AAA/Studio

## Out of Scope (MVP)
- Full generalized multi-asset registry design
- Detailed implementation architecture
- Deep automation of every lifecycle handoff
- Full disconnected syncing/mirroring
- Complete partner onboarding model
- Full MLflow-centered generalized solution
- Detailed API, CRD, or UI design

## Open Questions
1. What exact MCP metadata should be mandatory?
2. Which runtime/deployment references must be represented?
3. Which lifecycle states required for 3.5 vs later?
4. How should registry state inform gateway behavior?
5. How should registry state influence catalog surfacing?
6. What belongs in MLflow upstream proposal now vs later?

## Key Comments from Reviewers
- **Dan Kuc**: MLflow lifecycle states (None/Staging/Production/Archived) may need expansion for MCP
- **Dan Kuc**: MCP flow appears opposite to model registry flow (registry-first vs catalog-first for models)

## Source Documents
- [MCP Registry MVP Requirements (Google Doc)](https://docs.google.com/document/d/11mJpJ-Py8FxRDYdw41mMWEBvlahENS4rHqnpDNpqa8Y)
