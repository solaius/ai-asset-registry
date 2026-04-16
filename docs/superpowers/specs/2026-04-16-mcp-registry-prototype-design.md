# MCP Registry Prototype — Design Spec

**Date:** 2026-04-16
**Author:** Peter Double (Principal PM — MCP)
**Status:** Approved (brainstorm complete)

## Purpose

A frontend-only UX prototype of the proposed MCP Registry, styled as a native MLflow 3.10 feature. The prototype demonstrates how MCP servers fit into MLflow's existing model registry patterns, making the case to the design committee and upstream MLflow stakeholders that MCP servers belong as a first-class asset type in MLflow.

## Audience

Design committee and upstream MLflow stakeholders — people who need to approve the MCP Registry proposal and see how it fits into MLflow's existing UI patterns.

## Scope

### In Scope
- Full interactive prototype (simulated functional — forms open, state transitions work, navigation between views, filters respond)
- All 10 launch MCP servers as sample data
- Feature toggle (3.5 DP / 3.next) to show base scope vs full governance vision
- MLflow 3.10 accurate design (DuBois design system, GenAI/ML workflow switcher)

### Out of Scope
- Backend or API integration
- Real data persistence
- Authentication / RBAC enforcement
- Catalog or Gateway views (registry only)

## Tech Stack

Single HTML file (`index.html`) with embedded CSS and JS. No build tools, no dependencies. Open in any browser via `file://`.

**Location:** `mcps/mcp-registry/prototype/index.html`

## Design System Reference

Based on MLflow 3.10 / Databricks DuBois design system:

### Colors
| Token | Hex | Usage |
|-------|-----|-------|
| Primary (actionPrimary) | `#2272B4` | Buttons, links, active states |
| Primary hover | `#0E538B` | Button hover |
| Text primary | `#11171C` | Body text, headings |
| Text secondary | `#5F7281` | Labels, metadata |
| Text placeholder | `#8396A5` | Hints, timestamps |
| Background primary | `#FFFFFF` | Content cards |
| Background secondary | `#F6F7F9` | Page background, sidebar |
| Border | `#D1D9E1` | Card borders |
| Border accessible | `#C0CDD8` | Input borders, button borders |
| Danger | `#C82D4C` | Delete actions |
| AI gradient start | `#4299E0` | Workflow switcher border |
| AI gradient mid | `#CA42E0` | Workflow switcher border (purple) |
| AI gradient end | `#FF5F46` | Workflow switcher border (coral) |

### Lifecycle State Badge Colors (DuBois Tag component)
| State | Tag Color | Background | Text |
|-------|-----------|------------|------|
| Draft | default | `rgba(0,0,59,0.05)` | `#11171C` |
| Published | lime | `rgba(2,179,2,0.08)` | `#203C25` |
| Deprecated | lemon | `rgba(255,191,1,0.18)` | `#4F3422` |
| Retired | charcoal | `#37444F` | `#E8ECF0` |

### Governance Badge Colors (3.next only)
| State | Tag Color | Background | Text |
|-------|-----------|------------|------|
| Approved | lime | `rgba(2,179,2,0.08)` | `#203C25` |
| Pending | lemon | `rgba(255,191,1,0.18)` | `#4F3422` |
| Rejected | coral | `rgba(240,0,64,0.06)` | `#64172B` |
| Certified | lime | `rgba(2,179,2,0.08)` | `#203C25` |
| Candidate | lemon | `rgba(255,191,1,0.18)` | `#4F3422` |
| Verified | lime (icon) | — | Green checkmark |
| Unverified | default (icon) | — | Grey empty circle |
| None | default | `rgba(0,0,59,0.05)` | `#11171C` |

### Typography
- Font: `-apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, 'Noto Sans', sans-serif`
- Base size: 13px / 20px line-height
- Headings: 22px/28px (page title), 14px/20px (section heading)
- Labels: 11px uppercase with 0.5px letter-spacing
- Weight: 400 normal, 600 bold

### Spacing & Sizing
- Base unit: 8px (xs=4, sm=8, md=16, lg=24)
- Button height: 32px
- Input height: 32px
- Border radius: 4px (buttons, inputs, badges), 6px (cards), 8px (content area)
- Sidebar width: 190px

### Shadows
- Content card: `0px 3px 6px 0px rgba(0,0,0,0.05)`
- Dropdown: `0px 2px 16px rgba(0,0,0,0.08)`
- Modal: `0px 8px 40px rgba(0,0,0,0.13)`

