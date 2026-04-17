# AI Asset Registry - Knowledge Registry

This file tracks key information, decisions, concepts, and references gathered from all source materials for the AI Asset Registry proposal. It serves as a living document to support continued proposal development, prototyping, and documentation.

Last updated: 2026-04-16

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
| **Skills** | MLflow Registry | Active — Red Hat driving upstream proposal | RHAIRFE-1712 (public), RHAIRFE-1713 (in-cluster). Full strategy, user stories, data model, and upstream proposal drafted. See `skills/skills-registry/`. **Databricks MVP prototype now visible** at `B-Step62/mlflow` branch `skill-registry-mvp` — dedicated entity type with full CRUD, CLI, UI, and Claude Code integration. See Section 7. |
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
- **SKU**: RHOAI gets auth via Authorino + rate limiting (limited to AI workloads) from RHCL; full RHCL purchase for additional features
- **MCP Gateway TP**: MCP Gateway 0.6.0 ships as TP in RHCL 1.3.3 (April 30, 2026)
- **RHOAI-RHCL Agreement**: Signed internal support agreement (Apr 2026) governs how RHOAI deploys and supports RHCL components (see Section 6)

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

#### Proposed Data Model (from MLflow MCP Registry Data Model Proposal - brain dump, not finalized)
- **MCPServer**: Logical governed asset scoped to a workspace. Carries name, description, status (Active/Deprecated/Retired), tags, aliases, and audit fields
- **MCPServerVersion**: Immutable MCP payload (`server_json`) plus mutable governance metadata. Each version tracks lifecycle_state, approval_status, verification_status, certification_status independently
- **Design principles**: `server_json` is the canonical immutable MCP payload on the version; governance metadata is first-class and separate from tags; workspace is the visibility boundary for MVP; deployment visibility is minimal (`is_deployed` boolean only)
- **Governance invariants (proposed)**: `lifecycle_state=PUBLISHED` implies `approval_status=APPROVED`; `REJECTED`/`REVOKED` approval cannot coexist with `PUBLISHED` lifecycle state
- **Key distinction from current doc**: Separates governance into four independent status tracks rather than a single linear lifecycle (see Lifecycle States below)

### Gen MCP
- **Role**: MCP server generation, containerization, and runtime wrapping tool
- **Capabilities**: Generate MCP servers from API definitions (OpenAPI), CLI interfaces, or HTTP backends; containerize using KO build tool; add security/auth/TLS/observability wrappers for production readiness
- **Team**: Calum Murray, Matthias Weßendorf, Nader Ziada (engineering)
- **Key detail**: Evaluations have been extracted into a separate project (**MCP Checker** / `mcpchecker` — now in its own org) so they can be used against any MCP server, not just Gen MCP-generated ones
- **Ingestion pipeline role**: Can wrap stdio/unsecured HTTP MCPs with auth, TLS, observability for OpenShift readiness; useful for partners who don't provide production-ready containers
- **Limitation**: Opinionated containerization based on its own generated servers; for fully production-ready partner MCPs, standard podman/docker containerization is more appropriate

### MCP Checker (mcpchecker)
- **Role**: Generic MCP server evaluation framework (extracted from Gen MCP)
- **Capabilities**: Run evals against any MCP server with configurable agents; produces eval scores per tool; supports multiple agent backends (not just one)
- **Usage**: Ingestion pipeline evaluation step; customer-side re-evaluation with their own agents; pre-release quality gate (e.g., OpenShift MCP Server evals before release)
- **Key insight**: Customers may want to re-evaluate catalog MCPs with their own agent (e.g., tested with Claude Code/Gemini but customer uses Amazon Q)

### Model Registry / Kubeflow Hub (kubeflow/model-registry -> kubeflow/hub)
- **Role**: Existing model registry in Kubeflow/OpenShift AI, evolving into broader AI asset hub
- **Renaming**: KEP-0003 renames project to "Kubeflow Hub" with images moving to `ghcr.io/kubeflow/hub/*`
- **Catalog service already supports MCP servers** alongside models via `assetType` query parameter
- **Future asset types**: datasets, prompts, notebooks planned
- **Architecture**: GORM + MySQL/PostgreSQL, REST API v1alpha3, generic `customProperties` for extensibility
- **Plugin architecture**: PR #2219 by Alessio for genericizing asset types beyond models

