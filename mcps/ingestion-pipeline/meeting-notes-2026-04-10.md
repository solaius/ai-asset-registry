# MCP Ingestion Pipeline - Meeting Notes

**Meeting**: MCP Pipeline w/ Gen MCP
**Date**: April 10, 2026 (8:30 AM, 34 minutes)
**Participants**: Peter Double, Calum Murray, Matthias Wessendorf, Nader Ziada, Jose Gonzalez

---

## Objective

Define how MCP servers from partners and customers are ingested, validated, secured, and made available through the RHOAI platform. Establish the role of Gen MCP, scanning/evaluation tooling, and the partnership ecosystem team's pipeline in this process.

---

## Context & Problem Statement

Multiple teams are asking for a structured way to bring MCP servers into the product:

- **Partnership Ecosystem team** needs a pipeline to ingest, validate, and maintain partner MCPs independently of the product release cycle
- **Services and Sales** need a way for customers (platform engineers) to quickly create MCPs from existing APIs
- **Product** needs standardized metadata, security scanning, and evaluation before MCPs reach the catalog and registry
- **Current state is manual and inconsistent**: the four partner MCPs onboarded for 3.4 all had different metadata formats, different containerization approaches, and required individual manual adaptation

Once the MCP Registry (MLflow, targeting 3.5 Dev Preview) is in place alongside the existing MCP Gateway and Lifecycle Operator, having a structured ingestion pipeline would give Red Hat end-to-end control of the MCP ecosystem — from ingestion through governance, deployment, and consumption.

---

## Key Discussion Points

### 1. Two Ingestion Lanes

The meeting identified two distinct lanes for how MCPs enter the platform:

#### Lane 1: Partner MCP Ingestion
- Partners provide MCP server repositories expected to meet a defined checklist
- Pipeline validates the repo, scans for vulnerabilities, evaluates the MCP, and containerizes it
- Most partners are expected to bring **already-containerized** MCPs
- For partners with basic (stdio or unsecured HTTP) MCPs, Gen MCP can wrap them with auth, TLS, and observability
- **Goal**: Eventually operate **off-cycle** from product releases so partners can be onboarded and updated continuously

#### Lane 2: Customer MCP Generation
- Platform engineers use Gen MCP to create MCP servers from existing API definitions (OpenAPI), CLI interfaces, or HTTP backends
- Lower-code approach — sales teams are already pitching this to customers
- Gen MCP handles the full flow: generation, containerization (via KO build tool), and runtime wrapping

### 2. Gen MCP's Role — Scoped, Not Expanded

Initial hypothesis was that Gen MCP could handle the entire ingestion pipeline (generation, containerization, validation, scanning). The meeting clarified the boundaries:

| Capability | Gen MCP Role | Notes |
|---|---|---|
| **MCP Generation** | Primary tool | From API definitions, CLIs, HTTP backends |
| **Containerization** | Only for Gen MCP-generated servers | Opinionated; uses KO build. For partner MCPs that are already containerized, use podman/docker |
| **Runtime Wrapping** | Useful for basic MCPs | Add auth, TLS, observability to stdio/unsecured HTTP servers that lack production features |
| **Evaluation** | Extracted to MCP Checker | Separate org (`mcpchecker`); works with any MCP, not just Gen MCP-generated |
| **CVE Scanning** | Not in scope | Handled by Quay, Conflux, or Snyk |

**Key decision**: Gen MCP is not being expanded to be a universal containerization/pipeline tool. It excels at generation and runtime wrapping; scanning and pipeline orchestration belong to other tools.

### 3. Evaluation Framework (MCP Checker)

- Evaluations were pulled out of Gen MCP into a **separate project** (`mcpchecker`, its own org) because they are generic for any MCP
- Already in use for OpenShift MCP Server pre-release evaluations
- Supports configurable agents — customers can re-evaluate catalog MCPs with their own agent (e.g., tested with Claude Code/Gemini but customer uses Amazon Q)
- **Two evaluation contexts**:
  - **Pipeline evaluation**: Automated as part of ingestion, producing eval scores before catalog listing
  - **Customer re-evaluation**: Post-catalog, customer runs evals with their specific agent and configuration
- **Open question**: How MCP Checker integrates with MLflow, which is becoming the main evaluation tool for all AI assets

### 4. Scanning & Security

| Tool | Purpose | Status |
|---|---|---|
| **Quay** | CVE scanning of container images | Currently used by Jose for partner MCPs |
| **Dependabot** | Repo-level dependency security (GitHub) | Available for partner repos |
| **Snyk** | AI/agent-specific scanning | Ann Murray recommending; Peter needs to research what it specifically scans |
| **Conflux** | Pipeline orchestration for productization | Matthias suggested; needs investigation |

