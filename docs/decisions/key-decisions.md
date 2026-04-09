# Key Decisions & Open Questions

## Decided

### Registry vs Catalog Backend Assignment
- **Decision**: MLflow for registries, Kubeflow for catalogs
- **Date**: Reaffirmed 2026-04-07
- **By**: Adam Bellusci
- **Rationale**: Clear separation of governance (MLflow) and discovery (Kubeflow) concerns

### MCP and Agents as Top Priority for 3.5
- **Decision**: MCP servers and agents are the highest priority asset types for RHOAI 3.5
- **Date**: 2026-03-19 meeting
- **Rationale**: Growing enterprise demand for MCP governance; agent registries needed to prevent "agent sprawl"

### Metadata-First Registry Approach
- **Decision**: Registry manages metadata and references, not actual asset payloads
- **Rationale**: Aligns with MLflow's metadata/artifact separation; assets may live in many places

### Plugin-Based Extension Model
- **Decision**: New asset types added via plugins rather than bespoke registry implementations
- **Rationale**: Avoids repeated one-off efforts; enables faster expansion to new asset types

## Open Questions

### Lifecycle States
- MLflow has: None, Staging, Production, Archived
- MCP proposal has: Draft, Candidate, Verified, Approved, Published, Deprecated, Retired
- **Question**: Do we need to expand MLflow states? Or map between them?
- **Raised by**: Dan Kuc

### Registry-First vs Catalog-First Flow
- Models currently flow: Catalog -> Registry
- MCP servers proposed: Registry -> Catalog
- **Question**: Is the opposite flow intentional? How do we reconcile?
- **Raised by**: Dan Kuc

### Skills Packaging Format
- No standard format yet
- Options: OCI artifacts, markdown files, zip bundles
- Multiple competing approaches in the ecosystem (ClawHub, SkillNet, etc.)
- **Question**: What format should Red Hat standardize on?

### OCP 5.0 Deployment Strategy
- OLM 1.0 removes dependency declarations
- MCP Gateway depends on RHCL
- **Question**: How do we handle this? Platform-level installation? Alternative dependency mechanism?
- **Raised by**: Andrew Mackenzie

### MCP Server Registration in Gateway
- Currently manual MCPServer CR creation
- **Question**: Should this be automated from registry approval state?

### Mandatory MCP Metadata
- **Question**: What fields must be present in every MCP registry record?
- Candidates: identity, version, source, endpoints, transport, auth, lifecycle state, trust tier

### Agent Identity Model
- SPIFFE/SPIRE proposed for agent identities
- **Question**: Is this the right approach? What about integration with existing IdPs?
