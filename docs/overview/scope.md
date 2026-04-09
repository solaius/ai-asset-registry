# AI Asset Registry - Scope

## In Scope

- **Unified registry framework**: Common product framework for multiple AI asset registries
- **Metadata-first registry behavior**: Metadata, references, relationships, governance state
- **Multiple AI asset types**: Priority assets first, extensible via plugins
- **Plugin-based extensibility**: New asset types without bespoke registry redesign
- **Multiple backend registries**: Federated approach (MLflow, Kubeflow, future backends)
- **Dev and prod registry support**: Including promotion and lineage
- **Inner-loop to outer-loop workflows**: Local dev -> cluster -> production
- **OpenShift AI alignment**: Kubernetes, CRDs, APIs, containers, multi-tenancy
- **Catalog and registry interoperability**: Separate but complementary
- **Security and policy-aware governance**: Access control, identity, policy, audit
- **Future platform enablement**: Evaluation, observability, lifecycle automation, gateway integration

## Out of Scope (for initial document)

- Detailed implementation design (controllers, storage, service architecture)
- Backend-specific implementation decisions (how MLflow/Kubeflow must be extended)
- Final API schema / CRD design
- Detailed UI/UX design
- Full lifecycle automation for every asset type in MVP
- Full supply chain feature completeness in MVP
- Support for every possible AI asset type on day one

## MVP Scope (3.5 Dev Preview)

For the MCP Registry as first implementation:
- Governed registration of MCP servers
- Metadata-first MCP records
- Lifecycle state tracking
- Certification and approval visibility
- Support for internal and external MCP representation
- RBAC-aware visibility and governance
- Conceptual integration expectations for catalog, deployment, gateway, and AAA/Studio
