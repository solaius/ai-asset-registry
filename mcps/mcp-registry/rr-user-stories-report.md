# Ramesh Reddy Feedback — User Story Alignment Report

**Date**: 2026-04-16
**Context**: Ramesh reviewed the MCP Registry 3.5 user stories and provided a UI-oriented flow that reimagines how the stories map to the RHOAI product experience. This report compares his feedback point-by-point against the existing user stories in `mcp-registry-3.5-user-stories.md`.

---

## Summary

**Ramesh's feedback is largely a UI-level restatement of the existing stories, not a gap analysis.** His flow describes how the stories would manifest in the RHOAI console — navigation, buttons, tabs — while the existing stories deliberately exclude UI and focus on backend capabilities and cross-component interactions.

There are **two genuinely new ideas** in his feedback that go beyond what the stories cover:

1. **Deploy-from-catalog with registry selection** (his "Story 11") — a UX pattern that unifies the deployment flow
2. **Virtual MCP server / tool-selection execution policy** (his "Story 7" annotation) — a new concept for tool filtering at the gateway level

Everything else maps cleanly to existing stories.

---

## Point-by-Point Mapping

### 1. Rename "Model Registry" to "Registry"

| | |
|---|---|
| **Ramesh says** | Update "Model Registry" creation to simply "Registry" creation |
| **Existing coverage** | Not in user stories (UI naming is out of scope), but fully aligned with the multi-asset registry direction described in the overview |
| **Verdict** | **Already aligned** — this is a UI/branding decision, not a missing requirement |

---

### 2. Left nav: Models, MCPs, Agents, etc.

| | |
|---|---|
| **Ramesh says** | Include sections like Models, MCPs, Agents in left navigation |
| **Existing coverage** | Not in user stories (UI design is out of scope). Consistent with the AI Asset Registry vision |
| **Verdict** | **Already aligned** — navigation structure is a UI design concern, not a registry capability gap |

---

### 3. MCP catalog view showing public + workspace-scoped MCPs

| | |
|---|---|
| **Ramesh says** | When a user clicks MCPs, show all publicly accessible MCPs from the catalog plus workspace-scoped MCPs the user can access (references his "Stories 3, 5") |
| **Existing coverage** | **Story 3** (retrieve and list MCP records with scope filtering), **Story 5** (surface publishable MCPs to catalog), **Story 9** (enforce scoped visibility) |
| **Verdict** | **Fully covered** — Stories 3, 5, and 9 together define exactly this behavior. Ramesh is describing the UI presentation of these capabilities |

---

### 4. Platform admin "Add MCP" button

| | |
|---|---|
| **Ramesh says** | For a platform admin, provide an "Add MCP" button to select a registry instance and enter MCP metadata (references his "Stories 1, 2") |
| **Existing coverage** | **Story 1** (register internal MCP server), **Story 2** (register external MCP server) |
| **Verdict** | **Fully covered** — Stories 1 and 2 define the registration capability. The "select a registry instance" detail implies multi-registry support, which is not explicit in the current stories but is implied by the multi-asset registry framework direction |

**Minor nuance**: Ramesh's mention of "selecting a registry instance" suggests he's thinking about a world where multiple registry instances exist simultaneously. The current stories assume a single registry. This isn't a gap for 3.5 Dev Preview but is worth noting for future phases.

---

### 5. Platform engineer: draft-to-published toggle

| | |
|---|---|
| **Ramesh says** | In the catalog view, provide an option to switch an MCP from "draft" to "published." Once published, it becomes visible to AI engineers; otherwise hidden (references his "Story 9") |
| **Existing coverage** | **Story 4** (change publish state — draft/published/deprecated), **Story 5** (catalog surfaces only published MCPs), **Story 9** (scoped visibility) |
| **Verdict** | **Fully covered** — Story 4 is the exact capability described. Ramesh's reference to "Story 9" maps to our Story 4 (numbering mismatch likely from his own ordering). The visibility filtering is covered by Stories 5 and 9 |

---

### 6. "Deploy" button in catalog with target registry selection

| | |
|---|---|
| **Ramesh says** | When PE or AIE clicks "Deploy" in the catalog, allow choosing the target registry. This removes the disjointed 3.4 deployment flow. The selected registry stores runtime details (endpoint). References his "Story 11" |
| **Existing coverage** | **Story 6** (associate deployment/runtime references with registry records) covers the *data model* — storing deployment details against a registry record. The current stories do not describe a unified deploy-from-catalog UX flow |
| **Verdict** | **Partially new** — The underlying capability (Story 6) exists, but Ramesh is proposing a *deployment UX pattern* that doesn't have a corresponding story: initiate deployment from the catalog, select a target registry, and automatically capture the runtime reference back to the registry record. This is a UX integration pattern that bridges catalog, lifecycle operator, and registry in a single user action |

**What's actually new here**: The concept of the catalog as the deployment entry point (rather than separate lifecycle operator interaction) with automatic registry association. The data model supports it; the orchestrated flow doesn't have a story.

---

### 7. Virtual MCP server / tool-selection execution policy

