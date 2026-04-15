---
name: blog-create
description: Create Red Hat blog posts or review existing drafts through a multi-agent pipeline with iterative quality improvement. Use this skill whenever the user wants to write a blog post, create blog content, draft a Red Hat blog, review a blog draft, improve a blog post, or says things like "write a blog about X", "I need a blog post", "review my blog draft", "help me with a blog", "create a developer blog", or "blog about [topic]". Also use when the user shares a Google Doc or draft and wants it turned into a polished Red Hat blog post.
---

# blog-create

A multi-agent blog creation and review skill for Red Hat blog posts (redhat.com/blog and developers.redhat.com/blog). Handles two modes — creating blogs from scratch and reviewing existing drafts — through a unified pipeline with iterative quality improvement via four specialized sub-agent reviewers.

## Why This Skill Exists

Writing a strong Red Hat blog post requires balancing technical depth, editorial compliance, brand voice, visual communication, and narrative structure. No single pass catches everything. This skill uses four independent reviewers — each with a focused lens — to iteratively improve drafts until they meet a quality bar. The human stays in the loop at defined checkpoints, and all artifacts are versioned so nothing is lost.

## Before You Start

Load these two documents into context — they ground the entire workflow:

1. **`docs/knowledge-registry.md`** — Current project state, stakeholders, decisions, open questions
2. **`docs/blogs/blog-creation-guide.md`** — Red Hat editorial process, writing patterns, formatting rules, brand standards, examples

These are living documents. Read them fresh every time — don't rely on cached knowledge.

## Workflow Overview

```
Phase 1: Qualify → Phase 2: Abstract → Phase 3: Draft → Phase 4: Review Loop → Phase 5: Finalize
```

Each phase has a clear exit condition. Do not advance to the next phase until the current phase's exit condition is met.

## Phase 1: Qualify

**Purpose**: Gather requirements through conversational questions to establish the blog's goal, audience, structure, and source material.

**How**: Read `references/qualifying-questions.md` for the full question framework and adaptive logic.

**Inputs to gather**:
- Blog type (Red Hat Blog vs Developer Blog)
- Core thesis (what single problem does this post solve?)
- Target audience
- Products/projects involved
- Source material (drafts, docs, links, or start from scratch)
- Demo/code component (yes/no)
- Series context (standalone or part of series)
- CTA target
- Timing/event tie-in

**Shortcut path**: If the user provides a Google Doc link or substantial notes upfront, fetch and analyze the content, auto-fill extractable answers, present findings, and only ask about gaps.

**Existing draft review mode**: If the user provides an existing draft to review (not create from scratch), use the shortened qualifying flow — read the draft, infer blog type/audience/thesis, confirm with user, and only ask about gaps.

**Domain folder**: Based on products/topics discussed, determine the domain folder path under `docs/blogs/`. Present to user for confirmation:

```
docs/blogs/<domain>/<topic-short>/
```

Domain options: `mcps/`, `agents/`, `skills/`, `ai-assets/`, `cross-cutting/`, or nested paths like `mcps/ingestion-pipeline/`, `mcps/mcp-catalog/`.

**Exit condition**: User confirms the qualifying summary.

Output format for qualifying summary:

```markdown
## Blog Qualifying Summary

- **Blog type**: [Red Hat Blog / Red Hat Developer Blog]
- **Thesis**: [one sentence]
- **Audience**: [target readers]
- **Products**: [list]
- **Domain path**: docs/blogs/[domain]/[topic-short]/
- **Source material**: [list of sources]
- **Demo**: [Yes/No — details if yes]
- **Series**: [Standalone / Part N of series name]
- **CTA**: [target action]
- **Timing**: [event/release or none]
```

## Phase 2: Abstract

**Purpose**: Create a written contract for what the blog will contain, ensuring alignment before drafting begins.

Create the output directory structure:

```bash
# Create the blog folder and subfolders
mkdir -p docs/blogs/<domain>/<topic-short>/drafts/reviews
```

Write `docs/blogs/<domain>/<topic-short>/abstract.md` containing:

