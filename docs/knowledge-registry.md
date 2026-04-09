# AI Asset Registry - Knowledge Registry

This file tracks key information, decisions, concepts, and references gathered from all source materials for the AI Asset Registry proposal. It serves as a living document to support continued proposal development, prototyping, and documentation.

Last updated: 2026-04-08

---

## 1. Core Strategic Direction

### Vision
A **federated AI Asset Registry framework** for Red Hat OpenShift AI that provides:
- Multiple backend registries where needed (MLflow for registries, Kubeflow for catalogs)
- A unified interface for interacting with registered AI assets
- A plugin model enabling rapid addition of new AI asset types
- Consistent governance, lifecycle, and policy support across assets
- Support for development and production registry use cases

### Key Decision: Registry vs Catalog Separation
- **Registry = Governance** (system of record): identity, versioning, lifecycle state, policy linkage, lineage, approvals, auditability
- **Catalog = Discovery** (consumption surface): browsing, searching, filtering, curation, helping users find assets
- "MLflow is for registries. Kubeflow is for catalogs. We're done." - Adam Bellusci (2026-04-07 sync)
- Registries are **metadata-first** - actual assets stored/running elsewhere; registry governs metadata and context

### Strategic Importance
- Foundational for: evaluation, observability, gateway integration, lifecycle automation, policy enforcement, supply chain management, secure promotion
- Avoids one-off registry development for each new asset type
- Enables inner-loop to outer-loop workflows (local dev -> cluster dev -> production)

---

## 2. AI Asset Types

### Primary Assets (Current Priority)
| Asset Type | Backend | Status | Notes |
|---|---|---|---|
| **MCP Servers** | MLflow Registry | Highest priority for 3.5 | Runtime integrations, tool-serving; lifecycle operator exists |
| **Agents** | MLflow Registry | High priority for 3.5 | Composed capabilities referencing models, prompts, MCP servers, skills, guardrails |
| **Models** | Kubeflow Model Registry (current) -> MLflow (future) | Existing capability | Model registry already exists; future alignment with MLflow direction |
| **Prompts** | MLflow Prompt Registry | Active upstream work | MLflow already has prompt registry support |
| **Skills** | MLflow (prototype) | End of April 2026 target | Packaging format unsettled; Databricks building prototype |
| **Guardrails** | TBD | RHOAI 3.4 ships MCP guardrail DP | Safety/behavioral boundaries for AI interactions |
| **Knowledge Sources** | TBD | Future | Governed info sources for RAG, agents, retrieval workflows |

### Secondary/Future Assets
Notebooks, Pipelines, Evaluators, Skill Packs, Code Snippets, Workflows

---

## 3. Key Components & Their Roles

### MCP Gateway (Kuadrant/mcp-gateway)
- **Role**: Runtime connectivity and enforcement layer
- **Tech**: Envoy-based proxy on OpenShift using ext_proc, Gateway API, Istio
- **Features**: Virtual MCP servers, identity-aware filtering, per-tool auth/metrics/audit, streaming HTTP
- **CRDs**: MCPServer, MCPGatewayExtension
- **Ships with**: OCP for platform use; RHOAI required for customer MCP servers
- **SKU**: RHOAI gets auth via Authorino + rate limiting from RHCL; full RHCL purchase for additional features

### MCP Lifecycle Operator (kubernetes-sigs/mcp-lifecycle-operator)
- **Role**: Deployment primitive for MCP servers on Kubernetes
- **CRD**: MCPServer -> creates Deployment + Service
- **Summit demo**: May 11-14, 2026 (Catalog -> Deploy -> Gateway Register -> ConfigMap -> Gen AI Studio -> Tool Execution)
- **Supported servers**: OpenShift, Ansible AAP, Red Hat Satellite (WIP), Red Hat Insights, Linux/RHEL (WIP)

### MCP Registry (NEW - 3.5 Dev Preview target)
- **Role**: System of record / governance backbone for MCP servers
- **Purpose**: Fills the gap between catalog, deployment, gateway, and Studio
- **What it governs**: Identity, version, metadata, lifecycle state, certification/approval, trust, auditability
- **Conceptual integrations**: Catalog (surfacing), Lifecycle Operator (deployment), Gateway (runtime), AAA/Studio (consumption)
- **NOT**: a replacement for catalog, lifecycle operator, gateway, or Studio