## App Shell Layout

### MLflow 3.10 Sidebar
- White background with right border (`#E8ECF0`)
- 190px width
- Top section:
  - MLflow logo + version "3.10"
  - Collapse/expand button
  - **GenAI / Model training workflow switcher** — pill toggle with AI gradient border (`linear-gradient(135deg, #4299E0 20.5%, #CA42E0 46.91%, #FF5F46 79.5%)`) when GenAI is selected
- Navigation (GenAI mode):
  - Home
  - Experiments
  - Prompts
  - AI Gateway
  - **MCP Servers** (new — highlighted when active)
- Bottom section:
  - Docs (external link)
  - Settings

### Content Area
- White card on `#F6F7F9` background
- 8px margin from sidebar
- 8px border-radius
- Subtle shadow

### Per-Page Header (no global header bar)
- Breadcrumb navigation
- Page title with icon
- Action buttons (Register, lifecycle dropdown, overflow menu)
- **3.5 DP / 3.next feature toggle** — positioned in the page header area

## Views

### 1. MCP Server List View

**Route:** `/mcp-servers`

**Header:**
- Breadcrumb: "MCP Servers"
- Title: "MCP Servers" with server icon
- "+ Register MCP Server" primary button
- Feature toggle (3.5 DP / 3.next)

**Filters:**
- Search input with search icon
- "All States" dropdown (Draft, Published, Deprecated)
- "All Sources" dropdown (Red Hat, Partner, Community)

**Table columns (3.5 DP):**
| Column | Sortable | Content |
|--------|----------|---------|
| Name | No | Brand-colored icon + clickable link |
| Latest Version | No | Version number (e.g., "v3") |
| Lifecycle State | No | DuBois Tag badge |
| Source | No | Red Hat / Partner / Community |
| Tools | No | Count of tools |
| Last Modified | Yes | Relative timestamp |
| Tags | No | Up to 2 tag badges |

**Additional columns (3.next):**
| Column | Content |
|--------|---------|
| Approval | Approved / Pending / Rejected badge |
| Verified | Green checkmark or grey circle icon |
| Certified | Certified / Candidate / None badge |
| Deployed | Green dot (active) or grey dot (not deployed) |

3.next columns are visually separated with purple header text and a left border divider.

**Table behavior:**
- Row hover: `rgba(68,83,95,0.04)` background
- Row click: navigates to detail view
- Pagination: 10/25/50/100 rows per page

### 2. MCP Server Detail View

**Route:** `/mcp-servers/:name`

**Header:**
- Breadcrumb: MCP Servers / [Server Name]
- Brand icon + title + subtitle ("Registered by [user] · Source: [type]")
- Lifecycle state dropdown (badge + chevron)
- Overflow menu (Delete)

**Collapsible sections (all expanded by default):**

1. **Metadata** — 3-column grid:
   - Created, Last Modified, Creator
   - Transport, Endpoint, Workspace

2. **Tags** — key:value tag list with "+ Add tag" button

3. **Description** — Markdown text with Edit button

4. **Tools** — 2-column grid of tool cards (code name + description), "Show all N tools" expand link

5. **Versions** — Segmented control (All / Active) + version table:
   | Column | Content |
   |--------|---------|
   | Status | Green/grey dot |
   | Version | Link to version detail |
   | Registered at | Timestamp |
   | Lifecycle State | Badge |
   | Created by | Username |
   | Description | Truncated text |

**3.next additions:**
- **Governance Status** section (below Metadata) — 4 cards in 2x2 grid:
  - Approval: status badge, approver, date, rationale
  - Verification: verified/unverified icon, source, date
  - Certification: status badge, certifier, date, expiry
  - Linked Policies: count badge, policy name links
- **Audit Trail** section — timeline with colored dots, state changes, actors, timestamps, comments

### 3. Version Detail View

**Route:** `/mcp-servers/:name/versions/:version`

**Header:**
- Breadcrumb: MCP Servers / [Server Name] / Version [N]
- Title: "Version [N]" + subtitle with server name
- Lifecycle state dropdown
- Overflow menu (Delete)

**Collapsible sections:**

1. **Metadata** — 3-column grid:
   - Registered At, Last Modified, Creator
   - Version (semver), Source Run (link), Lifecycle State (badge)

2. **Deployment** — Grey card showing:
   - Active/Inactive badge
   - Namespace, Endpoint URL, Gateway Registered (checkmark)

3. **Description** — Version-specific description with Edit button