Current approach (3.4): Jose relied on Quay scanning. Partner dependencies were mostly left untouched to avoid breaking their applications — only UBI base image updates were applied. One or two images required Python version changes.

### 5. Metadata Standardization — Critical Gap

- All four partner MCPs onboarded in 3.4 had **different metadata formats**
- No upstream Kubernetes standard exists for MCP container metadata
- The Anthropic MCP specification does not address Kubernetes deployment concerns (auth, TLS, observability, container labels)
- **Red Hat opportunity**: Lead the upstream conversation for Kubernetes MCP container metadata standards
- Gen MCP can enforce standardized metadata for servers it generates, but partner MCPs need a defined schema to conform to
- Chris Hambridge and Matthias have been working on metadata definitions; Matthias tested partner servers (e.g., Terraform) with the Lifecycle Operator

### 6. Production Readiness Wrapping

For partner MCPs that lack production features, Gen MCP runtime can add:
- **Authentication/Authorization** (OAuth, token exchange)
- **TLS**
- **Observability** (metrics, tracing)
- This is independent of whether the MCP uses stdio or HTTP — any MCP without security gets the wrapper

Matthias confirmed: the Terraform MCP server, for example, has no auth at all. Gen MCP can handle that.

---

## Current State (3.4 Partner Onboarding — Jose's Experience)

Jose shared the hands-on experience of onboarding partner MCPs for 3.4:

1. Several partners provided containers; some did not
2. Adaptation work included:
   - Rebasing to UBI (some didn't use UBI as base image)
   - Adding required container labels
   - Copying partner licenses
   - Building containers from scratch for partners without Dockerfiles
3. CVE scanning done via Quay
4. Partner application dependencies were **not modified** to avoid breaking partner code
5. No end-to-end testing of partner applications was performed — only container-level checks
6. Process was entirely manual with no standardized pipeline

---

## Action Items

| # | Action | Owner | Status |
|---|---|---|---|
| 1 | Research Snyk capabilities — what does AI/agent scanning actually check? | Peter Double | Open |
| 2 | Follow up with Matt Dorn / Jose on what was needed to get partner MCPs to containerization level | Peter Double | Open |
| 3 | Follow up with Serob (Partnership Engineering) on their existing pipeline work | Peter Double | Open |
| 4 | Create Jira ticket for MCP ingestion pipeline work | Peter Double | Open |
| 5 | Build the management story/case for Gen MCP and pipeline to secure dedicated engineering time | Peter Double | Open |
| 6 | Define standardized MCP container metadata schema | Peter Double / Chris Hambridge / Matthias | Open |
| 7 | Investigate Conflux as pipeline orchestration tool | TBD | Open |
| 8 | Determine how MCP Checker evaluations integrate with MLflow evaluation capabilities | Peter Double / Calum Murray | Open |

---

## Participants & Roles

| Person | Team | Role in This Work |
|---|---|---|
| **Peter Double** | RHOAI Product Management | PM driving MCP ecosystem, ingestion pipeline requirements |
| **Calum Murray** | Gen MCP Engineering | Gen MCP lead; MCP Checker architecture |
| **Matthias Wessendorf** | Gen MCP Engineering | Gen MCP engineering; metadata and Lifecycle Operator testing |
| **Nader Ziada** | Gen MCP Engineering | Engineer (ex-Knative Serverless, recently joined) |
| **Jose Gonzalez** | Partnership Ecosystem (under Matt Dorn) | Hands-on partner MCP containerization and onboarding |
| **Chris Hambridge** | Product Management | Architect; partnership ecosystem, metadata work (not present) |
| **Ramesh** | Registry & Catalog | Observer for registry integration awareness (not present, invited) |

---

## Key Takeaways

1. **Two-lane ingestion model**: Partner MCPs (containerized, pipeline-managed) and customer MCPs (Gen MCP-generated) are distinct flows with shared validation/scanning/evaluation components
2. **Gen MCP is not the universal pipeline tool** — it excels at generation and runtime wrapping, not at general containerization or scanning
3. **MCP Checker is the evaluation standard** — generic, agent-configurable, already extracted from Gen MCP into its own project
4. **Metadata standardization is the most urgent gap** — no two partner MCPs look the same at the container level
5. **Red Hat can lead upstream** on Kubernetes MCP container metadata standards — no community exists for this yet
6. **Off-cycle partner management is the end goal** — the pipeline should eventually decouple partner MCP updates from the RHOAI release cycle
7. **Management buy-in needed** — Calum noted that a clear story is required for management to allocate dedicated engineering time to this work
