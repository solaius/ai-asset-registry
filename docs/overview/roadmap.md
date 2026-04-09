# AI Asset Registry - Roadmap

## Release Timeline

| Release | Timeframe | Registry-Relevant Content |
|---|---|---|
| RHOAI 3.0 GA | Nov 2025 | Baseline platform |
| RHOAI 3.4 | Apr 2026 (code freeze) | MCP Guardrail Dev Preview; Summit demo |
| **RHOAI 3.5** | **Target** | **MCP Registry Dev Preview; Agent registries; MCP Gateway in RHOAI** |
| OCP 5.0 | Future | MCP Servers + MCP Gateway included in platform |

## Phase 1: MCP Registry (3.5 Dev Preview)
- MCP server governed registration
- Metadata-first records with lifecycle state
- Certification/approval visibility
- RBAC-aware governance
- Integration with existing catalog, deployment, gateway, and Studio

## Phase 2: Agent & Skills Registry
- Agent registry as governed assets
- Skills registry (pending upstream Databricks work)
- Relationship modeling (agent -> model, prompt, MCP server, guardrail)
- Promotion workflows across environments

## Phase 3: Unified AI Asset Registry
- Plugin framework for arbitrary asset types
- Guardrails, knowledge sources, prompts as first-class registry assets
- Full lifecycle automation
- Supply chain integration (SBOM, provenance, attestation)
- Federation across registries

## Phase 4: Future Expansion
- Notebooks, pipelines, evaluators as registry assets
- Cross-environment promotion with full lineage
- Multi-tenancy support
- Policy engine integration (OPA, Kuadrant)
- Agent Gateway alignment

## Key Dependencies
- Databricks upstream design approval process (~1 month per design)
- Skills packaging format standardization
- OCP 5.0 OLM 1.0 migration (dependency mechanism changes)
- MLflow GenAI capabilities expansion

## Key Jira Tickets
- RHAIRFE-1370: AI Asset Registries (main epic)
- RHAIRFE-1457: MCP Gateway deployment in RHOAI 3.5
- RHAIRFE-1456: MCP Lifecycle Operator in RHOAI 3.5
