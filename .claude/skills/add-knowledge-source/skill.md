---
name: add-knowledge-source
description: Add a new knowledge source (Google Doc, PDF, URL, blog post, meeting transcript, repo) to the AI Asset Registry project. Use this skill whenever the user wants to incorporate a new document, reference, or source material into the project's knowledge base — including when they say "add this doc", "incorporate this source", "register this reference", "I have a new document", "add this blog", or share a Google Doc/PDF/repo/blog link for project use. Also use when updating the knowledge registry with new information from any source, including Red Hat blog posts, Red Hat Developer blog articles, or external technical blog content.
---

# Add Knowledge Source

This skill governs how new source documents are incorporated into the AI Asset Registry project. The goal is structured, consistent incorporation that properly categorizes, records, and cross-references new information — so the knowledge base stays coherent as the project grows.

## Why This Matters

The AI Asset Registry project synthesizes information from many sources (Google Docs, PDFs, meeting transcripts, GitHub repos, Jira tickets, blog posts). Each source has a different status (decided vs. proposed vs. exploratory), affects different parts of the project (MCP, agents, skills, cross-cutting), and needs to be discoverable by future readers. Sloppy incorporation leads to contradictions, stale references, and lost context. This process prevents that.

## Process Overview

```
Fetch/Read Source -> Analyze Content -> Classify -> Update Knowledge Registry -> Update Component Docs -> Report Impact
```

## Step 1: Fetch and Read the Source

Determine the source type and fetch its content:

| Source Type | How to Fetch |
|---|---|
| **Google Doc** | Use `mcp__google-workspace__get_doc_as_markdown` with the document ID. Include comments (they often contain important reviewer feedback). |
| **PDF** | Read from `docs/starting-artifacts/` or the provided path. |
| **Meeting transcript** | Read from `docs/starting-artifacts/meeting-transcriptions/` or provided path. |
| **GitHub repo** | Use `gh` CLI or browse the repo. Note the repo URL and key files. |
| **URL/webpage** | Use `WebFetch` to retrieve content. If WebFetch returns only metadata (common with JS-rendered sites like redhat.com), use Playwright (`mcp__plugin_playwright_playwright__browser_navigate` + `browser_run_code`) to load the page and extract the article body text. |
| **Blog post** | Use Playwright to navigate and extract the full article text (Red Hat blogs use client-side rendering that WebFetch cannot parse). Extract: title, author, date, full body text, section structure, code blocks, CTAs. For Red Hat Developer blogs, also note captions and alt text patterns. |
| **Jira ticket** | Use `mcp__plugin_atlassian_atlassian__getJiraIssue` with the issue key. |

If a local copy should be saved (PDFs, transcripts), place it in the appropriate `docs/starting-artifacts/` subdirectory:
- `mcp-artifacts/` — MCP-specific documents
- `agentic-artifacts/` — Agentic strategy documents
- `meeting-transcriptions/` — Meeting notes and transcripts
- Root `starting-artifacts/` — Cross-cutting or general documents

## Step 2: Analyze and Classify

Read the full document and extract:

### 2a. Document Metadata
- **Title**: Official title of the document
- **Source type**: Google Doc, PDF, transcript, repo, URL, Jira
- **Document ID/URL**: For future reference
- **Author(s)**: If identifiable
- **Date**: When written or last updated
- **Commenters/Reviewers**: Names and roles of people who commented (these may need to be added to the Stakeholders section)

### 2b. Document Status
Classify the document's authority level — this determines how information is presented in the knowledge registry:

| Status | Meaning | How to Present |
|---|---|---|
| **Decided** | Approved direction, shipped capability, or established fact | State directly as current truth |
| **Proposal** | Suggested approach, brain dump, design doc under review | Prefix with "Proposed" or "from [Doc Name] — not finalized". Use language like "could", "would", "suggests" |
| **Exploratory** | Early thinking, brainstorm, options analysis | Note as exploratory. Don't integrate into main sections — reference only |
| **Reference** | External standard, upstream doc, competitor analysis | Cite as external reference with attribution |
| **Blog Example** | Published Red Hat blog or developer blog post | Cite as a published example with author, date, URL. Note the blog type (Red Hat blog vs Developer blog), writing patterns, and how it relates to AI Asset Registry topics |