### Partner MCP Catalog (3.4 DP - Completed)
- **Role**: Curated collection of partner and community MCP servers in RHOAI catalog for Summit splash
- **Outcome**: 5 partners + 2 community servers integrated into RHOAI 3.4 DP by April 10 code freeze
- **Partners in 3.4 DP**: Confluent, Dynatrace, Terraform (HashiCorp), Microsoft Azure, EnterpriseDB (EDB)
- **Community servers**: MariaDB, MongoDB (positioned in "periphery" — demos, blogs, GenAI Studio configmap)
- **Did not make it**: Oracle (consent given, OAuth flow unresolved), CyberArk (consent given, technical requirements not met), JFrog (server moved to remote-only)
- **Quay org**: `quay.io/rhoai-partner-mcp` (created because `quay.io/opendatahub` process was too slow)
- **Upstream repo**: `opendatahub-io/model-metadata-collection` — PR merged three sources: Red Hat, Partner, Community
- **Consent process**: Katie Giglio managed consent letters and forms; 7 partners said YES; BDMs from Greg Labows' and Robert Mad's orgs handled outreach
- **Technical requirements for partners**:
  - **Transport**: Streamable HTTP only (no stdio)
  - **Deployment**: On-cluster, local hosting only for DP (no remote MCP servers)
  - **Container**: UBI-based image in Quay
  - **Maintenance**: Partners must commit to keeping assets up-to-date
  - **Support**: RHOAI will NOT support or fix customer tickets for partner MCP servers
- **Partner value proposition**: Live on cluster (vs stale web listing), Summit splash visibility, quickstart inclusion, AgentOps roadmap alignment, maturity tiering system planned
- **Metadata schema (defined for catalog)**:
  - Core: name, description, provider, logo, license (SPDX), licenseLink, readme
  - MCP-specific: tools[] (name, description, parameters, access type, revoked/revokedReason), transportType, deploymentMode, artifacts[] (OCI URIs), endpoints[]
  - Security indicators: verifiedSource, sast, secureEndpoint, readOnlyTools
- **Post-Summit plans**: Azure plugins beyond MCP (Agentic Packs), Oracle/CyberArk revisit, quickstarts (Dynatrace and Azure/ARO prioritized), deprecation of FY25 web catalogs, partner pipeline scaling