### Model Registry / Kubeflow Hub (kubeflow/model-registry -> kubeflow/hub)
- **Role**: Existing model registry in Kubeflow/OpenShift AI, evolving into broader AI asset hub
- **Renaming**: KEP-0003 renames project to "Kubeflow Hub" with images moving to `ghcr.io/kubeflow/hub/*`
- **Catalog service already supports MCP servers** alongside models via `assetType` query parameter
- **Future asset types**: datasets, prompts, notebooks planned
- **Architecture**: GORM + MySQL/PostgreSQL, REST API v1alpha3, generic `customProperties` for extensibility
- **Plugin architecture**: PR #2219 by Alessio for genericizing asset types beyond models

### AI Hub (UI Surface)
- **Role**: Unified UI in RHOAI
- **Structure**: Tabs for Catalog, Registry, Deployments per asset type
- **AAA (AI Available Assets)**: RBAC-scoped consumption surface for AI Engineers

---

## 4. Lifecycle & Flows

### MCP Server Lifecycle (12 stages)
Sources -> Quarantine -> Registry -> Catalog -> Deployments -> Consumable

### Lifecycle States
Draft -> Candidate -> Verified -> Approved -> Published -> Deprecated -> Retired

MLflow native states: None, Staging, Production, Archived (may need expansion - Dan Kuc comment)

### Current Flow (3.4 - fragmented/manual)
1. Discover in catalog
2. Deploy through lifecycle operator
3. Manually register in MCP Gateway
4. Manually update ConfigMap
5. Consume through Gen AI Studio

### Target Flow (3.5 - registry-governed)
1. MCPs discovered and evaluated
2. Governed in the registry (system of record)
3. Surfaced through catalog when approved
4. Associated with deployment/runtime state
5. Made consumable through registry-governed platform paths

### Asset Flow (8 stages)
Ingress -> Validation -> Registration -> Governance -> Promotion -> Deployment -> Runtime -> Consumption

---

## 5. Personas

| Persona | Focus | Registry Interaction |
|---|---|---|
| **AI Engineers** | Develop AI apps, consume assets | Find approved assets, reference during dev, promote through workflows |
| **Platform Engineers** | Set up & maintain AI platform | Enable registry capabilities, integrate with OpenShift AI, manage tenancy |
| **AgentOps Admins** | Governance & operational oversight | Review/approve assets, manage lifecycle, associate policies |
| **Business Consumers** | Indirect users | Benefit from governed, trusted AI capabilities |

---

## 6. Platform Integration & Deployment

### Release Timeline
| Release | Date | Relevant Content |
|---|---|---|
| RHOAI 3.0 GA | Nov 2025 | Baseline |
| RHOAI 3.4 | Apr 2026 (code freeze) | MCP Guardrail DP; Summit demo |
| RHOAI 3.5 | Target | MCP Registry Dev Preview; Agent registries; MCP Gateway deployment in RHOAI |
| OCP 5.0 | Future | MCP Servers + MCP Gateway included |

### SKU & Entitlement Model
- **RHOAI SKU**: Auth (via RHCL/Authorino) + MaaS entitled to Auth + Rate Limiting
- **RHCL SKU**: Full entitlements including MCP Gateway
- **All OpenShift versions** (OVE, OKE, OCP, OPP): Auth + Rate Limiting + MCP for internal use
- Catalog/Appendix 1 update: August 2026; verbiage due to Gerry by June/July

### Deployment Dependencies
- RHCL doesn't depend on MCP Gateway, but MCP Gateway depends on RHCL
- RHOAI 3.4: RHCL listed as prerequisite (not auto-deployed)
- RHOAI 3.5: Request to include MCP Gateway deployment (RHAIRFE-1457)
- OCP 5.0: OLM 1.0 removes dependency declarations - need alternative strategy

### Key Jira Tickets
- RHAIRFE-1370: AI Asset Registries main epic
- RHAIRFE-1457: MCP Gateway deployment in RHOAI 3.5
- RHAIRFE-1456: MCP Lifecycle Operator in RHOAI 3.5

---

## 7. Upstream & Databricks Collaboration

### Process
- Design approval required before coding (~1 month)
- PRs broken into small, focused pieces
- Need to extract focused design documents from comprehensive requirements doc