4. **Tags** — Version-level key:value tags

5. **Server Definition** — Immutable server.json displayed as syntax-highlighted JSON in dark code block. Label: "server.json · immutable"

6. **Tools Schema** — Table with tool name (code badge), description, input schema (monospace). "Show all N tools" expand.

### 4. Register MCP Server Modal

**Trigger:** "+ Register MCP Server" button

**Modal (560px width):**
- Title: "Register MCP Server"
- Fields:
  - **Name** (required) — text input
  - **Description** — textarea
  - **Source Type** (required) — segmented control: Internal / External
  - **server.json** (required) — tabbed input: Paste JSON / Upload File / From URL. Paste shows syntax-highlighted dark code block.
  - **Tags** — inline tag chips with add input
- Info banner: "This will create Version 1 in Draft state. You can publish it after review."
- Footer: Cancel + Register buttons

### 5. Lifecycle State Transition

**Trigger:** Lifecycle dropdown on detail/version views

**Dropdown:**
- Header: "Transition to"
- Valid target states with icon + badge + description
- Only valid transitions shown:
  - Draft → Published
  - Published → Deprecated
  - Deprecated → Retired

**Confirmation modal (420px width):**
- Visual arrow: [Current State] → [Target State]
- Info alert explaining impact of the transition
- Optional comment textarea
- Checkbox: "Deprecate all other published versions of this MCP server"
- Context-sensitive button label (Publish / Deprecate / Retire)

## Sample Data

10 MCP servers from the RHOAI 3.4 catalog launch:

| Name | Source | Latest Version | Lifecycle | Tools | Tags |
|------|--------|---------------|-----------|-------|------|
| Red Hat OpenShift | Red Hat | v3 | Published | 12 | infrastructure, kubernetes |
| Red Hat Ansible Automation Platform | Red Hat | v2 | Published | 8 | automation, playbooks |
| Red Hat Insights | Red Hat | v1 | Published | 6 | observability, advisories |
| Confluent Kafka | Partner | v2 | Published | 9 | streaming, data |
| EnterpriseDB (PostgreSQL) | Partner | v1 | Published | 7 | database, sql |
| HashiCorp Terraform | Partner | v1 | Draft | 14 | iac, provisioning |
| Microsoft Azure | Partner | v2 | Published | 11 | cloud, azure |
| Dynatrace | Partner | v1 | Published | 10 | observability, apm |
| MongoDB | Community | v1 | Published | 5 | database, nosql |
| MariaDB | Community | v1 | Deprecated | 4 | database, relational |

Red Hat OpenShift serves as the "hero" entry with 3 versions (v1 Retired, v2 Deprecated, v3 Published) to demonstrate version history and lifecycle transitions.

## Feature Toggle Behavior

**3.5 DP (toggle OFF — default):**
- Base columns in list table (Name, Version, Lifecycle, Source, Tools, Last Modified, Tags)
- Base sections in detail views (Metadata, Tags, Description, Tools, Versions, Deployment, server.json, Tools Schema)
- Lifecycle states: Draft, Published, Deprecated, Retired

**3.next (toggle ON):**
- Additional columns in list table (Approval, Verified, Certified, Deployed) with purple headers and left border separator
- AI gradient banner at top of page indicating 3.next mode
- Governance Status section on detail views (Approval, Verification, Certification, Linked Policies)
- Audit Trail section on detail views
- "3.next" purple label on new section headers
- Additional lifecycle state: Candidate (between Draft and Published, requiring approval)
- "Request Approval" button on detail views

## Interactivity

The prototype is **simulated functional** with hardcoded data:

- **Navigation** — clicking sidebar items, table rows, breadcrumbs, and version links switches between views
- **Feature toggle** — switches between 3.5 DP and 3.next modes, showing/hiding governance features
- **Register form** — modal opens, fields are interactive, "Register" adds a pre-baked entry
- **Lifecycle transitions** — dropdown opens, selecting a state shows confirmation modal, confirming updates the badge
- **Search/filter** — search input filters the table, dropdowns filter by state and source
- **Collapsible sections** — clicking section headers toggles visibility
- **"Show all tools" expand** — reveals full tools list

No real data persistence — refreshing resets to initial state.

## File Structure

```
mcps/mcp-registry/prototype/
└── index.html          # Single-file prototype (HTML + CSS + JS)
```

All CSS embedded in `<style>` tags, all JS embedded in `<script>` tags, all data as JS objects. No external dependencies.