- Thesis statement
- Target audience
- Blog type
- Key points (3 maximum)
- Products/projects involved
- CTA
- Source materials referenced
- Proposed section outline (H2 headings that tell the story)
- Series context (if applicable)
- Qualifying summary (embedded at the bottom)

**For existing draft review mode**: Generate the abstract FROM the existing draft rather than before it. The abstract describes what the draft contains and what it should become.

**Exit condition**: User approves the abstract.

## Phase 3: Draft

**Purpose**: Produce the first version of the blog post.

### From scratch
Generate v1 based on:
- The approved abstract
- Source materials (fetched via Google Workspace MCP or Playwright MCP)
- `docs/knowledge-registry.md` for project grounding
- Fresh web research (use Playwright for Red Hat blogs — they are JS-rendered and WebFetch cannot parse them)

### Target word count
Reference the word count guidelines from `docs/blogs/blog-creation-guide.md` and aim for the appropriate range based on blog type and topic depth. Typical ranges: 500-600 words for announcements, 800-1300 for tutorials, 1300-2000 for deep dives. If the topic needs more than 2000 words, propose a series split during qualifying.

### From existing content
User-provided draft or notes become v1 raw material. Copy the user's content as-is to `drafts/v1.md` — do not restructure yet (the review loop handles improvement). Note: the first review cycle will likely produce low formatting scores because the raw content hasn't been adapted to Red Hat editorial conventions yet. This is expected — the review loop is designed to improve iteratively.

### Image placeholders
Include image placeholders where visuals would aid understanding. Each placeholder follows this format:

```markdown
--------------------
**[Image Placeholder N: <short description>]**

**Placement rationale**: [Why an image belongs here, what it helps the reader understand]

**Image generation prompt**: [Detailed prompt including:
- Exact content/composition to depict
- Red Hat brand colors (#EE0000, #A30000, #151515 dark / #F0F0F0 light)
- Clean, modern style, not overloaded, not clip art
- Specific aspect ratio (hero: 16:9, inline: 4:3, diagram: 16:9 wide)
- Visual hierarchy and key callouts]

**Alt text**: [Descriptive, accessible alt text]

--------------------
```

The `--------------------` separators before and after make image placeholders visually distinct from body text, ensuring reviewers and editors can locate them easily during review.

Refer to `docs/blogs/blog-creation-guide.md` Brand Standards Quick Reference for the full color palette, typography, and illustration style guidance.

### Draft conventions
- Follow all formatting rules from `docs/blogs/blog-creation-guide.md`
- Sentence case headings, no H1 in body, cascading H2/H3/H4
- No backticks for code, use monospace indication
- No Oxford commas
- First person voice, Red Hat brand tone (open, authentic, helpful, brave)
- CTA near top (bolded, linked to Red Hat site) and in closing

#### AI writing avoidance

LLM-generated prose has recognizable patterns that erode reader trust. The draft must read as if a human wrote it. Specific rules:

- **No em dashes.** The em dash character (—) is a telltale sign of AI-generated text. Use commas, periods, colons, semicolons, or restructure the sentence instead. Every single em dash must be eliminated.
- **No marketing tropes.** Avoid "That changes today", "We are pleased to announce", "In today's fast-paced world", "game-changer", "at the end of the day". These phrases signal AI or committee-written content.
- **No filler transitions.** Avoid "Moreover", "Furthermore", "Additionally", "It's worth noting that", "It goes without saying". Use direct connections or start a new thought.
- **Vary sentence structure.** AI text tends toward parallel constructions (three bullet points of identical rhythm, two-sentence paragraphs repeating the same pattern). Break the symmetry.
- **Be specific over vague.** Replace "it simplifies things" with what specifically gets simpler and how. Replace "it's faster" with the actual mechanism.

#### Competitive references

- **Never link to competitors.** If competitors need to be mentioned for positioning context (e.g., naming alternative catalogs), use their names as plain text without hyperlinks. Linking gives them SEO credit and sends readers away.
- **Do not name unconfirmed future capabilities.** When referencing roadmap items, use generic descriptions ("additional AI asset types") rather than specific names ("agents, skills, guardrails") unless officially announced.

