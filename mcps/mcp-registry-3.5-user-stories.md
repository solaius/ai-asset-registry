# MCP Registry — 3.5 Dev Preview Targeted User Stories

## Purpose

This document defines the targeted user stories for MCP Registry in RHOAI 3.5 Dev Preview. It translates the MCP Registry MVP requirements into concrete actors, actions, and outcomes so that the 3.5 deliverable scope is clear and the implied API surfaces and cross-component interactions are easy to reason about.

This is intentionally narrow. It does not revisit the strategic case for the registry, define architecture, or prescribe implementation. The existing MVP requirements document provides that broader context.

---

## Terminology

This document uses **publish state** instead of "approval" to describe how an MCP record becomes eligible for downstream surfacing. This is intentional:

- **Publish state** describes a concrete lifecycle position — an MCP record is either in draft, published, or deprecated. Downstream components use this state to decide what to surface.
- **Approval** implies workflow scaffolding (reviewers, gates, engines) that is out of scope for 3.5 Dev Preview and may not exist in the product today.

Similarly, terms like "policy linkage" and "governance context" from the broader strategy are replaced here with explicit state, fields, and read/write interactions that can be mapped to real capability.

---

## Scope

**In scope for these stories:**

- Governed registration of MCP servers (internal and external)
- Metadata-first records aligned with the upstream MCP registry schema
- Lifecycle publish state (draft / published / deprecated)
- Scoped visibility (workspace-based)
- Downstream read access for catalog, gateway, and AAA/Studio integrations
- Deployment/runtime reference association
- Version history and deprecation

**Out of scope for these stories:**

- Approval workflows, review gates, or policy enforcement engines
- Certification or verification status tracking
- Full automated lifecycle handoffs between components
- Detailed API, CRD, or data model design
- Full multi-asset registry framework
- Disconnected sync/mirroring
- UI design

---

## Actors

| Actor | Description |
|---|---|
| **Platform Engineer** | Sets up and maintains the AI platform. Registers, manages, and governs MCP assets. |
| **AI Engineer** | Develops AI applications. Consumes governed MCP assets through Studio/AAA. |
| **Catalog** (integration) | Discovery surface that reads registry state to present publishable MCPs. |
| **MCP Gateway** (integration) | Runtime mediation layer that reads registry metadata for published MCPs. |
| **AAA / Studio** (integration) | Consumption surface that resolves published, scope-appropriate MCPs for AI engineers. |
| **Lifecycle Operator** (integration) | Deployment primitive. May write deployment state back to registry records. |

---

## User Stories

### Story 1 — Register an internal MCP server

**As a** Platform Engineer
**I want to** create a registry record for an internally developed MCP server
**So that** the MCP exists as a governed asset with stable identity, version, and metadata

**Acceptance notes**

- A registry record can be created with identity, version, source, and descriptive metadata
- The record stores a metadata-first reference to the MCP, not the runtime itself
- The record includes the canonical MCP payload (aligned with the upstream MCP registry schema)
- The record starts in draft publish state

**Cross-component implication**

- Establishes the core registry write surface
- Other components do not interact with the record until it is published

---

### Story 2 — Register an external MCP server

**As a** Platform Engineer
**I want to** create a registry record for an externally hosted MCP server
**So that** externally sourced MCPs can be governed and surfaced through the same platform experience

**Acceptance notes**

- A registry record can represent an external MCP without requiring the registry to host the runtime
- The record can store endpoint or source references for the external MCP
- The record supports the same version metadata and publish state as internal MCPs
- Internal and external MCPs use one normalized record shape

**Cross-component implication**

- Downstream components consume registry records the same way regardless of hosting model

---

### Story 3 — Retrieve and list MCP records

**As a** Platform Engineer or integrating component
**I want to** retrieve a single MCP record or list MCP records
**So that** I can understand what MCPs exist, what state they are in, and what metadata they carry

**Acceptance notes**

- A single MCP record can be retrieved by identifier and version
- MCP records can be listed with basic filtering
- Returned data includes identity, version, descriptive metadata, publish state, and relevant source or runtime references
- Results are filtered by the caller's access scope

**Cross-component implication**

- This is the core read surface that catalog, gateway, and AAA/Studio integrations depend on

---

### Story 4 — Change publish state

**As a** Platform Engineer
**I want to** move an MCP record between publish states: draft, published, or deprecated
**So that** the platform can distinguish between MCPs that are only recorded and MCPs that are eligible for downstream use

**Acceptance notes**

- Each MCP version has a publish state
- A Platform Engineer can transition the publish state
- Only published MCPs are eligible for downstream surfacing (catalog, gateway, AAA/Studio)
- Deprecated MCPs remain queryable for reference but are no longer preferred for new use
- Publish state transitions are recorded for auditability

**Cross-component implication**

- Catalog, gateway, and AAA/Studio filter on publish state — they do not need to invent separate governance logic
- This replaces the need for approval workflows in the 3.5 Dev Preview scope

---

### Story 5 — Surface publishable MCPs to the catalog

**As a** Catalog integration
**I want to** read only MCP records that are published and eligible for surfacing
**So that** the catalog reflects governed registry state rather than maintaining its own governance truth

**Acceptance notes**

