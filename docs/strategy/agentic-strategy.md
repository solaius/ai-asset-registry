# Agentic AI Strategy Context

## Executive Summary
Red Hat's agentic AI strategy is to provide the **enterprise control plane** to securely run, manage, and govern any agent, built with any framework, at scale. Not building another framework - providing the "last mile" for enterprise readiness.

**Source**: [Agentic Strategy 2026 (Google Doc)](https://docs.google.com/document/d/1d_XK88Gxwr8zuPJZQ8_fbvah-y7PX12CYf4BTbvNVPE)
**Maintainer**: Adel Zaalouk
**Superseded by**: [Red Hat AI - Agentic AI Product Strategy 2026 [Draft]](https://docs.google.com/document/d/1-kd7FJIZwu7apyGXaUdBDGKdhX1a-pTNPdfUrC5sj-Q/edit)

## Four Pillars

### 1. Foundation (Build) - BYOA
- Framework-agnostic: LangChain, LangGraph, CrewAI, AutoGen all supported
- Llama Stack as OpenAI-compatible API foundation
- Responses API for agent building
- ResponsesAgent from MLflow as potential unifying primitive

### 2. Govern & Secure (AgentOps)
- **Observability & Evaluations**: Full traceability for agent workflows, dataset builder, evaluation engine
- **Zero-Trust Security**: Agent identities (SPIFFE/SPIRE), fine-grained dynamic access control, on-behalf-of token exchange
- **Safety**: Guardrails, policy enforcement, compliance alignment

### 3. Deploy & Manage
- **Agent Registry**: Centralized discovery, versioning, management of all agents
- Single source of truth for deploying agents as governed endpoints
- Lifecycle management across hybrid cloud
- Prevention of "agent sprawl"

### 4. AI Hub
- Unified surface for catalog, registry, deployments
- Tabs per asset type
- AAA (AI Available Assets) for RBAC-scoped consumption

## Relevance to AI Asset Registry
The Agent Registry (Pillar 3) is a direct instance of the broader AI Asset Registry framework. The registry proposal provides the underlying platform capability that makes agent lifecycle management possible alongside MCP servers, models, prompts, and other asset types.

Key alignment points:
- Agent Registry should use the same plugin-based framework as MCP Registry
- Observability and evaluation data should link back to registered assets
- Security/identity models should integrate with registry governance
- AI Hub surfaces registry-governed assets for consumption