> Source: [Partners MCP servers in RHOAI Catalog](https://docs.google.com/document/d/1Z2rA0fiAC2Zt_AWnond_Ogi3-2cqEzElN-U76M1x740) — working document with decided outcomes for 3.4 DP.

### AI Hub (UI Surface)
- **Role**: Unified UI in RHOAI
- **Structure**: Tabs for Catalog, Registry, Deployments per asset type
- **AAA (AI Available Assets)**: RBAC-scoped consumption surface for AI Engineers
- **Design System**: PatternFly (https://www.patternfly.org/) — Red Hat's open source design system; all RHOAI UI and prototypes use PatternFly components, design tokens, and layout patterns

---

## 4. Lifecycle & Flows

### MCP Server Lifecycle (12 stages)
Sources -> Quarantine -> Registry -> Catalog -> Deployments -> Consumable

### Lifecycle States

**Current documented flow (7 states, linear)**:
Draft -> Candidate -> Verified -> Approved -> Published -> Deprecated -> Retired

**Proposed model (from MLflow Data Model Proposal - not finalized)**: Separates governance into four independent status tracks:
- **Lifecycle State** (5 states): Draft -> Candidate -> Published -> Deprecated -> Retired
- **Approval Status** (5 states): Draft -> Pending -> Approved -> Rejected -> Revoked
- **Verification Status** (2 states): Unverified -> Verified
- **Certification Status** (5 states): None -> Candidate -> Certified -> Expired -> Revoked

This separation allows independent progression — e.g., a version can be verified but not yet approved, or approved but not yet certified. The invariant is that `PUBLISHED` lifecycle requires `APPROVED` approval status.

**MLflow native states**: None, Staging, Production, Archived (may need expansion - Dan Kuc comment)

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

### MCP Ingestion Pipeline (from 2026-04-10 Gen MCP meeting)

Two primary ingestion lanes identified for getting MCPs into the platform:

**Lane 1: Partner MCP Ingestion (Partnership Ecosystem Team)**
- Partners provide MCP server repos (expected ready-to-go, meeting a checklist)
- Pipeline: Validate repo -> Scan for CVEs/vulnerabilities -> Evaluate MCP -> Containerize (podman/docker for production-ready MCPs) -> Registry
- For partners with basic/stdio/unsecured MCPs: Gen MCP runtime can wrap with auth, TLS, observability
- Goal: Eventually manage partner MCPs **off-cycle** from product releases; continuous updates, new partner onboarding
- Current state (3.4): Jose Gonzalez manually adapted partner containers — rebasing to UBI, adding labels, handling licenses; CVE scanning via Quay; partner dependencies mostly untouched to avoid breaking their apps
- Key tools: Quay (CVE scanning), Konflux (pipeline orchestration, suggested), Dependabot (repo-level), Snyk (AI-specific agent scanning — Ann Murray pushing; needs further research)

**Lane 2: Client/Customer MCP Generation**
- Platform engineers create MCPs from existing APIs/repos
- Flow: API definition (OpenAPI) or HTTP backend -> Gen MCP generation -> Containerization -> Validation -> Registry/Catalog
- Sales/field teams requesting this capability as part of the product
- Lower-code approach for platform engineers to quickly build MCPs

**Shared Pipeline Components (reusable across lanes)**
- **Validate**: Repo integrity, provenance, supply chain checks
- **Scan**: CVE scanning (Quay), AI-specific scanning (Snyk — under investigation), dependency checking
- **Evaluate**: MCP Checker for functional evaluation with configurable agents
- **Containerize**: Standardized metadata schema (no current standard — all 4 partner MCPs in 3.4 had different metadata formats)
- **Metadata standardization**: Critical gap — no upstream Kubernetes MCP container metadata standard exists; Red Hat opportunity to lead this upstream

**Key Decisions from Meeting**
- Gen MCP containerization is **not** the right fit for already-containerized partner MCPs (use podman/docker instead)
- Gen MCP runtime wrapping **is** useful for partners with basic stdio/HTTP MCPs lacking auth/TLS/observability
- Evaluations are generic (MCP Checker) and reusable across all lanes, including post-catalog customer re-evaluation
- MLflow will be the main evaluation tool for AI assets; MCP evaluation integration with MLflow TBD

### Partner Business Pipeline (from Partners MCP Catalog Doc)

The partner onboarding pipeline has both business and technical dimensions:

**Business Pipeline (completed for 3.4 DP)**:
1. Curate partner list (invitation-only, strategic categories: CI/CD, databases, hyperscalers, security)
2. BDM outreach with consent letter and value proposition
3. Partner signs consent form (Google Form managed by Katie Giglio)
4. Technical attestation by Ecosystem Engineering (Matt Dorn's team)
5. Image build and push to `quay.io/rhoai-partner-mcp`
6. Catalog integration and testing by RH AI team (Chris Hambridge)

**Partner Disqualification Criteria**:
- Technical: stdio-only transport, stale/unmaintained assets, remote-only MCP servers (for DP)
- Business: No consent, no maintenance commitment, no "skin in the game"

**Post-DP Scaling Plans**:
- Define lighter-weight maintenance/support policy (different from operators — AI assets have different nature/longevity)
- Partner pipeline for adding and maintaining MCP servers at scale
- Quickstarts with featured partners (Dynatrace, Azure/ARO prioritized)
- Deprecation of FY25 web-based catalogs (redhat.com/en/products/ai/openshift-ai/mcp-servers)
- Change "Others" tab in UI to "Community"
- MCP as the model for how to handle plugins, agents, and other AI asset partner onboarding

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
| RHOAI 3.4 DP | Apr 10, 2026 (code freeze) | MCP Catalog DP with 5 partner + 2 community MCPs; MCP Guardrail DP; Summit demo; Kubeflow GA |
| RHOAI 3.5 (TP target) | Target | MCP Registry Dev Preview; Agent registries; MCP Gateway deployment in RHOAI; Hardening, registry integration, gateway configuration |
| RHOAI GA | End of CY26 target | Full GA of MCP ecosystem |
| OCP 5.0 | Future | MCP Servers + MCP Gateway included |

### Summit 2026
- **Dates**: May 11-14, 2026, Atlanta, Georgia
- **Partner splash**: Blog post about partner MCPs in catalog (Peter drafting, Sharon/Katie coordinating partner review)
- **Blog submission deadline**: April 24, 2026
- **Demo**: Lifecycle operator with catalog, gateway registration, GenAI Studio tool execution
- **Logos**: Not in DP build; will be fixed post-DP
- **Other Summit content**: Agentic overview blog (Younes), Agent Ops talk (Peter/Roberto/Younes), Power Hour with AI segment

### SKU & Entitlement Model
- **RHOAI SKU**: Auth (via RHCL/Authorino) + Rate Limiting (limited to AI workloads)
- **Permitted RHOAI use of RHCL** (from signed agreement):
  - Unlimited tokens for AI use cases (MaaS only — ~~MCP, Agents~~ struck from scope)
  - Token Metrics for AI use cases (MaaS only — ~~MCP, Agents~~ struck from scope)
  - RHCL Rate limiting limited to AI workloads
  - Authorino (covered by separate agreement)
  - Any additional features/use cases require RHCL subscription or updated agreement
- **RHCL SKU**: Full entitlements including MCP Gateway, DNS, HA/DR/GLB
- **All OpenShift versions** (OVE, OKE, OCP, OPP): Auth + Rate Limiting + MCP for internal use
- Catalog/Appendix 1 update: August 2026; verbiage due to Gerry by June/July

### RHOAI-RHCL Internal Support Agreement (Signed Apr 2026)

> Source: [Internal Support and Product Agreement](https://docs.google.com/document/d/11JTt4SHZJ1UFG9RWZsVL2t6MZqBFZ4Iuq4niXxtE59g) — signed agreement.

**Agreement scope**: Case 1 only — RHOAI deploying specific versions of RHCL components via images pushed to registry by RHCL team. Case 2 (customers installing full RHCL alongside RHOAI) covered by separate future agreement.

**RHCL Version Matrix** (key releases):
| RHCL Version | Date | OCP Support | Notes |
|---|---|---|---|
| 1.3.2 | Apr 8, 2026 | 4.19, 4.20, 4.21 | |
| 1.3.3 | Apr 30, 2026 | 4.19, 4.20, 4.21 | **MCP Gateway 0.6.0 TP** |
| 1.4 | Jun 11, 2026 | 4.19, 4.20, 4.21, 4.22 | |

**Known Version Gap (Aug 2026)**: RHOAI 3.5 will support OCP 4.19, but RHCL likely will not. Configuration (RHOAI 3.5 + RHCL + OCP 4.19) documented as **untested with best-effort support only**. OCP 4.19 maintenance support ends Dec 2026 (4 months overlap). Customers advised to upgrade to OCP 4.20+.

**Support workflow**:
- RHOAI gets RHCL early build images for integration testing
- RHOAI support opens JIRA tickets directly with RHCL Engineering ([CONNLINK project](https://issues.redhat.com/projects/CONNLINK)) — bypasses RHCL support for faster resolution
- Both engineering teams collaborate on customer issues
- Pre-defined index image (tagged with supported OCP version/platform) required for dependency operator testing

**Signoff status**: Signed by Christopher Ferreira (PM RHCL), Jonathan Zarecki (PM RHAI), Jeff DeMoss (PM RHAI), Andrew Mackenzie (Eng Dir RHCL), Roy Nissim (Eng Mgr RHAI), John Graham (Eng Dir RHAI). Pending: Craig Brookes (PTO), Thomas Maas, Rutuja Argade, Amana Juricic (support team reviewing).

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
- **Skills Registry MVP prototype visible**: `B-Step62/mlflow` branch `skill-registry-mvp` (B-Step62 = Yuki Watanabe, Databricks). Full implementation with dedicated entity type, REST API, CLI, UI, and Claude Code integration. See below.
- Plugin architecture: Kubeflow PR #2219 for genericizing asset types
- Model registry alignment: future move toward MLflow-based direction

### Skills Registry MVP — Databricks Prototype (discovered 2026-04-16)

**Repository**: [B-Step62/mlflow](https://github.com/B-Step62/mlflow/tree/skill-registry-mvp) — branch `skill-registry-mvp`
**Author**: Yuki Watanabe (B-Step62, Databricks) — same person leading PR #21725 (Skills Evaluation Phase 1)
**Status**: MVP prototype on a personal fork branch — not merged upstream, not announced publicly

**Key design decisions (confirmed)**:
- **Dedicated entity type** — NOT a specialized ModelVersion. Uses its own `registered_skills`, `skill_versions`, `skill_version_tags`, `skill_aliases` tables. This is the cleaner approach Red Hat's upstream proposal advocated for.
- **SKILL.md as the canonical format** — Skills are defined by SKILL.md files with YAML frontmatter (name, description). `manifest_content` (full SKILL.md text) stored on each version.
- **Packaging-agnostic artifacts** — Skill bundles stored in MLflow's artifact repository at `mlflow-artifacts:/skills/{name}/{version}/`. Supports S3, GCS, Azure, local filesystem.
- **Auto-incrementing integer versions** — Same pattern as Model Registry and Prompt Registry.
- **Aliases** — Named pointers to versions (e.g., `copilot@champion → v3`), consistent with MLflow 3 alias model.

**Data Model**:
- `Skill`: name, description, creation_timestamp, last_updated_timestamp, latest_version, aliases
- `SkillVersion`: name, version, source (GitHub URL or local path), description, manifest_content (full SKILL.md), artifact_location, creation_timestamp, tags, aliases, created_by
- `SkillAlias`: name, alias, version
- Tags: user-defined key-value pairs + system tags (`mlflow.skill.commit_hash`, `mlflow.skill.artifact_location`)

**REST API**: Under `/ajax-api/3.0/mlflow/skills/` (NOT `2.0/mlflow/` — uses the newer API version). Full CRUD: preview, register, list, get, delete skills/versions, plus tag and alias management.

**SDK API** (under `mlflow.genai`):
- `register_skill(source, tags, skill_names)` — accepts GitHub URL or local path; auto-discovers SKILL.md files
- `load_skill(name, version, alias)` — retrieve by version or alias
- `preview_skills(source)` — preview before registering
- `search_skills(filter_string, max_results)` — list with filtering
- `install_skill(name, version, alias, scope, project_path)` — install to Claude Code (global or project scope)
- `set_skill_alias()`, `delete_skill_alias()`, `set_skill_tag()`, `delete_skill_tag()`

**CLI** (under `mlflow skills`):
- `mlflow skills register <source>` — register from GitHub URL or local path
- `mlflow skills list` — list all registered skills
- `mlflow skills load <ref>` — install to Claude Code (supports `name`, `name/version`, `name@alias`, bulk)
- `mlflow skills show <ref>` — show details

**Claude Code Integration**:
- `install_skill()` copies artifacts to `~/.claude/skills/{name}/` (global) or `.claude/skills/{name}/` (project)
- Writes `.mlflow_skill_info` JSON sidecar alongside SKILL.md with version, source, commit_hash, tracking_uri, installed_at
- Tracing integration enriches tool spans with skill version info from sidecar — creates transparent link between skill usage and registry without runtime server dependency
- Span names: `tool_Skill:{skillName}` for usage analytics

**UI**:
- Skills list page with search, bulk selection, register modal (GitHub URL input), usage breakdown chart (stacked bar, 24h/7d/30d)
- Skills detail page with version table, SKILL.md preview (rendered markdown), file browser, tag/alias management, usage tab (per-skill line chart)

**What the MVP does NOT include** (alignment with Red Hat's governance layer):
- No lifecycle states beyond implicit (no Draft/Published/Deprecated)
- No approval workflows, certification, or verification tracks
- No trust tiers or security scanning
- No formal dependency/relationship model
- No workspace-scoped RBAC beyond basic workspace boundary
- No packaging format validation

**Implications for Red Hat**:
1. **Our preferred architecture was chosen**: Dedicated entity type, not tag-based ModelVersion specialization. This validates our upstream proposal's recommendation.
2. **Our upstream proposal still adds value**: The MVP has no publish_state, no governance tracks, no formal relationships — exactly the features our proposal adds.
3. **Risk reduced**: The prototype exists and is well-architected. The question shifts from "will they build it?" to "how do we influence the design before merge?"
4. **Coordinate with B-Step62**: Yuki Watanabe built both the Skills Evaluation PR (#21725) and this registry MVP. He is the key Databricks engineer to align with.
5. **Gap analysis needed**: Compare our proposed data model and API against MVP implementation — identify what to align with vs. what to propose as extensions.

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

### IBM/Red Hat Positioning for Agentic AI (from 2026-04-14 Agentic AI pod)
- **Joint customer conversations only** — this positioning is for when IBM and Red Hat are together in front of a customer; it does NOT change Red Hat's product roadmap
- **Red Hat focus**: Agent runtime, lifecycle management, Agent Ops (observability, evaluations, security)
- **IBM focus**: Agent building, control plane, Watsonx Orchestrate (no-code/fancy GUI, local/business users)
- **Key message**: Red Hat AI Enterprise will be first-class in Watsonx portfolio; IBM properties (Orchestrate, Govern) will depend on Red Hat AI Enterprise, not on wx.ai/CP4D
- **Red Hat stays the course**: MCP Gateway, Agent Ops, evaluations/traces with MLflow — none of this changes
- **Risk**: Field sellers may misinterpret as "Red Hat doesn't do agents" — need FAQ and repeated messaging
- **Mitigation**: FAQ attached to Power Hour, agentic messaging review by Joe, reinforcement in podcast and webinars
- **Tushar Katarki's framing**: "Let the best solution win" — overlaps are inevitable in AI; customer choice is the answer

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
| Matt Prahl | MLflow/Registry engineering (data model) |
| Shane Utt | Guardrails |
| Calum Murray | Gen MCP / MCP Checker engineering |
| Matthias Weßendorf | Gen MCP engineering |
| Nader Ziada | Gen MCP engineering (ex-Knative Serverless) |
| Jose Gonzalez | Partnership Ecosystem team (under Matt Dorn); partner MCP containerization |
| Matt Dorn | Partnership Ecosystem team lead |
| Ann Murray | Pushing Snyk for AI/agent scanning |
| Serob | Partnership Engineering team; working on ingestion pipeline pieces |
| Sharon Dashet | Partnership Ecosystem team, ecosystem engineering; key driver of partner MCP onboarding for 3.4 DP |
| Katie Giglio | Partner consent letters and outreach coordination |
| Tanya Heuze | Partner Ecosystem lead |
| Hugo Rivero | Partner Ecosystem |
| Paul Christensen | Partner Ecosystem; partner value proposition |
| Alec Leschin | Business Development; partner curation and Oracle pipeline |
| Ilan Pinto | Engineering; AI-driven SAST scanner |
| Itamar Heim | Leadership (Partnership Ecosystem management chain) |
| Mike Evans | Leadership (Partnership Ecosystem) |
| Younes Ben Brahim | Product Marketing Manager; agentic messaging, Summit content |
| Ornkanya Sinonpat (Aom) | Product Marketing; blog timeline/editorial coordination for Summit |
| Tushar Katarki | IBM/Red Hat alignment strategy for agentic AI |
| Noel O'Connor | Sales/field; raised concern about seller confusion on IBM/RH agentic positioning |
| Jennifer Vargas | GTM/marketing; seller enablement concerns, web page reviews |
| Aaron Isom | Business Development; Azure contact facilitation |
| Daniele Martinoli | Ecosystem Engineering; original MCP validation research (Q3/Q4 FY25) |
| Karl Eklund | Quickstart sponsorship and prioritization |
| Swati Kale | Quickstart sponsorship |
| Jessie Beach | Media team; ecosystem blog writing |
| John Gibson | Suggested Vast Data as partner |
| Roberto | Summit Agent Ops talk (with Younes and Peter) |
| Roy Nissim | Co-founder of Jones, reporting to Catherine Weeks; observability, AI gateway exploration |
| Jonathan Zareki | PM, RHAI; working on gateways with Roy Nissim; signed RHOAI-RHCL agreement |
| Christopher Ferreira | PM, RHCL; signed RHOAI-RHCL agreement |
| Jeff DeMoss | PM, RHAI; signed RHOAI-RHCL agreement |
| Thomas Maas | Engineering Manager, RHCL |
| John Graham | Engineering Director, RHAI; signed RHOAI-RHCL agreement |
| Rutuja Argade | Support Manager, RHAI |
| Amana Juricic | Support Director, RHAI |
| Tomas Jochec | RHCL coordination; driving signoffs on support agreement |

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
| MCP Pipeline w/ Gen MCP (transcript) | docs/starting-artifacts/meeting-transcriptions/ | 2026-04-10 meeting; ingestion pipeline, Gen MCP role, partner containerization, scanning/evaluation |
| Agentic AI pod v2 (transcript) | docs/starting-artifacts/meeting-transcriptions/ | 2026-04-14 meeting; IBM/Red Hat agentic positioning, MCP catalog blog post planning, Summit content coordination |
| Sharon/Peter 1:1 - 3.4 confirmation (transcript) | docs/starting-artifacts/meeting-transcriptions/ | 2026-04-13 meeting; 3.4 sign-off confirmed, partner splash blog, quickstarts, deprecation process, AI Hub concept, MaaS/gateway resourcing |

### Google Documents
| Document | Doc ID | Key Content |
|---|---|---|
| AI Asset Registries | 1AtsP7xwlC15znJEfJ9jHiEow21kwUtIQAg3d06uC6wQ | Product requirements, core concepts, capabilities, asset coverage |
| Agentic Strategy 2026 | 1d_XK88Gxwr8zuPJZQ8_fbvah-y7PX12CYf4BTbvNVPE | Enterprise challenges, four pillars, customer insights, competitive |
| MCP Gateway on OpenShift | 1liziAy55qQBd80qRN63WTZjhu5mJRQ4-OlogP_edftQ | Installation guide, production deployment, Gateway API, TLS |
| MCP Gateway Delivery | 1asuSND63alcJGIExLH-VvBzevtKleuuWge39lO5Su7Q | SKU model, entitlements, deployment scenarios |
| MCP Catalog | 1L3yVBHKJLwVJ2SzF5NunzXc8gFJNZKbMU6OWfPyB_fE | Catalog PM write-up, requirements, competitor comparison |
| MCP Registry MVP | 11mJpJ-Py8FxRDYdw41mMWEBvlahENS4rHqnpDNpqa8Y | 3.5 Dev Preview requirements, scope, integration expectations |
| MLflow MCP Registry Data Model Proposal | 1KOLTSMVjdUhb06rQLSx4G5MVSQLXePToGAQ4wNn31m8 | **PROPOSAL**: MCPServer/MCPServerVersion data model, governance enums (lifecycle, approval, verification, certification), invariants |
| Partners MCP servers in RHOAI Catalog | 1Z2rA0fiAC2Zt_AWnond_Ogi3-2cqEzElN-U76M1x740 | **DECIDED**: Partner selection, consent, technical pipeline, Summit outcome (5 partners + 2 community in 3.4 DP), post-Summit plans |
| Internal Support and Product Agreement (RHOAI-RHCL) | 11JTt4SHZJ1UFG9RWZsVL2t6MZqBFZ4Iuq4niXxtE59g | **DECIDED**: Signed support agreement — RHCL version matrix, support workflow, permitted RHOAI use cases (MaaS tokens/metrics, rate limiting, Authorino), known OCP 4.19 gap |

### External References
| Resource | URL | Key Content |
|---|---|---|
| PatternFly Design System | https://www.patternfly.org/ | **REFERENCE**: Red Hat's open source design system (v6.4.0). 100+ enterprise UI components, design tokens, accessibility guidance, theming. All RHOAI UI prototypes and product interfaces must use PatternFly. Includes React component library, layout systems (flex, grid, gallery, stack), charts/topology, and UX writing standards. |

### GitHub Repositories
| Repository | Purpose |
|---|---|
| kubernetes-sigs/mcp-lifecycle-operator | Kubernetes operator for deploying MCP servers via MCPServer CRD |
| Kuadrant/mcp-gateway | Envoy-based MCP Gateway with broker, router, K8s controller |
| kubeflow/model-registry | Model registry with plugin extensibility (PR #2219) |
| opendatahub-io/architecture-context | Architecture documentation for OpenDataHub/RHOAI |
| opendatahub-io/ai-helpers | AI helper tools/utilities for OpenDataHub |
| opendatahub-io/model-metadata-collection | Default catalog metadata (Red Hat, Partner, Community MCP servers); upstream for RHOAI catalog content |
| opdev/partner-mcp-dockerfiles | Partner MCP server container build files |
| chambridge/ocp-mcp-servers-research | Engineering evaluation of partner MCP servers (23 evaluated) |
| RHEcosystemAppEng/mcp-validation | MCP server validation tool (Q3/Q4 FY25 work) |
| B-Step62/mlflow (branch: skill-registry-mvp) | **Databricks Skills Registry MVP prototype**. Full implementation: dedicated entity type (Skill/SkillVersion), 4 DB tables, REST API (`/ajax-api/3.0/mlflow/skills/`), CLI (`mlflow skills`), SDK (`mlflow.genai`), Claude Code integration (.mlflow_skill_info sidecar), UI (list/detail with usage analytics). Author: Yuki Watanabe (B-Step62). Not merged upstream. |

### Blog Artifacts
| Blog | Author | Type | Path | Description |
|---|---|---|---|---|
| The MCP Catalog is here: discover, deploy and connect on Red Hat OpenShift AI | Peter Double | Red Hat Blog | mcps/mcp-catalog/blogs/ | MCP Catalog announcement for RHOAI 3.4 DP. Covers three-tier MCP ecosystem (3 Red Hat, 5 partner, 2 community), MCP Lifecycle Operator deployment, MCP Gateway on RHCL, and enterprise governance roadmap. Target: Summit 2026 / April 24 submission. |

---

## 13. Open Questions & Risks

### Open Questions
1. What exact MCP metadata should be mandatory for registry records?
2. Which lifecycle states are required for 3.5 Dev Preview?
3. How should registry-governed state inform gateway behavior?
4. How should registry state influence catalog surfacing?
5. What belongs in MLflow upstream proposal now vs later?
6. Skills packaging format - OCI artifacts? Markdown? Zip bundles? **Partially answered**: Databricks MVP uses SKILL.md with YAML frontmatter as the canonical format, stored as directory artifacts in MLflow artifact store. OCI and other formats not addressed in MVP.
7. Agent identity model - SPIFFE/SPIRE or alternative?
8. How to handle OLM 1.0 dependency removal in OCP 5.0?
9. Whether certification programs should be backed by an admin-managed allowlist (from Data Model Proposal)
10. Whether lifecycle history should be added in MVP or deferred (from Data Model Proposal)
11. Whether approval, verification, and certification transitions need distinct permissions beyond general update permissions (from Data Model Proposal)
12. Whether MCP registry entities need `EntityAssociationType` support in MVP or later (from Data Model Proposal)
13. How should MCP Lifecycle Operator interact with registry entries — read-only "discovered/managed" entries vs. full governance? (from Data Model Proposal comments - Chris Hambridge, Matt Prahl)
14. What does Snyk's AI/agent scanning actually check? Is it needed beyond standard CVE scanning? (Ann Murray recommendation — needs research)
15. What should the standardized MCP container metadata schema look like for Kubernetes? (No upstream standard exists; all 4 partner MCPs in 3.4 had different metadata)
16. How should the partner ingestion pipeline be orchestrated? (Konflux suggested; needs investigation)
17. How does MCP Checker evaluation integrate with MLflow's evaluation capabilities? (MLflow becoming main eval tool for AI assets)
18. What is the partnership ecosystem team's existing pipeline work? (Follow up with Serob and Matt Dorn)
19. What indemnification clause is needed for partners whose MCPs are in our catalog? (Oracle requested IP indemnity; legal ownership unclear — may need platform-level answer for all agentic assets)
20. How should Azure "Agentic Packs" / plugins beyond MCP be handled in AI Hub? (Azure requested this during consent process)
21. What is the deprecation plan for FY25 web-based MCP catalogs (redhat.com pages)? (Draft started by Matt Dorn and Sharon Dashet)
22. AI Gateway architecture decision: Which gateway approach will Red Hat adopt? (Chief architects Jason and Jessica reviewing; meeting ~2 weeks from 2026-04-13; Envoy not AI-native, Solo.io agent gateway has leadership issues, many alternatives under review)
23. How should community MCP servers be positioned differently from partner servers in the catalog? (MariaDB and MongoDB in 3.4 but not featured in Summit splash)
24. MaaS (AI Gateway) resourcing concern: Only 5 engineers (2 experienced) vs 40 on MLflow — risk to gateway quality (raised by Peter, 2026-04-13)
25. RHOAI-RHCL agreement excludes MCP and Agents from unlimited tokens and token metrics (struck through in agreement). What are the implications for MCP/Agent token tracking and cost allocation? Is a separate agreement needed?
26. RHOAI-RHCL Case 2 agreement (customers installing full RHCL alongside RHOAI for extended functionality) — not yet developed; needed when those use cases are defined
27. RHOAI 3.5 + OCP 4.19 version gap: RHCL likely won't support OCP 4.19 when RHOAI 3.5 ships (Aug 2026). Best-effort only for 4 months until OCP 4.19 EOM (Dec 2026)

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
- Chris Hambridge / Matt Prahl (Data Model Proposal): Should MCP Lifecycle Operator create read-only "discovered/managed" registry entries with restricted mutation? (related to RHAIRFE-294)
- Jon Burdo (Data Model Proposal): Certification allowlist question may connect to RHAIRFE-294
- Sharon Dashet (Partners Doc): Partner support/indemnification is a bigger scope question — "What is our legal liability to our customers when they use our agentic platform?"
- Sharon Dashet (Partners Doc): Certification program for AI assets should be lighter than operators to avoid driving partners away
- Hugo Rivero (Partners Doc): Which metadata requirements are RHOAI-specific vs part of the MCP specification?
- Noel O'Connor (Agentic pod): Sellers may only remember "Red Hat doesn't do agents" from IBM/RH joint slides — need proactive counter-messaging
- Jennifer Vargas (Agentic pod): IBM owning "AI Governance" and "gateway" in joint slides will cause problems for field sales long-term; need seller enablement materials
- Amana Juricic (RHOAI-RHCL Agreement): "Are we anticipating any troubleshooting required from support side or between teams (support & engineering) on those requests?" — support team reviewing scope of cross-team troubleshooting
- Rutuja Argade (RHOAI-RHCL Agreement): Clarifying when customers should be asked to upgrade to OCP 4.20 and workflow for opening tickets with RHCL engineering
