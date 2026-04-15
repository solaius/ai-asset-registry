# Blog Submission Form

- **Publication type**: Red Hat Blog (redhat.com/en/blog)
- **Title**: The MCP Catalog is here: discover, deploy and connect on Red Hat OpenShift AI
- **Subtitle**: Your agents need tools and the MCP Catalog in Red Hat OpenShift AI delivers them
- **Byline**: Peter Double
- **Target publication**: Pre-Summit / Summit 2026 (May 11-14, Atlanta)
- **Submission deadline**: April 24, 2026
- **Series**: First post in MCP ecosystem series
- **Products mentioned**: Red Hat OpenShift AI, Red Hat OpenShift, Red Hat Ansible Automation Platform, Red Hat Insights, Red Hat Connectivity Link, MCP Lifecycle Operator, MCP Gateway
- **Partners mentioned**: Confluent, EnterpriseDB, HashiCorp | an IBM Company, Microsoft, Dynatrace
- **Community projects mentioned**: MongoDB, MariaDB
- **Partner review required**: Yes (Katie Giglio coordinating with all five partners)
- **Editorial contacts**: Aom Sinonpat (timeline), Jessie Beach (ecosystem blog writing), Lorraine (editor team)

### Pre-Submission Checklist
- [ ] Writing for Red Hat course completed within the last year
- [ ] At least one SME/peer review
- [ ] Product group review (see blog reviewer list)
- [ ] Partner review (all five partners via Katie Giglio)
- [ ] All links verified and working
- [ ] All product names match Official Product Names List
- [ ] Images generated/sourced and alt text finalized
- [ ] Meta title, meta description and custom URL slug provided

---

# The MCP Catalog is here: discover, deploy and connect on Red Hat OpenShift AI

**Your agents need tools and the MCP Catalog in Red Hat OpenShift AI delivers them**

You have tried it. Pulled an MCP server from a GitHub repo, wrestled it into a container, sorted out authentication yourself and hoped it would hold up in production. That is the state of [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) adoption in the enterprise today: promising protocol, painful deployment.

[Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai) 3.4 takes a different approach. We are introducing the **MCP Catalog** in Developer Preview, a curated catalog of MCP servers that you can discover, deploy and manage directly on [Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift). It ships pre-loaded with MCP servers from Red Hat, technology partners and the open source community, and we are actively adding more.

[**Try Red Hat OpenShift AI**](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial) to explore the MCP Catalog yourself.