- The catalog can query MCP records from the registry
- Only MCPs with published state and appropriate visibility scope are returned
- The catalog presents these records for discovery and selection
- The catalog does not define its own separate publish or governance state for the same MCP

**Cross-component implication**

- Registry is the governance source of truth; catalog is the discovery surface
- This boundary prevents duplicate governance state from drifting between systems

---

### Story 6 — Associate deployment or runtime references

**As a** Platform Engineer
**I want to** associate deployment or runtime details with a governed MCP record
**So that** a deployed MCP instance is not disconnected from its registry entry

**Acceptance notes**

- A registry record can store deployment or runtime references (e.g., endpoint, deployment association)
- A deployed MCP can be understood as an instance of a governed registry record
- The registry does not become the deployment engine — the lifecycle operator remains the deployment primitive
- In 3.5 Dev Preview, these associations may be written manually or by an external process

**Cross-component implication**

- The lifecycle operator deploys MCP servers; the registry records the governed association
- This bridges the gap between "what is governed" and "what is running" without requiring full automation in 3.5

---

### Story 7 — Provide gateway-relevant MCP metadata

**As an** MCP Gateway integration
**I want to** retrieve registry metadata for published MCPs that is relevant to runtime mediation
**So that** gateway integration can begin moving away from disconnected manual stitching

**Acceptance notes**

- Gateway-relevant MCP metadata (identity, version, endpoint references, publish state) can be read from the registry
- The registry can distinguish which MCPs are eligible for downstream runtime use
- The registry does not perform gateway enforcement — the gateway remains the runtime control layer
- In 3.5 Dev Preview, manual or external steps may still exist between registry state and gateway configuration

**Cross-component implication**

- The registry becomes the governed source for metadata that gateway-related flows consume
- Full automation of registry-to-gateway handoff is a later-phase goal, not a 3.5 requirement

---

### Story 8 — Surface consumable MCPs to AAA / Studio

**As an** AAA or Studio integration
**I want to** retrieve MCPs that are both published and visible to the current scope
**So that** AI engineers see only MCPs that are intended for consumption

**Acceptance notes**

- AAA/Studio can query MCP records from the registry (directly or via catalog)
- Returned MCPs are filtered by publish state and visibility scope
- Returned metadata is sufficient for selection and consumption
- This provides a governed alternative to the current config-map-driven exposure model

**Cross-component implication**

- Supports the shift from static configuration to registry-governed consumption paths
- AI engineers consume what the registry says is published, not what was manually stitched into a ConfigMap

---

### Story 9 — Enforce scoped visibility

**As a** Platform Engineer or AI Engineer
**I want to** see only the MCP records appropriate to my role or scope
**So that** governed MCP visibility aligns with enterprise boundaries

**Acceptance notes**

- Registry reads are scope-aware (workspace-based visibility for MVP)
- Different users or integrations see different subsets of MCP records based on their scope
- Scoped visibility applies to both management operations (Platform Engineer) and consumption queries (AI Engineer, AAA/Studio)

**Cross-component implication**

- Catalog, gateway, and AAA/Studio receive scope-filtered views rather than one global list
- This is concrete, RBAC-aligned behavior — it does not require a policy engine

---

### Story 10 — Preserve version history and deprecate older MCPs

**As a** Platform Engineer
**I want to** retain prior MCP versions and mark older versions as deprecated
**So that** the platform preserves continuity while steering users toward current versions

**Acceptance notes**

- Multiple versions of the same MCP can exist in the registry
- Each version carries its own publish state and metadata
- An MCP version can be marked deprecated independently of other versions
- Older versions remain queryable — deprecation does not erase history
- Downstream consumers can see deprecation status when resolving MCPs

**Cross-component implication**

- Catalog and AAA/Studio can reflect version status and guide users toward current versions
- Version history supports auditability without requiring a full audit logging system in 3.5

---

## What This Means for 3.5 Dev Preview

These stories imply a minimal MCP Registry with the following capabilities:

| Capability | Stories |
|---|---|
| Create MCP record (internal or external) | 1, 2 |
| Retrieve MCP record by identifier and version | 3 |
| List MCP records with basic filtering | 3 |
| Change publish state (draft / published / deprecated) | 4 |
| Associate deployment or runtime references | 6 |
| Query publishable MCPs for catalog | 5 |
| Query consumable MCPs for AAA/Studio | 8 |
| Provide gateway-relevant MCP metadata | 7 |
| Enforce scoped visibility on all reads | 9 |
| Support multiple versions per MCP with deprecation | 10 |

**Store state vs. automate behavior**: For 3.5 Dev Preview, the registry needs to *store* enough state to support the interactions above. Full automation of every handoff (e.g., registry publish automatically triggering gateway registration or catalog sync) is a later-phase goal. In 3.5, the registry becomes the governed source of truth. Manual or external steps may still bridge some component interactions.

---

## Out of Scope

This story set does not define:

- Approval workflows, review gates, or approval engines
- Policy authoring or enforcement
- Certification or verification tracking
- Full automated gateway registration from registry state
- Detailed API, CRD, or data model design
- Full disconnected sync or mirroring
- Full generalized multi-asset registry design
- UI/UX design