This distinction is critical. A proposal should never be written into the knowledge registry as though it were decided. Future readers depend on being able to distinguish "what is" from "what could be."

### 2c. Affected Areas
Determine which parts of the project this document touches:

| Domain | Folder | Example Topics |
|---|---|---|
| **MCP Registry** | `mcps/`, `docs/components/mcp-registry.md` | MCP data models, lifecycle, gateway integration |
| **Agents** | `agents/`, `docs/assets/asset-types.md` | Agent registry, agent identity, agent lifecycle |
| **Skills** | `skills/`, `docs/assets/asset-types.md` | Skills packaging, skills registry |
| **AI Assets (cross-cutting)** | `ai-assets/`, `docs/architecture/` | Registry framework, plugin model, shared concepts |
| **Platform** | `docs/integration/` | SKU, deployment, OCP integration |
| **Strategy** | `docs/strategy/` | Agentic strategy, competitive landscape |
| **Security/Governance** | `docs/security/` | RBAC, policy, trust, audit |
| **Blog Examples** | `docs/blogs/` | Published Red Hat blog posts used as writing examples, formatting references, or content strategy guides |

A document may affect multiple areas. Identify the primary area and any secondary areas.

### 2d. Key Information to Extract
- **New concepts or definitions** not already in the knowledge registry
- **Decisions or proposals** that confirm, contradict, or extend existing documented positions
- **People mentioned** who aren't in the stakeholders list
- **Open questions** raised by the document or its comments
- **Reviewer comments** that surface unresolved debates or important considerations
- **Jira ticket references** mentioned in the document or comments
- **Contradictions** with existing documented information (flag these explicitly)

### 2e. Blog-Specific Extraction (when source is a blog post)
When the source is a blog post, also extract:
- **Blog type**: Red Hat blog (redhat.com/en/blog) vs Red Hat Developer blog (developers.redhat.com) — these have different audiences, tones, and formatting conventions
- **Author and role**: Name, title, and team (for the stakeholders list and as a writing example reference)
- **Structure patterns**: How the post uses headings, introductions, code blocks, diagrams, CTAs
- **Tone and voice**: Technical depth level, use of first person, conversational vs formal
- **Call to action (CTA)**: What the post directs readers to do next
- **Content marketing elements**: How the post balances technical value with Red Hat product positioning
- **SEO patterns**: Keywords in title, meta description approach, internal linking strategy
- **Series context**: Whether it's part of a series and how posts interlink
- **What makes it effective**: Why this blog was selected as an example — specific writing techniques worth emulating

## Step 3: Update docs/knowledge-registry.md

The knowledge registry is the central reference. Updates should touch the relevant sections:

### Section 3 (Key Components) or Section 2 (Asset Types)
- Add new component details or asset type information
- For proposals: add under the relevant component with a clear "(Proposed)" marker and source attribution
- For decided items: update the existing description

### Section 4 (Lifecycle & Flows)
- If the document defines or modifies lifecycle states, flows, or processes
- For proposals that differ from current documented flows: show both the current and proposed versions, clearly labeled

### Section 6 (Platform Integration)
- Timeline changes, SKU updates, deployment changes

### Section 7 (Upstream & Databricks Collaboration)
- Upstream work updates, new collaboration details

### Section 8 (Security & Governance)
- Governance model changes, security requirements

### Section 9 (Competitive Landscape)
- New competitors or differentiation updates

### Section 11 (Key People & Stakeholders)
- Add any new people mentioned in the document or comments who aren't already listed
- Include their role/area

### Section 12 (Source Document Index)
This is mandatory for every new source. Add the document to the appropriate table:

**For Google Documents:**
```markdown
| [Title] | [Doc ID] | [Status marker]: [Brief description of key content] |
```
Use status markers: **PROPOSAL**, **DECIDED**, **EXPLORATORY**, **REFERENCE**

**For Local Documents:**
```markdown
| [Title] | docs/starting-artifacts/[path] | [Brief description of key content] |
```