#### Precision

- **Don't aggregate counts when tiers tell the story.** If there are three Red Hat servers, five partner servers, and two community servers, say that, not "10 servers". The tier structure is the message.
- **Be precise about automation vs manual steps.** Especially for Developer Preview features, do not imply seamless end-to-end automation if there are manual steps. Describe what each component actually does ("the Operator deploys it on your cluster... From there, the Gateway handles runtime connectivity") rather than conflating them into a single magic action.

**Exit condition**: `drafts/v1.md` written to disk.

## Phase 4: Review Loop

**Purpose**: Iteratively improve the draft through parallel sub-agent review until quality threshold is met.

### How the review loop works

Read `references/scoring.md` for full scoring rules, pass criteria, and iteration controls.

For each iteration:

1. **Spawn four reviewer sub-agents in parallel** using the Agent tool. Each reviewer receives:
   - Current draft (`drafts/vN.md`)
   - Abstract (`abstract.md`)
   - Blog creation guide (`docs/blogs/blog-creation-guide.md`)
   - Their specific rubric (one of `references/reviewer-architect.md`, `references/reviewer-content.md`, `references/reviewer-formatting.md`, `references/reviewer-image.md`)
   - The qualifying summary (embedded in abstract)
   - **Content reviewer only**: also receives `docs/knowledge-registry.md` for fact-checking

   **Sub-agent prompt template** (adapt per reviewer — replace all `<PLACEHOLDERS>`):

   ```
   You are the [Architect/Content/Formatting/Image] reviewer for a Red Hat blog post.

   Review the draft against your rubric and the blog creation guide. Score each dimension 1-10, multiply by its weight, and provide specific line-level feedback with corrections.

   Read these files:
   - Draft: docs/blogs/<domain>/<topic-short>/drafts/v<N>.md
   - Abstract: docs/blogs/<domain>/<topic-short>/abstract.md
   - Blog creation guide: docs/blogs/blog-creation-guide.md
   - Your rubric: .claude/skills/blog-create/references/reviewer-<type>.md
   [Content reviewer only: - Knowledge registry: docs/knowledge-registry.md]

   Write your review to: docs/blogs/<domain>/<topic-short>/drafts/reviews/v<N>-<type>.md

   Follow the output format specified in your rubric exactly.
   ```

2. **Collect all four reviews** from `drafts/reviews/vN-*.md`

3. **Aggregate scores** per `references/scoring.md`:
   - Architect: 30%, Content: 30%, Formatting: 20%, Image: 20%
   - Check pass criteria: overall >= 8.0, no dimension below 6.0
   - Update `drafts/reviews/score-summary.md`

4. **If passed**: Present to user for approval. Skip to Phase 5 on approval.

5. **If not passed**: Revise the draft:
   - First, read all four review files from the current iteration (`drafts/reviews/vN-architect.md`, `vN-content.md`, `vN-formatting.md`, `vN-image.md`) to understand the full picture
   - Fix any dimension below 6.0 first (blockers)
   - Address lowest-scoring dimension across all agents
   - Resolve conflicting feedback using blog type as tiebreaker
   - Apply quick wins (editorial compliance fixes)
   - Include brief changelog at top of new draft version
   - Write revised draft as `drafts/v(N+1).md` — never overwrite previous versions

6. **Repeat** until pass or checkpoint.

### Iteration controls

- **Max 3 autonomous iterations** before mandatory human checkpoint
- **At checkpoint, user chooses**:
  - **Continue**: 3 more autonomous iterations
  - **Steer**: Provide guidance, then 3 more iterations
  - **Accept**: Override threshold, proceed to finalize
  - **Abandon**: Keep all drafts and reviews, stop
- **Hard ceiling**: 9 iterations (3 checkpoints)
- **Early exit**: If draft passes before checkpoint, present immediately for user approval

### Near-miss rule