This is not a static listing. Every MCP server in the catalog deploys live on your cluster through the [MCP Lifecycle Operator](https://github.com/kubernetes-sigs/mcp-lifecycle-operator), connects through the [MCP Gateway](https://github.com/Kuadrant/mcp-gateway) running on [Red Hat Connectivity Link](https://www.redhat.com/en/technologies/cloud-computing/connectivity-link) and becomes consumable in gen AI studio, the interface in Red Hat OpenShift AI where you build and test AI applications. This is the beginning of the enterprise MCP ecosystem we are building.

## The MCP Catalog: from discovery to deployment

Until now, the [AI Hub](https://docs.redhat.com/en/documentation/red_hat_openshift_ai_self-managed/3.4/html/working_with_the_model_catalog/overview-of-model-registries_working-model-catalog) in Red Hat OpenShift AI UI was focused on models. With 3.4, MCP servers join the catalog as first class citizens. It is also the first step toward a broader AI asset surface that will expand in upcoming releases.

Most MCP catalogs available today, including Smithery, Docker MCP Catalog and the official MCP registry, are discovery surfaces. They help you find servers, but deployment, security and lifecycle management are your problem. You download a container image of unknown provenance, configure it manually and hope the next update does not break your workload.

The MCP Catalog in Red Hat OpenShift AI closes that gap. When you browse the catalog in the AI Hub, you are looking at MCP servers that have been validated for enterprise deployment: production-grade connectivity (streamable HTTP transport), on-cluster hosting and container images built on [Red Hat Universal Base Image](https://www.redhat.com/en/blog/introducing-red-hat-universal-base-image) and scanned for vulnerabilities. When you select a server, the MCP Lifecycle Operator deploys it on your cluster, creating the Kubernetes resources and exposing the service. From there, the MCP Gateway handles runtime connectivity, providing identity-aware routing and per-tool metrics so your platform team knows exactly which agents are calling which tools.

The result is a governed path from discovery to deployment to consumption. Select, deploy, connect.

--------------------
**[Image Placeholder 1: MCP Catalog in AI Hub]**

**Placement rationale**: Shows the reader the catalog experience, including the AI Hub tabs, MCP server cards with partner branding and the deploy action path.

**Image generation prompt**: If the Red Hat OpenShift AI 3.4 UI is available, capture a real screenshot of the AI Hub MCP Catalog tab. If a generated mockup is needed: Red Hat OpenShift AI Hub interface with a left sidebar (#212427 dark background, navigation items including "AI Hub > MCP Catalog") and a main content area (#F0F0F0 light background). Show a 3x2 grid of MCP server cards featuring OpenShift, Ansible, Confluent, Dynatrace, Microsoft Azure and MongoDB. Each card displays: server name, provider logo area, one-line description, tier badge (Red Hat / Partner / Community) and a "Deploy" button in #EE0000 Red Hat Red. Use Red Hat Text for card body text, Red Hat Display for headings. 16:9 aspect ratio, clean enterprise UI style consistent with PatternFly design system.

**Alt text**: The MCP Catalog in the Red Hat OpenShift AI Hub UI displaying MCP server cards organized by tier (Red Hat, partner and community), each with a deploy action, showing the governed path from discovery to deployment.

--------------------

## MCP servers ready to use

The catalog ships with three tiers of MCP servers, each addressing real enterprise workflows. Here is what is available today.

**Three Red Hat MCP servers** connect your AI agents directly to the platforms you already run:

- **[Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift)**: Your agents can query cluster state, manage workloads and troubleshoot deployments through natural language. When a pod fails at 2 a.m., your on-call engineer asks the agent for the last 50 log lines and the resource status of the affected deployment. No context-switching between dashboards, no kubectl gymnastics.
- **[Red Hat Ansible Automation Platform](https://www.redhat.com/en/technologies/management/ansible)**: Connect agents to your automation workflows. An agent can trigger Ansible playbooks, check job status and orchestrate configuration changes across your infrastructure. For agent-driven operations (AgentOps) teams, this means incident response workflows that span detection, diagnosis and remediation without leaving the agent context.
- **[Red Hat Insights](https://www.redhat.com/en/technologies/management/insights)**: Surface platform intelligence and recommendations through your AI agents. Instead of your team manually reviewing Insights advisories, an agent can pull the latest recommendations, correlate them with your cluster state and suggest remediation steps, bringing operational insights directly into your agentic workflows.

**Five technology partner MCP servers** extend your agents into the broader enterprise stack:

- **Confluent Kafka**: Connect agents to your event streams for topic management, pipeline monitoring and real-time data access. If your agentic workflows need to react to live data, this is the integration point.
- **EnterpriseDB (PostgreSQL)**: Connect agents to PostgreSQL databases for queries, schema management and database operations, powered by EDB's pg-airman-mcp server.
- **HashiCorp | an IBM Company Terraform**: Let agents provision and manage infrastructure as code, bridging the gap between AI-driven decisions and infrastructure execution.
- **Microsoft Azure**: Enable agents to manage Azure resources, provision services and automate cloud operations alongside your OpenShift workloads.
- **Dynatrace**: Bring full-stack observability into your agentic workflows. Agents can query performance data, investigate incidents and correlate metrics across your environment.

**Two community MCP servers** round out the data tier:

- **MongoDB**: Query and manage document collections through your agents. Useful for RAG workflows and applications backed by unstructured or semi-structured data.
- **MariaDB**: Relational database connectivity for agents that need to query and manage structured data stores.

To put this in concrete terms: before the MCP Catalog, connecting Dynatrace to your agents meant finding a server on GitHub, building a container from source, debugging transport issues and configuring authentication, all before you could make a single tool call. Now, you select Dynatrace in the catalog and the Lifecycle Operator handles the rest.

**[Explore the MCP Lifecycle Operator on GitHub](https://github.com/kubernetes-sigs/mcp-lifecycle-operator)** to see how MCP servers are deployed and managed on Kubernetes.

--------------------
**[Image Placeholder 2: Three-tier MCP ecosystem]**

**Placement rationale**: Visually reinforces the three-tier structure (Red Hat, partner, community) and the breadth of use cases covered, giving the reader a scannable summary after the detailed listing.

**Image generation prompt**: Clean horizontal three-column layout on #FFFFFF white background. Left column labeled "Red Hat" (#EE0000 header bar): three rows listing OpenShift, Ansible Automation Platform, Insights with text labels in Red Hat Text 400 weight. Center column labeled "Partners" (#0066CC header bar): five rows listing Confluent, EnterpriseDB, HashiCorp | an IBM Company, Microsoft, Dynatrace with text labels. Right column labeled "Community" (#3D7317 header bar): two rows listing MongoDB, MariaDB with text labels. Center title above columns: "MCP Catalog" in Red Hat Display 600 weight. Thin #D2D2D2 borders between columns. Each row is the same height with centered text. 16:9 wide aspect ratio for inline placement.

**Alt text**: Diagram showing the three tiers of MCP servers shipping in the catalog: three Red Hat servers (OpenShift, Ansible Automation Platform, Insights), five partner servers (Confluent, EnterpriseDB, HashiCorp | an IBM Company, Microsoft, Dynatrace) and two community servers (MongoDB, MariaDB), illustrating the breadth of the day-one ecosystem.

--------------------

## Building the enterprise MCP ecosystem

The MCP servers you get in Red Hat OpenShift AI 3.4 are the foundation, not the ceiling. We are building toward a full enterprise MCP ecosystem with the capabilities that production deployments demand.

**Quickstarts are coming.** We are working with our partners to deliver guided [AI Quickstart](https://docs.redhat.com/en/learn/ai-quickstarts) experiences. Developed with strategic partners, AI quickstarts are ready-to-run, industry-specific use cases that put the power of **Red Hat AI** directly into your hands. These simple-to-deploy playgrounds help teams sharpen the skills needed to move AI ideas from experimentation to production on enterprise-ready, open source infrastructure.

**The ecosystem will grow.** We evaluated more servers than we shipped. Some required transport changes we could not support in Developer Preview. Others lacked the maintenance commitment we need from ecosystem partners. Curation is as much about what you leave out as what you include, and the process we have built, from partner consent through technical validation and catalog integration, is designed to scale. MCP servers are the first AI asset type going through this pipeline and we will use the same model as additional AI asset types mature.

**Enterprise governance is next.** Right now, you can discover and deploy. What you cannot yet do is trace where an MCP server came from, enforce which teams can deploy which servers or audit every tool call an agent makes. That governance layer is what we are building next: supply chain controls to verify every server's provenance and build, trust tiers to distinguish certified from community assets, access policies to scope deployment permissions and full auditability across the lifecycle. The goal is to give your platform team the same operational control over MCP servers that they already have for any other production workload.

## Get started

The MCP Catalog gives platform teams a governed, production-ready path from discovery to deployment: curated servers, validated images and lifecycle management on your cluster.

It is available now in Red Hat OpenShift AI 3.4 Developer Preview. **[Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial)** to explore the MCP Catalog and deploy your first MCP server on OpenShift. Attending Red Hat Summit 2026 in Atlanta (May 11-14)? Come see the catalog and MCP Lifecycle Operator in action.

This is the first post in a series on the MCP ecosystem in Red Hat OpenShift AI. Stay tuned for hands-on quickstarts with our partner MCP servers.
