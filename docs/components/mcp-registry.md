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

## Proposed Data Model (Brain Dump — Not Finalized)
> Source: [MLflow MCP Registry Data Model Proposal](https://docs.google.com/document/d/1KOLTSMVjdUhb06rQLSx4G5MVSQLXePToGAQ4wNn31m8) — this is a proposal/brain dump, not an approved design.

### Entities
| Entity | Role | Key Properties |
|---|---|---|
| **MCPServer** | Logical governed asset, scoped to a workspace | name, description, status, tags, aliases, audit fields |
| **MCPServerVersion** | Immutable MCP payload + mutable governance metadata | version, server_json (immutable), lifecycle/approval/verification/certification states, tags, source, run_id, is_deployed |

### Design Principles
- `MCPServer` is the logical governed asset; `MCPServerVersion` stores an immutable MCP payload plus mutable governance metadata
- `server_json` lives on `MCPServerVersion` as the canonical MCP payload (follows [upstream MCP registry schema](https://registry.modelcontextprotocol.io/docs#/schemas/ServerJSON))
- Governance metadata is first-class and separate from tags
- Deployment visibility is intentionally minimal for MVP — captured only as `is_deployed`
- Workspace is the visibility boundary for MVP

### Governance Status Tracks (Proposed)
Unlike a single linear lifecycle, the proposal separates governance into four independent tracks:

| Track | States | Purpose |
|---|---|---|
| **Lifecycle State** | Draft, Candidate, Published, Deprecated, Retired | Progression state of the version |
| **Approval Status** | Draft, Pending, Approved, Rejected, Revoked | Authorization/governance decision |
| **Verification Status** | Unverified, Verified | Identity/source verification |
| **Certification Status** | None, Candidate, Certified, Expired, Revoked | Certification program compliance |

### Governance Invariants
- `lifecycle_state=PUBLISHED` implies `approval_status=APPROVED`
- `approval_status=REJECTED` or `REVOKED` should not coexist with `lifecycle_state=PUBLISHED`
- `workspace` on version resolves to same value as parent `MCPServer.workspace`

### Open Follow-Ups (from Proposal)
1. Whether certification programs should be backed by an admin-managed allowlist
2. Whether lifecycle history should be added in MVP or deferred
3. Whether approval, verification, and certification transitions need distinct permissions
4. Whether MCP registry entities need `EntityAssociationType` support in MVP
5. How MCP Lifecycle Operator should interact with registry — read-only "discovered" entries? (Chris Hambridge, Matt Prahl)

### Relevance to Other Asset Types
This data model is MCP-specific, but the pattern (logical asset + versioned record, separated governance tracks, workspace scoping) could inform future data models for agents, skills, and other AI assets.

## Key Comments from Reviewers
- **Dan Kuc**: MLflow lifecycle states (None/Staging/Production/Archived) may need expansion for MCP
- **Dan Kuc**: MCP flow appears opposite to model registry flow (registry-first vs catalog-first for models)
- **Chris Hambridge** (Data Model): Should Lifecycle Operator create read-only "managed" registry entries?
- **Matt Prahl** (Data Model): Clarified concept as "discovered" MCP servers from MLflow's perspective
- **Jon Burdo** (Data Model): Certification allowlist question may connect to RHAIRFE-294

## Source Documents
- [MCP Registry MVP Requirements (Google Doc)](https://docs.google.com/document/d/11mJpJ-Py8FxRDYdw41mMWEBvlahENS4rHqnpDNpqa8Y)
- [MLflow MCP Registry Data Model Proposal (Google Doc)](https://docs.google.com/document/d/1KOLTSMVjdUhb06rQLSx4G5MVSQLXePToGAQ4wNn31m8) — **PROPOSAL**, not finalized