If overall >= 7.5 and only one dimension is between 5.0-5.9, flag as "conditional pass" at human checkpoint. User decides whether to accept or iterate.

## Phase 5: Finalize

**Purpose**: Produce submission-ready artifacts and close the loop.

### Sequence

1. **Strip internal changelog** from the passing draft version

2. **Pre-fill blog submission form** template at top of draft:
   - Publication type, byline (defaults to Peter Double — overridable during qualifying), reviewers needed, image checklist, pre-submission checklist

3. **Write `final.md`**: Submission form + clean draft in `docs/blogs/<domain>/<topic-short>/final.md`

4. **Generate `seo.md`** in `docs/blogs/<domain>/<topic-short>/seo.md`:
   - Meta title (50-60 chars, keywords front-loaded)
   - Meta description (150-160 chars, action-oriented)
   - Primary and secondary keywords
   - Suggested URL slug
   - Internal link suggestions

5. **Generate `blog-preview.html`**: Create a branded HTML preview of the blog post.
   - Read the template from `assets/blog-template.html`
   - Read the conversion guide from `references/html-preview-guide.md`
   - Extract metadata from `final.md` (title, subtitle, author, date, product label)
   - Convert the markdown body to HTML following the guide's conversion rules
   - Replace all `{{PLACEHOLDER}}` tokens in the template
   - Render structured image placeholders as actual HTML diagrams when the content describes a table or comparison; otherwise render as placeholder cards
   - Write to `docs/blogs/<domain>/<topic-short>/blog-preview.html`

6. **Update `drafts/reviews/score-summary.md`** with final status

7. **Offer Google Doc creation** (optional):
   - If yes: create via `mcp__google-workspace__create_doc` with submission form + draft content using `pedouble@redhat.com`
   - If no: skip, local artifacts are complete

8. **Update `docs/knowledge-registry.md`**:
   - Section 12 Blog Examples: add new entry with title, author, blog type, path, and description
   - Section 11: add any new stakeholders mentioned during the process

9. **Present completion summary**:

```markdown
## Blog Complete: [Title]

### Artifacts
- Abstract: [path]
- Final draft: [path]
- HTML preview: [path]
- SEO metadata: [path]
- Iterations: [N] drafts, [N*4] reviews
- Google Doc: [link] (if created)

### Final Scores
| Agent | Score |
|---|---|
| Architect | [score] |
| Content | [score] |
| Formatting | [score] |
| Image | [score] |
| **Overall** | **[score]** |

### Image Placeholders ([N] total)
[list with descriptions — these need generation/sourcing]

### Next Steps
1. Generate/source images using prompts in the draft
2. Copy to Google Docs (if not already done)
3. Get SME/peer reviews from: [names]
4. Submit through Workfront request form
5. Prepare Employee Advocacy post for promotion
```

## Tool Usage Reference

| Task | Tool |
|---|---|
| Fetch Google Docs | `mcp__google-workspace__get_doc_as_markdown` (user: pedouble@redhat.com) |
| Fetch web pages / Red Hat blogs | Playwright (`browser_navigate` + `browser_run_code`) — Red Hat blogs are JS-rendered |
| General web research | Playwright or WebSearch |
| Create Google Doc | `mcp__google-workspace__create_doc` (user: pedouble@redhat.com) |
| Spawn reviewer sub-agents | Agent tool with subagent_type unset (general-purpose) |
| Read/write files | Read, Write, Edit tools |
| Search project files | Glob, Grep tools |

## Important Reminders

- **Never overwrite drafts** — always create a new version file (v1, v2, v3...)
- **Reviews are also versioned** — `vN-architect.md`, `vN-content.md`, etc.
- **Read the knowledge registry fresh** every session — it's a living document
- **Use Playwright, not WebFetch** for Red Hat blog URLs — they require JavaScript rendering
- **Brand standards matter** — reference the Brand Standards Quick Reference in the blog creation guide for colors, typography, and illustration style
- **The abstract is a contract** — if the draft diverges significantly from the abstract, flag it before continuing