### Current Upstream Work
- MLflow: Prompt Registry (exists), expanding GenAI capabilities
- Skills registry prototype: targeting end of April 2026
- Plugin architecture: Kubeflow PR #2219 for genericizing asset types
- Model registry alignment: future move toward MLflow-based direction

### Open Questions for Upstream
- What exact MCP metadata should be mandatory for registry records?
- Which lifecycle states required for 3.5 Dev Preview vs later?
- How should registry state inform gateway behavior?
- What belongs in MLflow upstream proposal now vs later?

---

## 8. Security & Governance

### Security Pipeline
- SBOM generation, CVE scanning (Clair/Syft/Grype)
- Provenance checks, signed attestations
- Stacklok ToolHive integration for secure MCP server execution
- x-certificationStatus, x-securityScan, x-maturityLevel vendor extensions

### Governance Model
- RBAC-aware visibility and management
- Policy linkage (association of assets with policies/controls)
- Approval workflows separate from runtime enforcement
- Auditability and controlled change tracking

### Zero-Trust for Agents (from Agentic Strategy)
- Agents need their own verifiable identities (SPIFFE/SPIRE)
- Fine-grained, dynamic access control
- On-behalf-of token exchange patterns
- Integration with enterprise IdPs (Keycloak)

---

## 9. Competitive Landscape

### MCP Catalogs/Registries
| Competitor | Type | Key Differentiator |
|---|---|---|
| Smithery | Open marketplace | "Largest" open marketplace; CLI/SDK |
| Docker MCP Catalog | Curated + containerized | Verified Docker images; Docker Desktop integration |
| GitHub MCP Registry | Open registry | GitHub identity; announced Sep 2025 |
| Official MCP Registry | Protocol-official | "App store for MCP servers" |
| ToolHive (Stacklok) | Open + secure runtime | Security-focused; sandboxed containers |
| Microsoft Copilot Studio | Closed vendor | In-product certified connectors |
| Databricks Unity Catalog | Enterprise | Unified governance for data + AI |

### Red Hat Differentiator
Enterprise governance, lifecycle management, policy enforcement, and platform integration - not just discovery/listing.

---

## 10. Agentic AI Strategy Context

### Four Pillars
1. **Build & BYOA**: Framework-agnostic; Llama Stack as foundation; Responses API
2. **Govern & Secure (AgentOps)**: Observability, evaluations, zero-trust, guardrails
3. **Deploy & Manage**: Agent Registry, lifecycle management, hybrid cloud deployment
4. **AI Hub**: Unified surface for catalog, registry, deployments

### Key Customer Pain Points
- Governance gap: can't audit agent decisions (SAS, BCG, UBS blocked)
- Security impasse: agent identity & access control unsolved (Amadeus, BofA)
- Fragmentation: multiple frameworks, no central control (Infineon, T-Mobile, Florida Blue)
- ROI: need cost tracking and chargeback (Turkcell, Expedia)

### ResponsesAgent (MLflow)
- Potential alignment with Llama Stack + Responses API
- Connects with MLflow experiments and registry for versioning/management
- Could provide framework for tracing agents built from any framework

---

## 11. Key People & Stakeholders

| Person | Role/Area |
|---|---|
| Peter Double | Principal PM - MCP, AI Asset Registries |
| Adam Bellusci | Leadership - Registry/Catalog direction |
| Adel Zaalouk | Agentic Strategy maintainer |
| Andrew Block | MCP Gateway on OpenShift |
| Andrew Mackenzie | MCP Gateway delivery/packaging |
| Craig Brookes | MCP Gateway engineering |
| Dan Kuc | MLflow/Registry engineering |
| David Martin | MCP Gateway verification/testing |
| Edson Tirelli | Agents/Skills |
| Jon Burdo | Product Management |
| Chris Hambridge | Product Management |
| Ju Lim | Product Management - RHOAI |
| Mrunal Patel | MCP Server deployment/OCP integration |
| Alessio | Kubeflow plugin architecture |
| Hunter Gerlach | Consulting use cases |
| Shane Utt | Guardrails |

---

## 12. Source Document Index