| | |
|---|---|
| **Ramesh says** | "Not explicitly in your stories": during deployment, simplify tool selection to create a "virtual MCP server" that the MCP Gateway uses as an execution policy. This policy is stored in the registry and shared with the Gateway |
| **Existing coverage** | **Story 7** (provide gateway-relevant metadata) is the closest match, but it describes reading metadata from the registry for gateway use — not creating a derived "virtual MCP server" or execution policy |
| **Verdict** | **New concept** — This is a genuinely new idea. The virtual MCP server concept means: instead of deploying a full MCP server, a user could select specific tools from an MCP and create a policy-filtered view that the gateway enforces. This is a runtime governance pattern not covered by any existing story |

**Why this matters**: This bridges the gap between "what tools an MCP offers" and "what tools a specific deployment is allowed to use." It's a gateway-level concern that implies registry storage of tool-level policy — a richer data model than the current stories assume.

---

### 8. Deployment triggers Gateway, captures endpoint

| | |
|---|---|
| **Ramesh says** | The deployment step could trigger the Gateway and capture the resulting endpoint in the registry |
| **Existing coverage** | **Story 6** (deployment references), **Story 7** (gateway metadata). The overview explicitly notes that full automation of these handoffs is a later-phase goal |
| **Verdict** | **Already scoped as future** — The current stories deliberately scope 3.5 as "store state, automate later." Ramesh is describing the fully automated version, which is the target architecture but not 3.5 Dev Preview scope |

---

### 9. MCP details page with "Deployments" tab

| | |
|---|---|
| **Ramesh says** | Include a "Deployments" tab showing all deployments of that MCP across registries within user's scope |
| **Existing coverage** | **Story 6** (deployment references provide the data), **Story 9** (scoped visibility). UI is explicitly out of scope |
| **Verdict** | **Already supported by data model** — Story 6 stores the deployment associations. A deployments tab is a UI concern. The data model supports it |

---

### 10. AAA namespace inference

| | |
|---|---|
| **Ramesh says** | AAA already operates within a namespace/workspace, so we should be able to infer the relevant MCP registry and available MCP server instances for execution — "end of story" |
| **Existing coverage** | **Story 8** (surface consumable MCPs to AAA/Studio), **Story 9** (scoped visibility) |
| **Verdict** | **Fully covered** — Ramesh is confirming that the existing scope model works. His "end of story" comment suggests he sees this as straightforward, not a gap |

---

### 11. Overall: UI tightly aligned with catalog

| | |
|---|---|
| **Ramesh says** | This approach keeps the UI tightly aligned with the catalog and primarily requires adding a deployments view to catalog screens backed by registry |
| **Existing coverage** | Consistent with the architecture: Registry = governance, Catalog = discovery surface |
| **Verdict** | **Aligned** — This is an endorsement of the component boundaries defined in the overview |

---

## What's Actually New (Action Items)

| # | New Concept | Source | Recommendation |
|---|---|---|---|
| 1 | **Deploy-from-catalog UX flow** with registry selection and automatic runtime reference capture | Point 6 | Consider adding a Story 11 that describes the orchestrated deploy flow as a UX integration pattern. This doesn't change the registry capability but defines how catalog, lifecycle operator, and registry interact in a single user action. May be out of scope for 3.5 if UI is excluded, but worth capturing as a target experience. |
| 2 | **Virtual MCP server / tool-selection execution policy** stored in registry for gateway consumption | Point 7 | This is a richer concept than current Story 7. It implies tool-level granularity in registry records and a derived policy object. Worth discussing with Ramesh to understand if this is a 3.5 need or a later-phase enhancement. If pursued, it would expand Story 7 and potentially add a new story around tool-level policy. |

---

## What's Not New (Just Different Framing)

| Ramesh Point | Existing Story | Why It Looks Different |
|---|---|---|
| Catalog showing public + scoped MCPs | Stories 3, 5, 9 | Ramesh describes the UI; stories describe the API capability |
| Add MCP button | Stories 1, 2 | Ramesh describes the UX entry point; stories describe the data operation |
| Draft-to-published toggle | Story 4 | Ramesh describes a UI control; stories describe state transitions |
| Deployments tab | Story 6 | Ramesh describes a UI view; stories describe the data model |
| AAA namespace inference | Stories 8, 9 | Ramesh confirms the design works; stories define it |
| Automated gateway integration | Stories 6, 7 | Ramesh describes full automation; stories scope 3.5 as "store state" with automation later |

---

## Bottom Line

Ramesh's feedback validates the existing story set. His flow is a UI-level walkthrough of how the stories come to life in the RHOAI console, which is a useful complement to the backend-focused stories. The two genuinely new ideas (deploy-from-catalog flow and virtual MCP server concept) are worth discussing but don't represent missed requirements in the current 3.5 Dev Preview scope — they're UI integration patterns and gateway-level enhancements that build on the foundation the stories already define.

The story set doesn't need revision. What it needs is a companion UX flow document (which Ramesh's feedback essentially starts) that shows how these backend capabilities manifest in the product experience.
