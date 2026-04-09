# Governance & Security

## Governance Model

### RBAC-Aware Visibility
- Users see and act on assets within their governance boundaries
- Role-, project-, namespace-, and environment-scoped access
- Integration with identity and access systems (Keycloak, enterprise IdPs)

### Policy Linkage
- Assets associated with applicable policies and controls
- Registry carries metadata; enforcement via gateways, policy engines, controllers
- Alignment between registry governance and runtime enforcement

### Approval Workflows
- Approval separate from runtime enforcement
- Review and approval before assets are published/surfaced
- Governance continuity as assets evolve

### Auditability
- Lifecycle history preserved over time
- All catalog actions logged (surfacing, removal, revocation)
- Full traceability for compliance and security reviews

## Security Pipeline

### Supply Chain Security
- SBOM generation for MCP servers and agents
- CVE scanning (Clair, Syft, Grype)
- Provenance checks and signed attestations
- Stacklok ToolHive integration for secure MCP server execution

### Metadata Extensions
- `x-certificationStatus`: Certification level of the asset
- `x-securityScan`: Security scan results and status
- `x-maturityLevel`: Maturity classification

### Trust Tiers
- Certified (Red Hat verified)
- Community (community-contributed, moderated)
- Unverified (external, unreviewed)

## Agent Security (Zero-Trust)
From the Agentic Strategy:
- Agents need their own verifiable identities (SPIFFE/SPIRE or equivalent)
- Fine-grained, dynamic access control
- On-behalf-of token exchange patterns
- Integration with enterprise IdPs (Keycloak, EntraID)
- Granular data access policies (Zero Trust alignment)

## MCP Gateway Security
- OAuth 2.0 protected resource support
- Identity-aware tool filtering (allow/deny per tool)
- Per-tool authentication and authorization via Authorino/RHCL
- TLS termination at Gateway level
- Certificate management via cert-manager or Kuadrant TLSPolicy
