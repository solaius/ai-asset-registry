# AI Asset Registry - Documentation

## Quick Start

Start with the **[Knowledge Registry](knowledge-registry.md)** - it tracks all key information, decisions, and references in one place.

## Structure

```
docs/
  knowledge-registry.md          # Living knowledge base (start here)
  overview/
    vision-and-strategy.md       # Product vision and strategic direction
    scope.md                     # In/out of scope, MVP definition
    roadmap.md                   # Release timeline and phases
  architecture/
    core-concepts.md             # Registry, Catalog, Plugin, AAA, etc.
    system-boundaries.md         # Component responsibilities and integration flow
  assets/
    asset-types.md               # Primary and secondary AI asset types
  components/
    mcp-gateway.md               # MCP Gateway (Kuadrant) - runtime enforcement
    mcp-lifecycle-operator.md    # Lifecycle Operator - deployment primitive
    mcp-catalog.md               # MCP Catalog - discovery surface
    mcp-registry.md              # MCP Registry (NEW) - governance backbone
    repositories.md              # GitHub repository reference
  personas/
    target-users.md              # AI Engineers, Platform Engineers, AgentOps Admins
  strategy/
    agentic-strategy.md          # Red Hat Agentic AI strategy context
  flows/
    lifecycle-flow.md            # Lifecycle stages, current vs target flows
  security/
    governance-and-security.md   # RBAC, policy, supply chain, zero-trust
  competitive/
    landscape.md                 # MCP catalogs/registries competitive analysis
  integration/
    platform-integration.md      # OpenShift AI, SKUs, MLflow/Databricks upstream
  decisions/
    key-decisions.md             # Decided items and open questions
  starting-artifacts/            # Source PDFs, transcriptions, spreadsheets
```

## Source Documents

All documentation was synthesized from:
- 7 local documents (PDFs, meeting transcriptions, spreadsheets)
- 6 Google Docs (product requirements, strategy, deployment, catalog, registry MVP)
- 5 GitHub repositories (lifecycle operator, gateway, model registry, architecture context, AI helpers)
