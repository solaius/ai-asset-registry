# MCP Registry — 3.5 Dev Preview Overview

## Summary

The MCP Registry is the system of record for MCP servers in Red Hat OpenShift AI. It provides governed identity, versioning, lifecycle state, and metadata for MCP assets so that other platform components — catalog, lifecycle operator, gateway, and AAA/Studio — can consume a single source of truth instead of maintaining disconnected state.

This document defines the scope and role of the MCP Registry for the 3.5 Dev Preview. The targeted user stories in the companion tab define the specific behaviors and cross-component interactions.

## Problem

In the current 3.4 MCP ecosystem, the path from discovery to consumption involves five disconnected steps:

1. Discover an MCP in the catalog
2. Deploy it through the lifecycle operator
3. Manually register it in the MCP Gateway
4. Manually update a ConfigMap so it appears in Gen AI Studio
5. Consume through Studio

Steps 3 and 4 are manual, ungoverned, and disconnected from each other. There is no single place that tracks what MCP servers exist, what state they are in, which are intended for consumption, or how a deployed instance relates to the asset it was deployed from.

The MCP Registry eliminates this gap by becoming the governed record that the other components read from.

## What the Registry Does

The MCP Registry stores metadata-first records for MCP servers. Each record carries:

- **Identity and version** — stable name, version string, descriptive metadata
- **MCP payload** — the canonical server definition aligned with the upstream MCP registry schema
- **Publish state** — draft, published, or deprecated (determines downstream surfacing eligibility)
- **Source and hosting references** — whether the MCP is internally or externally hosted
- **Deployment/runtime references** — optional association with a deployed instance (endpoint, deployment details)
- **Workspace scope** — visibility boundary for RBAC-aware access

The registry does not host MCP runtimes, enforce gateway policies, deploy services, or replace the catalog. It stores the governed state that those components consume.

## Component Boundaries

| Component | Responsibility | Reads from Registry | Writes to Registry |
|---|---|---|---|
| **MCP Registry** | Governed identity, versioning, lifecycle state, metadata | — | — |
| **Catalog** (Kubeflow Hub) | Discovery and browsing | Published MCPs for surfacing | No |
| **Lifecycle Operator** | Deployment (MCPServer CRD) | No (in 3.5 DP) | May write deployment references |
| **MCP Gateway** | Runtime mediation and enforcement | Published MCP metadata | No |
| **AAA / Studio** | Consumption for AI engineers | Published, scope-filtered MCPs | No |

Each component retains its existing responsibility. The registry adds the governed state layer they can all read from instead of each maintaining separate truth.

## Current vs. Target Flow

**Today (3.4):**
Catalog → Lifecycle Operator → **manual** Gateway registration → **manual** ConfigMap update → Studio

**Target (3.5 DP):**
Registry stores governed MCP records → Catalog reads published MCPs for discovery → Lifecycle Operator deploys and associates runtime references → Gateway reads registry metadata → AAA/Studio resolves published, scoped MCPs for consumption

In 3.5 Dev Preview, not every handoff is fully automated. The registry stores enough state for components to read from it. Some steps (e.g., gateway configuration, deployment association) may still involve manual or external processes, but they operate on registry state rather than disconnected artifacts.

## 3.5 Dev Preview Scope

**In scope:**

- Register MCP servers (internal and external) as governed, versioned records
- Store metadata-first records with the canonical MCP payload
- Manage publish state (draft / published / deprecated) to control downstream surfacing
- Provide read access for catalog, gateway, and AAA/Studio integrations filtered by publish state and workspace scope
- Associate deployment or runtime references with governed records
- Preserve version history and support deprecation of older versions
- RBAC-aware scoped visibility on all operations

**Out of scope:**

- Approval workflows, review gates, or policy enforcement engines
- Certification or verification status tracking
- Full automated lifecycle handoffs between components
- Detailed API, CRD, or data model design (covered in the upstream MLflow proposal)
- Full multi-asset registry framework
- Disconnected sync/mirroring
- UI/UX design

See the **3.5 DP Targeted User Stories** tab for the specific behaviors, acceptance criteria, and cross-component interactions that define the 3.5 deliverable.

## Open Questions

- What exact MCP metadata should be mandatory for the registry record?
- Which runtime and deployment references must be represented?
- How should registry publish state influence catalog surfacing in the first phase?
- How should registry metadata inform gateway configuration?
- What belongs in the MLflow upstream proposal now versus later?
