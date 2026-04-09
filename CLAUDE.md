# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

AI Asset Registry proposal for Red Hat OpenShift AI (RHOAI). Defines a unified, federated registry framework for governing multiple AI asset types (MCP servers, agents, models, prompts, skills, guardrails, knowledge sources) through a common product experience.

**Key principle**: Registry = Governance (MLflow), Catalog = Discovery (Kubeflow Hub). Metadata-first, plugin-based extensibility.

## Repository Structure

### Domain Workspaces (separated concerns)
- `mcps/` — MCP registry-specific work (highest priority, MVP)
- `agents/` — Agent registry-specific work
- `skills/` — Skills registry-specific work
- `ai-assets/` — Cross-cutting AI asset registry concerns

### Cross-Cutting Documentation
- `docs/knowledge-registry.md` — Living knowledge base: decisions, concepts, references across all source materials (start here)
- `docs/overview/` — Vision, strategy, scope, roadmap
- `docs/architecture/` — Core concepts, system boundaries
- `docs/assets/` — AI asset type definitions and registry needs
- `docs/components/` — MCP Gateway, Lifecycle Operator, Registry, Catalog, repo reference
- `docs/personas/` — Target users and customer insights
- `docs/strategy/` — Agentic AI strategy context
- `docs/flows/` — Lifecycle and asset flows
- `docs/security/` — Governance, security, policy
- `docs/competitive/` — Competitive landscape analysis
- `docs/decisions/` — Key decisions and open questions
- `docs/integration/` — Platform integration, SKUs, deployment
- `docs/starting-artifacts/` — Source PDFs, meeting transcriptions, spreadsheets

## Key Context

- Owner: Peter Double (Principal PM - MCP)
- Target: RHOAI 3.5 Dev Preview for MCP Registry
- Upstream: MLflow/Databricks collaboration; design approval required before coding
- Related repos: kubernetes-sigs/mcp-lifecycle-operator, Kuadrant/mcp-gateway, kubeflow/model-registry (renaming to kubeflow/hub)
- Jira: RHAIRFE-1370 (main), RHAIRFE-1457 (gateway in RHOAI), RHAIRFE-1456 (lifecycle operator in RHOAI)
- The proposal covers three distinct problem areas: (1) Registry — storage, versioning, distribution (MLflow, clear MVP), (2) Studio — UI for prompt/skill iteration, (3) Ecosystem/marketplace — packaging, distribution, partner plugins. Keep these concerns separated.

## Status

Proposal and documentation phase. No application code yet. The `docs/knowledge-registry.md` is the central reference for all gathered information.