### Local Documents
| Document | Location | Key Content |
|---|---|---|
| AI Asset Registries - Comprehensive (PDF) | docs/starting-artifacts/ | **Most important** - full product requirements |
| RHOAI - MCP Asset Management (173pp PDF) | docs/starting-artifacts/mcp-artifacts/ | Complete MCP ecosystem: roadmap, data models, APIs, security, gateway, lifecycle |
| MCP Catalog (PDF) | docs/starting-artifacts/mcp-artifacts/ | Registry integration requirements |
| MCP Lifecycle Operator (PDF) | docs/starting-artifacts/mcp-artifacts/ | Summit demo requirements, MCPServer CRD |
| Agentic AI Strategy 2026 (PDF) | docs/starting-artifacts/agentic-artifacts/ | Four pillars, customer insights, competitive analysis |
| Registry Proposal Discussion (transcript) | docs/starting-artifacts/meeting-transcriptions/ | 2026-03-19 meeting; Databricks process, plugin architecture |
| AI Asset Registries Sync (transcript) | docs/starting-artifacts/meeting-transcriptions/ | 2026-04-07 sync; skills registry, registry vs catalog debate |

### Google Documents
| Document | Doc ID | Key Content |
|---|---|---|
| AI Asset Registries | 1AtsP7xwlC15znJEfJ9jHiEow21kwUtIQAg3d06uC6wQ | Product requirements, core concepts, capabilities, asset coverage |
| Agentic Strategy 2026 | 1d_XK88Gxwr8zuPJZQ8_fbvah-y7PX12CYf4BTbvNVPE | Enterprise challenges, four pillars, customer insights, competitive |
| MCP Gateway on OpenShift | 1liziAy55qQBd80qRN63WTZjhu5mJRQ4-OlogP_edftQ | Installation guide, production deployment, Gateway API, TLS |
| MCP Gateway Delivery | 1asuSND63alcJGIExLH-VvBzevtKleuuWge39lO5Su7Q | SKU model, entitlements, deployment scenarios |
| MCP Catalog | 1L3yVBHKJLwVJ2SzF5NunzXc8gFJNZKbMU6OWfPyB_fE | Catalog PM write-up, requirements, competitor comparison |
| MCP Registry MVP | 11mJpJ-Py8FxRDYdw41mMWEBvlahENS4rHqnpDNpqa8Y | 3.5 Dev Preview requirements, scope, integration expectations |

### GitHub Repositories
| Repository | Purpose |
|---|---|
| kubernetes-sigs/mcp-lifecycle-operator | Kubernetes operator for deploying MCP servers via MCPServer CRD |
| Kuadrant/mcp-gateway | Envoy-based MCP Gateway with broker, router, K8s controller |
| kubeflow/model-registry | Model registry with plugin extensibility (PR #2219) |
| opendatahub-io/architecture-context | Architecture documentation for OpenDataHub/RHOAI |
| opendatahub-io/ai-helpers | AI helper tools/utilities for OpenDataHub |

---

## 13. Open Questions & Risks

### Open Questions
1. What exact MCP metadata should be mandatory for registry records?
2. Which lifecycle states are required for 3.5 Dev Preview?
3. How should registry-governed state inform gateway behavior?
4. How should registry state influence catalog surfacing?
5. What belongs in MLflow upstream proposal now vs later?
6. Skills packaging format - OCI artifacts? Markdown? Zip bundles?
7. Agent identity model - SPIFFE/SPIRE or alternative?
8. How to handle OLM 1.0 dependency removal in OCP 5.0?

### Risks
1. **Databricks upstream process**: ~1 month for design approval; PRs must be small/focused
2. **Skills packaging**: No standard format yet; multiple competing approaches
3. **OCP 5.0 OLM changes**: Dependencies going away; need alternative installation strategy
4. **Model registry transition**: Moving from Kubeflow to MLflow-based direction has migration implications
5. **Scope creep**: Comprehensive doc is 173+ pages; need focused, extractable design documents

### Key Comment Threads (from Google Docs)
- Dan Kuc: MLflow lifecycle states (None/Staging/Production/Archived) may need expansion
- Dan Kuc: MCP flow seems opposite to model registry flow (registry-first vs catalog-first)
- Andrew Mackenzie: OCP 5.0 deployment plan urgently needs nailing down
- Mrunal Patel: MCP servers registered post-Gateway installation; ideally Gateway installation is part of RHOAI