**For GitHub Repositories:**
```markdown
| [repo-org/repo-name] | [Purpose description] |
```

**For Blog Posts (add to "Blog Examples" subsection — create if it doesn't exist):**
```markdown
| [Title] | [Author] | [Blog Type] | [URL] | [Why it's a good example] |
```
Blog Types: **Red Hat Blog**, **Red Hat Developer Blog**

### Section 13 (Open Questions & Risks)
- Add new open questions from the document
- Add new risks identified
- Add key comment threads to the "Key Comment Threads" subsection with source attribution

## Step 4: Update Domain-Specific Docs

Based on the affected areas identified in Step 2c, update the relevant docs:

- **docs/components/*.md** — Component-specific details (data models, APIs, integration points)
- **docs/assets/asset-types.md** — New asset type information
- **docs/architecture/*.md** — Architectural concepts, system boundaries
- **docs/flows/*.md** — Lifecycle or flow changes
- **docs/security/*.md** — Governance or security changes

When updating component docs:
- Add a new section for the proposed/new information
- Include a source link to the original document
- Mark proposals clearly with a blockquote attribution: `> Source: [Doc Title](URL) — this is a proposal, not an approved design.`
- Add the source to the component doc's own "Source Documents" section if it has one

## Step 5: Report Impact

After all updates are made, output a structured impact report:

```markdown
## Knowledge Source Incorporation Report

### Document
- **Title**: [title]
- **Type**: [Google Doc / PDF / Transcript / Repo / URL]
- **Status**: [Decided / Proposal / Exploratory / Reference]
- **Source**: [URL or path]

### Files Updated
- `docs/knowledge-registry.md` — Sections: [list sections updated]
- `docs/components/[file].md` — [what was added]
- [other files]

### Key Information Added
- [Bullet list of the most important new information incorporated]

### Affected Areas
- **Primary**: [domain]
- **Secondary**: [domains, if any]

### New Stakeholders Added
- [Name — Role] (if any)

### New Open Questions
- [List any new open questions added]

### Contradictions or Tensions
- [Any conflicts with existing documented information, if found]

### Cross-Cutting Implications
- [How this might affect other domains beyond the primary one]
```

## Handling Edge Cases

### Document contradicts existing information
Don't silently overwrite. Present both positions in the knowledge registry with clear attribution. Add a note to Section 13 (Open Questions) flagging the contradiction for resolution. Example: "Current documentation states X (from [Source A]). [Source B] proposes Y instead. Resolution needed."

### Document is very large (100+ pages)
Focus on what's new or changed relative to existing knowledge. Don't re-document information that's already captured. Summarize key additions only.

### Document covers multiple domains
Update all affected domain docs, but keep each update focused on the relevant subset. Don't dump the entire document's content into every file.

### Document is a meeting transcript
Extract decisions, action items, and notable quotes. Attribute statements to speakers. Meeting context (who was present, what was discussed) goes in the knowledge registry; action items may warrant Jira references.

### Source is a GitHub repository
Record the repo URL, purpose, and key files/CRDs/APIs in Section 12 and `docs/components/repositories.md`. Don't try to summarize the entire repo — focus on what's relevant to the registry project.

### Source is a blog post
Blog posts serve dual purposes: they contain technical information relevant to the project AND they serve as writing examples for future blog creation. When incorporating a blog post:
1. Extract any technical content relevant to the AI Asset Registry and incorporate it into the appropriate knowledge registry sections (same as any other source)
2. Add the blog to the Section 12 "Blog Examples" table with author, blog type, URL, and a note on why it's a good example
3. If the blog demonstrates notable writing patterns (strong structure, effective CTAs, good technical depth), note these in `docs/blogs/` as reference material
4. Add any new people mentioned to the Stakeholders section (Section 11) if they're relevant to the project

### Source is a blog process/editorial document
Documents about how to write blogs (editorial handbooks, style guides, submission processes) are reference material for the blog creation guide. Incorporate key process steps and writing guidelines into `docs/blogs/blog-creation-guide.md` rather than the knowledge registry. Add the source to Section 12 as a **REFERENCE** document.
