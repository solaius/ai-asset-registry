# AI Asset Registry - Vision & Strategy

## Product Vision

A federated AI Asset Registry framework for Red Hat OpenShift AI that enables governance and lifecycle management for multiple AI asset types through a common, extensible product experience.

## Problem Statement

AI platforms are expanding beyond models. Organizations need to manage agents, MCP servers, prompts, guardrails, skills, and knowledge sources. Current approaches create isolated registries per asset type, leading to:
- Duplicated effort across teams
- Inconsistent user experiences
- Fragmented governance
- Slower support for new asset types

## Strategic Direction

### Registry vs Catalog Separation
- **Registries** (MLflow): Governance, lifecycle, identity, versioning, policy, auditability
- **Catalogs** (Kubeflow): Discovery, browsing, searching, filtering, curation

### Key Principles
1. **Metadata-first**: Registry governs metadata and references; actual assets live elsewhere
2. **Plugin-based extensibility**: New asset types added without bespoke registry efforts
3. **Federated model**: Multiple backend registries, unified product experience
4. **Dev and prod support**: Development registries and production registries with promotion between them
5. **Platform-native**: Kubernetes, CRDs, APIs, Gateway API alignment

## Goals
1. Enable AI assets as first-class governed citizens
2. Avoid one-off registry development per asset type
3. Provide unified experience across backend registries
4. Support inner-loop to outer-loop workflows
5. Create foundation for evaluation, observability, gateway integration, lifecycle automation
6. Align with OpenShift AI and Kubernetes-native patterns

## Source Documents
- [AI Asset Registries (Google Doc)](https://docs.google.com/document/d/1AtsP7xwlC15znJEfJ9jHiEow21kwUtIQAg3d06uC6wQ)
- [MCP Registry MVP Requirements (Google Doc)](https://docs.google.com/document/d/11mJpJ-Py8FxRDYdw41mMWEBvlahENS4rHqnpDNpqa8Y)
- docs/starting-artifacts/AI Asset Registries - Comprehensive.pdf
