# create-blog Skill Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a multi-agent blog creation and review skill that handles creating Red Hat blog posts from scratch and reviewing existing drafts, with four specialized sub-agent reviewers (architect, content, formatting, image) providing iterative feedback until quality thresholds are met.

**Architecture:** A main `skill.md` orchestrates five phases (Qualify, Abstract, Draft, Review Loop, Finalize). The skill progressively loads six reference files from a `references/` folder — qualifying questions, four reviewer rubrics, and scoring rules. Sub-agents are spawned in parallel during the review loop, each receiving only their specific rubric. Drafts are versioned (never overwritten) and organized by domain under `docs/blogs/`.

**Tech Stack:** Claude Code skills (markdown), Agent tool for sub-agent reviewers, Google Workspace MCP (doc fetching/creation), Playwright MCP (web research, blog scraping), file system tools (Read/Write/Glob/Grep)

---

## File Structure

```
.claude/skills/create-blog/
├── skill.md                          # Main workflow orchestration — phases, flow control, tool usage
└── references/
    ├── qualifying-questions.md       # Question framework, adaptive logic, shortcut path
    ├── reviewer-architect.md         # Structure & narrative rubric, scoring dimensions
    ├── reviewer-content.md           # Substance & voice rubric, scoring dimensions
    ├── reviewer-formatting.md        # Editorial compliance rubric, scoring dimensions
    ├── reviewer-image.md             # Visual communication rubric, scoring dimensions
    └── scoring.md                    # Score aggregation weights, pass criteria, iteration controls
```

Each reference file is loaded on-demand by the main skill — reviewers only see their own rubric file plus shared inputs.

---

### Task 1: Create the main skill file (skill.md)

**Files:**
- Create: `.claude/skills/create-blog/skill.md`

This is the orchestration hub. It defines the five-phase workflow, tells the model when to load each reference file, and handles all flow control (phase transitions, review loop iteration, human checkpoints, finalization).

- [ ] **Step 1: Create the skill directory**

```bash
mkdir -p .claude/skills/create-blog/references
```

- [ ] **Step 2: Write skill.md**

Write the following content to `.claude/skills/create-blog/skill.md`:

```markdown
---
name: create-blog
description: Create Red Hat blog posts or review existing drafts through a multi-agent pipeline with iterative quality improvement. Use this skill whenever the user wants to write a blog post, create blog content, draft a Red Hat blog, review a blog draft, improve a blog post, or says things like "write a blog about X", "I need a blog post", "review my blog draft", "help me with a blog", "create a developer blog", or "blog about [topic]". Also use when the user shares a Google Doc or draft and wants it turned into a polished Red Hat blog post.
---

# create-blog

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

### From existing content
User-provided draft or notes become v1 raw material. Copy the user's content as-is to `drafts/v1.md` — do not restructure yet (the review loop handles improvement).

### Image placeholders
Include image placeholders where visuals would aid understanding. Each placeholder follows this format:

```markdown
![Image Placeholder N: <short description>]

**Placement rationale**: [Why an image belongs here — what it helps the reader understand]

**Image generation prompt**: [Detailed prompt including:
- Exact content/composition to depict
- Red Hat brand colors (#EE0000, #A30000, #151515 dark / #F5F5F5 light)
- Clean, modern style — not overloaded, not clip art
- Specific aspect ratio (hero: 16:9, inline: 4:3, diagram: 16:9 wide)
- Visual hierarchy and key callouts]

**Alt text**: [Descriptive, accessible alt text]
```

Refer to `docs/blogs/blog-creation-guide.md` Brand Standards Quick Reference for the full color palette, typography, and illustration style guidance.

### Draft conventions
- Follow all formatting rules from `docs/blogs/blog-creation-guide.md`
- Sentence case headings, no H1 in body, cascading H2/H3/H4
- No backticks for code — use monospace indication
- No Oxford commas
- First person voice, Red Hat brand tone (open, authentic, helpful, brave)
- CTA near top (bolded, linked to Red Hat site) and in closing

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

   **Sub-agent prompt template** (adapt per reviewer):
   ```
   You are the [Architect/Content/Formatting/Image] reviewer for a Red Hat blog post.

   Review the draft against your rubric and the blog creation guide. Score each dimension 1-10, multiply by its weight, and provide specific line-level feedback with corrections.

   Inputs:
   - Draft: [read drafts/vN.md]
   - Abstract: [read abstract.md]
   - Blog creation guide: [read docs/blogs/blog-creation-guide.md]
   - Your rubric: [read references/reviewer-<type>.md]

   Output format: Write your review to drafts/reviews/vN-<type>.md following the format specified in your rubric.
   ```

2. **Collect all four reviews** from `drafts/reviews/vN-*.md`

3. **Aggregate scores** per `references/scoring.md`:
   - Architect: 30%, Content: 30%, Formatting: 20%, Image: 20%
   - Check pass criteria: overall >= 8.0, no dimension below 6.0
   - Update `drafts/reviews/score-summary.md`

4. **If passed**: Present to user for approval. Skip to Phase 5 on approval.

5. **If not passed**: Revise the draft:
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

5. **Update `drafts/reviews/score-summary.md`** with final status

6. **Offer Google Doc creation** (optional):
   - If yes: create via `mcp__google-workspace__create_doc` with submission form + draft content using `pedouble@redhat.com`
   - If no: skip, local artifacts are complete

7. **Update `docs/knowledge-registry.md`**:
   - Section 12 Blog Examples: add new entry with title, author, blog type, path, and description
   - Section 11: add any new stakeholders mentioned during the process

8. **Present completion summary**:

```markdown
## Blog Complete: [Title]

### Artifacts
- Abstract: [path]
- Final draft: [path]
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
```

- [ ] **Step 3: Verify the file was created correctly**

```bash
wc -l .claude/skills/create-blog/skill.md
head -5 .claude/skills/create-blog/skill.md
```

Expected: File exists, ~230-260 lines, starts with YAML frontmatter (`---`).

- [ ] **Step 4: Commit**

```bash
git add .claude/skills/create-blog/skill.md
git commit -m "feat(create-blog): add main skill orchestration file

Defines five-phase workflow (Qualify, Abstract, Draft, Review Loop,
Finalize) with progressive reference loading, sub-agent spawning,
and iteration controls."
```

---

### Task 2: Create qualifying-questions.md reference

**Files:**
- Create: `.claude/skills/create-blog/references/qualifying-questions.md`

This file contains the full question framework, adaptive logic, and shortcut path for Phase 1.

- [ ] **Step 1: Write qualifying-questions.md**

Write the following content to `.claude/skills/create-blog/references/qualifying-questions.md`:

```markdown
# Qualifying Questions Framework

This reference defines the question flow for Phase 1 (Qualify). Questions are asked one at a time, adapting based on prior answers. The goal is to gather enough context to write a strong abstract — no more, no less.

## Question Flow

Ask these questions conversationally, one per message. Adapt based on answers — skip questions that have already been answered by prior context or source material.

### Q1: Blog Type

**Ask**: "Is this for the Red Hat Blog or the Red Hat Developer Blog? Or not sure?"

| Answer | Next action |
|---|---|
| Red Hat Blog | Note: thought leadership tone, IT decision-maker audience |
| Developer Blog | Note: hands-on tutorial tone, practitioner audience |
| Not sure | Ask them to describe the content briefly; recommend based on: if it explains *why* → Red Hat Blog; if it shows *how* with code → Developer Blog |

### Q2: Core Thesis

**Ask**: "What single problem does this post solve for the reader? Try to say it in one sentence."

**Why this matters**: This becomes the first paragraph's anchor. If the user can't state it in one sentence, the blog probably covers too much ground — help them narrow it down or consider a series.

**If they struggle**: Offer framing prompts:
- "After reading this post, the reader will be able to..."
- "The problem this post addresses is..."
- "If I had to tweet the value of this post, I'd say..."

### Q3: Target Audience

**Ask**: "Who is the primary reader?" Offer choices:
- IT decision makers / architects
- Platform engineers / SREs
- Application developers
- Data scientists / ML engineers
- Security/compliance teams
- Other (describe)

**Adaptive follow-ups**:
- If Developer Blog: "What prerequisites should the reader have? What runtime environment will they need?"
- If Red Hat Blog: "What business context or strategic framing should we set up? Are there market trends or competitive pressures to reference?"

### Q4: Products and Projects

**Ask**: "Which Red Hat products or open source projects are involved?"

**Validation**: Cross-reference against the [Official Product Names List](https://docs.google.com/spreadsheets/d/1DLS_lS3VKidgZIvcLmLp9BoiqptkvqHWfe1D5FD2kfk/edit?gid=1987148185#gid=1987148185) and `docs/knowledge-registry.md`. Use official names only.

**Domain folder proposal**: Based on products/topics, propose a domain path:

| Products/Topics | Suggested Domain |
|---|---|
| MCP Gateway, MCP servers, tool calling | `mcps/` or `mcps/mcp-gateway-openclaw/` |
| MCP ingestion, partner onboarding | `mcps/ingestion-pipeline/` |
| MCP Catalog, discovery, search | `mcps/mcp-catalog/` |
| Agent registry, agent lifecycle | `agents/` |
| Skills, prompts, guardrails | `skills/` |
| Cross-cutting registry, governance, multiple asset types | `ai-assets/` |
| Strategy, vision, industry perspective | `cross-cutting/` |

Present the proposed path and topic-short name for user confirmation: "I'd suggest organizing this under `docs/blogs/mcps/mcp-gateway-openclaw/`. Does that work, or would you prefer a different path?"

### Q5: Source Material

**Ask**: "What source material do you have?"

| Answer | Action |
|---|---|
| "I have a draft/notes" | Ask for content or Google Doc link. Fetch via Google Workspace MCP or read from provided path. |
| "I have reference docs" | Ask for links/IDs. Fetch each. |
| "Start from scratch" | Note: will rely on knowledge registry + domain folder docs + web research. |

**In all cases**: Offer to scan related domain docs automatically: "I can also scan the `docs/` folder for related material on [topic]. Want me to do that?"

**For Google Doc links**: Extract the Doc ID from the URL and use `mcp__google-workspace__get_doc_as_markdown`.

### Q6: Demo or Code Component

**Ask**: "Does this post include a demo, code walkthrough, or hands-on component?"

| Answer | Follow-up |
|---|---|
| Yes | "What language/framework? What environment does the reader need? Any prerequisites to install?" |
| No | Skip — move to Q7 |

### Q7: Series Context

**Ask**: "Is this a standalone post or part of a series?"

| Answer | Follow-up |
|---|---|
| Part of a series | "Which part? Are there links to prior posts? What does each part cover?" |
| Standalone | Skip — move to Q8 |

### Q8: Call to Action

**Ask**: "What should the reader do after reading? What's the primary CTA?"

**Suggest options** based on products mentioned:
- Try [Product] (link to trial page)
- Explore the [GitHub repo]
- Read the [related doc/guide]
- Watch the [webinar/demo]
- Join the [community/Slack]

Remind: "The primary CTA should link to something on redhat.com — GitHub repos and upstream projects work great as secondary CTAs."

### Q9: Timing and Events

**Ask**: "Is this tied to a specific event, release, or date?"

| Answer | Follow-up |
|---|---|
| Yes | "Which event/release? What's the target publication date? Any embargo considerations?" |
| No | Skip — qualifying complete |

## Shortcut Path

When the user provides a Google Doc link or substantial content upfront:

1. **Fetch and analyze** the content
2. **Auto-fill** extractable answers:
   - Thesis: infer from intro/abstract
   - Products: extract product mentions
   - Audience: infer from tone and depth
   - Blog type: infer from format (tutorial vs thought leadership)
   - CTA: check for existing calls to action
   - Series: check for series references
3. **Present findings**: "Based on your doc, here's what I've gathered..."
4. **Only ask about gaps** — skip questions that have clear answers from the source

## Existing Draft Review Mode

When reviewing an existing draft (not creating from scratch):

1. Read the draft in full
2. Infer: blog type, thesis, audience, products, structure
3. Present inferences to user for confirmation
4. Ask only about: CTA (if not clear), series context (if not clear), domain path
5. Proceed with shortened qualifying summary

## Exit Condition

Present the qualifying summary for user confirmation:

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

User must confirm before proceeding to Phase 2 (Abstract).
```

- [ ] **Step 2: Verify the file**

```bash
wc -l .claude/skills/create-blog/references/qualifying-questions.md
head -3 .claude/skills/create-blog/references/qualifying-questions.md
```

Expected: File exists, ~120-140 lines, starts with `# Qualifying Questions Framework`.

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/create-blog/references/qualifying-questions.md
git commit -m "feat(create-blog): add qualifying questions reference

Adaptive question framework with 9 questions, shortcut path for
existing content, and existing draft review mode."
```

---

### Task 3: Create reviewer-architect.md reference

**Files:**
- Create: `.claude/skills/create-blog/references/reviewer-architect.md`

The Architect reviewer evaluates structure and narrative flow.

- [ ] **Step 1: Write reviewer-architect.md**

Write the following content to `.claude/skills/create-blog/references/reviewer-architect.md`:

```markdown
# Architect Reviewer — Structure & Narrative

You are the Architect reviewer. Your job is to evaluate whether the blog post has a sound structure and a compelling narrative arc. You care about the skeleton of the piece — does it hold together? Does each section earn its place? Does the reader know why they're reading by the end of paragraph one?

## Your Lens

You evaluate structure, not substance. You don't fact-check claims or verify code — that's Content's job. You don't check heading capitalization or link formatting — that's Formatting's job. You focus on:

- Does the post have a clear thesis that the reader encounters immediately?
- Do the sections flow logically, each building on the last?
- Is the depth calibrated to the blog type (strategic overview vs step-by-step tutorial)?
- Does the opening create enough tension or promise enough value to keep reading?
- Does the closing land — does it feel earned, and does the CTA follow naturally?

## Scoring Dimensions

Score each dimension 1-10. Multiply by the weight to get the weighted score.

| Dimension | Weight | What a 10 looks like | What a 4 looks like |
|---|---|---|---|
| **Thesis clarity** | 2x | Problem stated in paragraph 1. Reader knows "what's in it for me" within 3 sentences. Thesis is specific, not vague. | Thesis is buried in paragraph 3+, vague ("AI is changing things"), or absent entirely. Reader has to guess why they should keep reading. |
| **Section flow** | 2x | H2s form a logical progression. Each section builds on the last. Reader could reconstruct the argument from headers alone. | Sections feel random or repetitive. Major logical gaps between sections. Reader loses the thread. |
| **Depth calibration** | 1x | Depth matches blog type. Red Hat Blog: strategic, "why" focused, appropriate abstraction level. Developer Blog: step-by-step, concrete, runnable. | Red Hat Blog reads like a tutorial. Developer Blog reads like a press release. Audience mismatch. |
| **Opening hook** | 2x | First paragraph creates tension, names a cost, or identifies a gap the reader feels. Draws the reader in. | Opens with boilerplate, company history, or "In this blog post we will..." No hook. |
| **Closing strength** | 1x | Restates the value delivered. CTA follows naturally from the argument. Reader feels the post delivered on its promise. | Abrupt ending. CTA feels bolted on. No sense of completion. |
| **Series coherence** | 1x | (If series) Works standalone AND connects to prior/next posts. Reader can start here. (If standalone) N/A — score 8 by default. | Depends heavily on other posts to make sense. References "as we discussed in Part 1" without context. |

## Review Output Format

Write your review to `drafts/reviews/vN-architect.md` using this structure:

```markdown
# Architect Review — v[N]

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Thesis clarity | [score] | 2x | [score * 2] |
| Section flow | [score] | 2x | [score * 2] |
| Depth calibration | [score] | 1x | [score] |
| Opening hook | [score] | 2x | [score * 2] |
| Closing strength | [score] | 1x | [score] |
| Series coherence | [score] | 1x | [score] |
| **Total** | | | **[sum] / 90** → **[normalized 1-10]** |

## Line-Level Feedback

[For each issue, reference the specific section or paragraph. Provide the current text and a suggested revision where applicable.]

### [Dimension Name]
- **Location**: [Section heading or paragraph number]
- **Issue**: [What's wrong]
- **Suggestion**: [How to fix it]

## Summary

[Single paragraph: the ONE most important structural change that would have the biggest impact on the draft's quality. This is what the revision should prioritize.]
```

## Scoring Normalization

Your total possible weighted score is 90 (sum of all weights × 10). Normalize to 1-10:

`normalized_score = (weighted_total / 90) * 10`

## Inputs You Receive

- Current draft (`drafts/vN.md`)
- Abstract (`abstract.md`) — this is the contract; check if the draft fulfills it
- Blog creation guide (`docs/blogs/blog-creation-guide.md`) — structure and formatting expectations
- Qualifying summary (embedded in abstract) — blog type, audience, thesis

## What to Check Against the Abstract

The abstract defines what the blog promised to deliver. Flag any of these:
- Draft has sections not mentioned in the abstract's outline
- Abstract promised a key point that the draft doesn't cover
- Draft's thesis has drifted from the abstract's thesis
- Blog type/audience mismatch between abstract and draft tone
```

- [ ] **Step 2: Verify the file**

```bash
wc -l .claude/skills/create-blog/references/reviewer-architect.md
```

Expected: File exists, ~80-100 lines.

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/create-blog/references/reviewer-architect.md
git commit -m "feat(create-blog): add architect reviewer rubric

Structure & narrative reviewer with 6 weighted dimensions covering
thesis clarity, section flow, depth calibration, opening hook,
closing strength, and series coherence."
```

---

### Task 4: Create reviewer-content.md reference

**Files:**
- Create: `.claude/skills/create-blog/references/reviewer-content.md`

The Content reviewer evaluates substance and voice.

- [ ] **Step 1: Write reviewer-content.md**

Write the following content to `.claude/skills/create-blog/references/reviewer-content.md`:

```markdown
# Content Reviewer — Substance & Voice

You are the Content reviewer. Your job is to evaluate whether the blog post says something worth reading and says it in the right voice. You care about the meat of the piece — is it accurate? Is it original? Does it sound like Red Hat, or does it sound like a press release written by committee?

## Your Lens

You evaluate substance and voice, not structure or formatting. You don't reorganize sections — that's Architect's job. You don't check heading capitalization — that's Formatting's job. You focus on:

- Are the technical claims accurate and backed by evidence?
- Does the voice match Red Hat's brand (open, authentic, helpful, brave)?
- Is the content calibrated to the target audience's knowledge level?
- Does the post offer genuine insight, or is it a docs rewrite / marketing fluff?
- Are claims backed by examples, benchmarks, architecture details, or real scenarios?

## Scoring Dimensions

Score each dimension 1-10. Multiply by the weight to get the weighted score.

| Dimension | Weight | What a 10 looks like | What a 4 looks like |
|---|---|---|---|
| **Technical accuracy** | 2x | All claims correct and current. Product names match official list. Version numbers and capabilities are accurate. Cross-checked against knowledge registry. | Contains factual errors, outdated information, or claims about features that don't exist yet (or aren't at least in tech preview). |
| **Red Hat voice** | 2x | First person. Direct, opinionated, conversational. Sounds like a real engineer sharing what they learned. Admits tradeoffs honestly. | Passive voice. Corporate boilerplate. "We are pleased to announce..." Marketing buzzwords without substance. |
| **Audience alignment** | 1x | Language matches target reader's level. Developer Blog uses precise technical terms. Red Hat Blog explains concepts without condescending. | Developer Blog over-explains basics. Red Hat Blog uses jargon without context. Mismatched abstraction level. |
| **Originality** | 1x | Offers a perspective, framework, or insight the reader won't find in the docs. The author's experience and opinion are visible. | Reads like a reformatted docs page or feature announcement. No author voice. Nothing new. |
| **Evidence & examples** | 2x | Backed by benchmarks, architecture diagrams, real scenarios, code output, or performance data. "Show, don't tell." | Vague assertions ("it's faster", "it simplifies things") without data, examples, or proof. |
| **Product positioning** | 1x | Products mentioned naturally where relevant. Red Hat's role is clear without being forced. Open source projects credited. | Every paragraph is a product pitch. Or: products not mentioned at all when they should be. Forced marketing language. |

## Fact-Checking Against Knowledge Registry

When reviewing, cross-reference claims against `docs/knowledge-registry.md`:
- Product capabilities mentioned should match what's documented
- Stakeholder names and roles should be accurate
- Project status (decided vs proposed) should be represented correctly
- Don't present proposals as shipped features

## Voice Reference

From the blog creation guide, Red Hat's brand voice is: **open, authentic, helpful, and brave**.

| Do | Don't |
|---|---|
| Write in first person ("I", "we") | Use passive voice ("it was determined") |
| Be direct and opinionated | Hedge everything with "might" and "could" |
| Use concrete language | Use vague marketing language |
| Admit tradeoffs and limitations honestly | Claim your solution is perfect |
| Address the reader as "you" | Use "one" or "users" abstractly |

**No Oxford commas** — Red Hat style omits the serial comma.

## Review Output Format

Write your review to `drafts/reviews/vN-content.md` using this structure:

```markdown
# Content Review — v[N]

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Technical accuracy | [score] | 2x | [score * 2] |
| Red Hat voice | [score] | 2x | [score * 2] |
| Audience alignment | [score] | 1x | [score] |
| Originality | [score] | 1x | [score] |
| Evidence & examples | [score] | 2x | [score * 2] |
| Product positioning | [score] | 1x | [score] |
| **Total** | | | **[sum] / 90** → **[normalized 1-10]** |

## Line-Level Feedback

[For each issue, reference the specific section or paragraph. Quote the problematic text and provide a correction.]

### [Dimension Name]
- **Location**: [Section heading or quote from draft]
- **Issue**: [What's wrong]
- **Current**: "[quoted text]"
- **Suggested**: "[revised text]"

## Accuracy Flags

[List any factual claims that need verification. Include the claim, where it appears, and what you checked it against.]

## Summary

[Single paragraph: the ONE most important content change that would have the biggest impact. This is what the revision should prioritize.]
```

## Scoring Normalization

Your total possible weighted score is 90 (sum of all weights × 10). Normalize to 1-10:

`normalized_score = (weighted_total / 90) * 10`

## Inputs You Receive

- Current draft (`drafts/vN.md`)
- Abstract (`abstract.md`) — the contract for what the blog should cover
- Blog creation guide (`docs/blogs/blog-creation-guide.md`) — voice, tone, and writing patterns
- Knowledge registry (`docs/knowledge-registry.md`) — fact-checking reference
- Qualifying summary (embedded in abstract) — audience, products, thesis
```

- [ ] **Step 2: Verify the file**

```bash
wc -l .claude/skills/create-blog/references/reviewer-content.md
```

Expected: File exists, ~100-120 lines.

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/create-blog/references/reviewer-content.md
git commit -m "feat(create-blog): add content reviewer rubric

Substance & voice reviewer with 6 weighted dimensions covering
technical accuracy, Red Hat voice, audience alignment, originality,
evidence, and product positioning."
```

---

### Task 5: Create reviewer-formatting.md reference

**Files:**
- Create: `.claude/skills/create-blog/references/reviewer-formatting.md`

The Formatting reviewer evaluates editorial compliance.

- [ ] **Step 1: Write reviewer-formatting.md**

Write the following content to `.claude/skills/create-blog/references/reviewer-formatting.md`:

```markdown
# Formatting Reviewer — Editorial Compliance

You are the Formatting reviewer. Your job is to evaluate whether the blog post meets Red Hat's editorial standards and is ready for the editorial team to process without friction. You are the last line of defense before submission — every formatting issue you catch saves a round-trip with the editorial team.

## Your Lens

You evaluate formatting, editorial compliance, and SEO readiness. You don't evaluate whether the argument is sound — that's Architect's job. You don't fact-check — that's Content's job. You focus on:

- Do headings follow Red Hat conventions (sentence case, cascading H2/H3/H4, no H1)?
- Is code formatted correctly (monospace, no backticks)?
- Are CTAs properly placed and linked?
- Are SEO basics covered (keywords in title, meta-ready)?
- Are product names official? Are links correct?
- Does the typography and any visual/HTML reference follow brand standards?

## Scoring Dimensions

Score each dimension 1-10. Multiply by the weight to get the weighted score.

| Dimension | Weight | What a 10 looks like | What a 4 looks like |
|---|---|---|---|
| **Heading hierarchy** | 1x | All sentence case. Clean H2 → H3 → H4 cascade. No H1 in body. No skipped levels. | Title case headings. H1 used in body. Jumps from H2 to H4. Inconsistent casing. |
| **Code formatting** | 1x | Code in monospace. No backticks (editorial team has to remove them). Code is real and runnable. Terminal output separated from commands. | Backticks used for inline code. Code blocks not marked as monospace. Pseudocode instead of real code. |
| **CTA placement** | 2x | Primary CTA near top, bolded, linked to redhat.com content. Secondary CTA mid-article. Closing CTA in conclusion. All links working. | No CTA, or CTA only at the very end. Primary CTA links to external site instead of Red Hat. CTAs not bolded or linked. |
| **SEO readiness** | 1x | Target keyword in title and first paragraph. Title is 50-60 chars. Meta description could be extracted from intro. Custom URL slug is obvious. | No clear keyword strategy. Title is 100+ chars. Opening paragraph doesn't contain the topic keyword. |
| **Link strategy** | 1x | Red Hat product pages linked on first mention. Internal links (redhat.com) present. GitHub/upstream repos linked where relevant. All links tested. | No internal links. Product names mentioned but never linked. Broken links. |
| **Editorial compliance** | 2x | No Oxford commas. Official product names used throughout. No jargon or hyperbole. No animated GIFs. 10 or fewer images, all with alt text. | Oxford commas present. Unofficial product names (e.g., "RHOAI" instead of "Red Hat OpenShift AI"). Jargon unexplained. |
| **Brand standards** | 1x | Typography references use Red Hat font families (Display/Text/Mono). Any color references use official palette. Visual/HTML elements align with brand guidelines from the blog creation guide's Brand Standards Quick Reference. | Non-brand fonts specified. Colors don't match palette. Visual elements clash with brand guidelines. |
| **Word count** | 1x | Appropriate for blog type. Under 2,000 words for single post. If over, flag for series split with a concrete proposal for how to divide it. | Drastically over/under for the topic. 3,000-word single post that should be a series. 200-word post trying to cover a deep topic. |

## Editorial Rules Quick Reference

These are the rules most commonly violated. Check every one:

1. **Sentence case headings**: Only capitalize the first word and proper nouns. "Getting started with MCP Gateway" not "Getting Started with MCP Gateway"
2. **No Oxford commas**: "identity, policy and governance" not "identity, policy, and governance"
3. **No backticks**: Use monospace font indication instead. The editorial team has to manually remove backticks.
4. **Official product names**: Always use the full official name on first mention (e.g., "Red Hat OpenShift AI" not "RHOAI" or "OpenShift AI"). Abbreviated forms acceptable after first mention if the abbreviation is official.
5. **No H1 in body**: H1 is reserved for the page title (set in Drupal). Body starts at H2.
6. **Alt text on all images**: Required for accessibility. For Developer blog, also add captions.
7. **No animated GIFs**: Accessibility requirement.
8. **Features in tech preview or GA only**: Don't write about features that haven't reached at least tech preview state.

## Brand Standards Reference

Check any visual or typographic references against the Brand Standards Quick Reference in `docs/blogs/blog-creation-guide.md`:

- **Color palette**: Primary Red Hat Red (#EE0000), full neutral family, extended color families for data visualization
- **Typography**: Red Hat Display (headings), Red Hat Text (body), Red Hat Mono (code)
- **Illustration style**: Clean, modern, not overloaded — follow illustration guidelines

## Review Output Format

Write your review to `drafts/reviews/vN-formatting.md` using this structure:

```markdown
# Formatting Review — v[N]

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Heading hierarchy | [score] | 1x | [score] |
| Code formatting | [score] | 1x | [score] |
| CTA placement | [score] | 2x | [score * 2] |
| SEO readiness | [score] | 1x | [score] |
| Link strategy | [score] | 1x | [score] |
| Editorial compliance | [score] | 2x | [score * 2] |
| Brand standards | [score] | 1x | [score] |
| Word count | [score] | 1x | [score] |
| **Total** | | | **[sum] / 100** → **[normalized 1-10]** |

## Line-Level Feedback

[For each issue, quote the exact text and provide the correction.]

### [Dimension Name]
- **Location**: [Line/section reference]
- **Current**: "[exact text from draft]"
- **Corrected**: "[fixed text]"
- **Rule**: [Which editorial rule this violates]

## Editorial Compliance Checklist

- [ ] All headings in sentence case
- [ ] No Oxford commas
- [ ] No backticks (monospace indicated correctly)
- [ ] Official product names on first mention
- [ ] No H1 in body
- [ ] All images have alt text
- [ ] No animated GIFs
- [ ] Features at tech preview+ only
- [ ] Word count appropriate ([actual count] words)

## Summary

[Single paragraph: the most impactful formatting fix needed. Often this is a systematic issue like "all headings use title case" rather than a one-off.]
```

## Scoring Normalization

Your total possible weighted score is 100 (sum of all weights × 10). Normalize to 1-10:

`normalized_score = (weighted_total / 100) * 10`

## Inputs You Receive

- Current draft (`drafts/vN.md`)
- Abstract (`abstract.md`) — for context on blog type and audience
- Blog creation guide (`docs/blogs/blog-creation-guide.md`) — editorial rules, formatting conventions, brand standards
- Qualifying summary (embedded in abstract) — blog type determines some formatting expectations
```

- [ ] **Step 2: Verify the file**

```bash
wc -l .claude/skills/create-blog/references/reviewer-formatting.md
```

Expected: File exists, ~120-140 lines.

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/create-blog/references/reviewer-formatting.md
git commit -m "feat(create-blog): add formatting reviewer rubric

Editorial compliance reviewer with 8 weighted dimensions covering
heading hierarchy, code formatting, CTAs, SEO, links, editorial
rules, brand standards, and word count."
```

---

### Task 6: Create reviewer-image.md reference

**Files:**
- Create: `.claude/skills/create-blog/references/reviewer-image.md`

The Image reviewer evaluates visual communication and image placeholder quality.

- [ ] **Step 1: Write reviewer-image.md**

Write the following content to `.claude/skills/create-blog/references/reviewer-image.md`:

```markdown
# Image Reviewer — Visual Communication

You are the Image reviewer. Your job is to evaluate whether the blog post uses visuals effectively and whether the image placeholders are well-crafted enough to produce useful images. You care about visual communication — does each image earn its place? Are the generation prompts specific enough to produce the right image on the first try?

## Your Lens

You evaluate image placement, prompt quality, and brand compliance for visuals. You don't evaluate the text content — that's Content's job. You don't check heading formatting — that's Formatting's job. You focus on:

- Does each image placement serve a purpose (aids comprehension, not decoration)?
- Are the generation prompts detailed enough to produce the right image?
- Do the prompts reference Red Hat brand standards correctly?
- Are aspect ratios specified and appropriate for the placement context?
- Is the alt text descriptive and accessible?

## Scoring Dimensions

Score each dimension 1-10. Multiply by the weight to get the weighted score.

| Dimension | Weight | What a 10 looks like | What a 4 looks like |
|---|---|---|---|
| **Placement rationale** | 2x | Every image aids comprehension. Each placement has a clear "this helps the reader understand X" rationale. No decorative filler. Architecture diagrams where systems interact. Flow diagrams where processes are described. | Images placed at random intervals. Rationale is generic ("adds visual interest"). Decorative images that don't aid understanding. |
| **Prompt specificity** | 2x | Prompts are detailed enough to generate the correct image on the first try. Include exact content to depict, composition, visual hierarchy, key callouts, and style direction. | Prompts are vague ("show the architecture"). Missing critical details about what to include. Would produce a generic image that doesn't match the content. |
| **Brand compliance** | 2x | Prompts reference the official Red Hat color palette — not just #EE0000 but the full palette from the Brand Standards Quick Reference (primary reds, neutrals, extended families for data visualization). Typography references use Red Hat Display/Text/Mono. Illustration style follows brand guidelines (clean, modern, not overloaded). | Only mentions "red" without hex codes. Uses non-brand colors. Specifies non-Red Hat fonts. Style clashes with brand (clip art, overly complex, unprofessional). |
| **Aspect ratio & sizing** | 1x | Every placeholder specifies the correct aspect ratio for its context. Hero images: 16:9. Inline images: 4:3. Wide diagrams: 16:9. Ratios are consistent and practical. | No aspect ratios specified. Ratios don't match context (square hero image, portrait-mode diagram). |
| **Alt text quality** | 1x | Alt text is descriptive and accessible. Conveys the image's purpose, not just its visual content. A screen reader user would understand why the image is there. | Alt text is generic ("diagram"), missing, or just repeats the caption. Doesn't convey purpose. |
| **Image count** | 1x | 10 or fewer images total. Each image earns its place. Appropriate density for the post length and type. | More than 10 images. Or: zero images in a post that clearly needs visual aids (architecture walkthrough, multi-step tutorial). |

## Brand Standards Reference

Check all image prompts against the Brand Standards Quick Reference in `docs/blogs/blog-creation-guide.md`:

### Color Palette (must be referenced in prompts)
- **Primary**: Red Hat Red #EE0000
- **Dark reds**: #A60000, #5F0000, #3F0000
- **Light reds**: #F56E6E, #F9A8A8, #FBC5C5, #FCE3E3, #FEF0F0
- **Neutrals**: #151515 (near-black), #383838, #6A6E73, #F0F0F0, #FFFFFF
- **Extended families** (for diagrams/data viz): Blue (#0066CC interactive), Teal (#147878), Purple (#3D2785), Green (#3D7317), Orange (#F0561D), Yellow (#DCA614)

### Typography (for any text in images)
- Headings: Red Hat Display
- Body text: Red Hat Text
- Code/technical: Red Hat Mono

### Style Guidelines
- Clean, modern, professional
- Not overloaded — each element earns its place
- Not clip art — polished and on-brand
- Consistent visual hierarchy
- Reference the Illustration and Photography sub-pages at redhat.com/en/about/brand/standards for detailed style guidance

## Image Placeholder Format Reference

Each placeholder in the draft should follow this structure:

```markdown
![Image Placeholder N: <short description>]

**Placement rationale**: [Why an image belongs here]

**Image generation prompt**: [Detailed prompt with:
- Exact content/composition
- Red Hat brand colors (specific hex codes from palette)
- Style direction (clean, modern, per brand guidelines)
- Aspect ratio (hero: 16:9, inline: 4:3, diagram: 16:9 wide)
- Visual hierarchy and key callouts]

**Alt text**: [Descriptive, accessible alt text]
```

Flag any placeholders that are missing fields or have incomplete prompts.

## When to Recommend create-diagram Skill

If a placement clearly needs a technical architecture diagram, system flow diagram, or component interaction diagram rather than a generated image, flag it:

> **Recommendation**: This placement would be better served by a technical diagram. Consider using the `create-diagram` skill for this image, which can produce accurate architecture diagrams with proper Red Hat branding.

## Review Output Format

Write your review to `drafts/reviews/vN-image.md` using this structure:

```markdown
# Image Review — v[N]

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Placement rationale | [score] | 2x | [score * 2] |
| Prompt specificity | [score] | 2x | [score * 2] |
| Brand compliance | [score] | 2x | [score * 2] |
| Aspect ratio & sizing | [score] | 1x | [score] |
| Alt text quality | [score] | 1x | [score] |
| Image count | [score] | 1x | [score] |
| **Total** | | | **[sum] / 90** → **[normalized 1-10]** |

## Per-Image Feedback

### Image Placeholder [N]: [description]
- **Placement**: [Appropriate / Unnecessary / Missing from a key section]
- **Prompt quality**: [Specific enough / Needs more detail on X]
- **Brand compliance**: [Compliant / Missing: X]
- **Aspect ratio**: [Correct / Should be X instead of Y]
- **Alt text**: [Good / Needs improvement: X]
- **Recommendation**: [Keep / Revise prompt / Remove / Replace with create-diagram]

## Missing Image Opportunities

[List any sections that would benefit from a visual but don't have one. Include a suggested placement rationale.]

## Summary

[Single paragraph: the most impactful improvement for the image placeholders. Often this is about prompt specificity or brand compliance across all images.]
```

## Scoring Normalization

Your total possible weighted score is 90 (sum of all weights × 10). Normalize to 1-10:

`normalized_score = (weighted_total / 90) * 10`

## Inputs You Receive

- Current draft (`drafts/vN.md`) — contains image placeholders
- Abstract (`abstract.md`) — for understanding what visuals the post needs
- Blog creation guide (`docs/blogs/blog-creation-guide.md`) — image rules, brand standards
- Qualifying summary (embedded in abstract) — blog type, products (informs what visuals are needed)
```

- [ ] **Step 2: Verify the file**

```bash
wc -l .claude/skills/create-blog/references/reviewer-image.md
```

Expected: File exists, ~130-150 lines.

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/create-blog/references/reviewer-image.md
git commit -m "feat(create-blog): add image reviewer rubric

Visual communication reviewer with 6 weighted dimensions covering
placement rationale, prompt specificity, brand compliance, aspect
ratios, alt text, and image count."
```

---

### Task 7: Create scoring.md reference

**Files:**
- Create: `.claude/skills/create-blog/references/scoring.md`

This file defines score aggregation, pass criteria, and iteration controls.

- [ ] **Step 1: Write scoring.md**

Write the following content to `.claude/skills/create-blog/references/scoring.md`:

```markdown
# Scoring & Iteration Rules

This reference defines how individual reviewer scores are aggregated, what constitutes a passing draft, and how the review iteration loop is controlled.

## Score Aggregation

Each reviewer produces a normalized score (1-10) from their weighted dimension scores. These are aggregated with the following weights:

| Reviewer | Weight | Rationale |
|---|---|---|
| **Architect** | 30% | Structure is the skeleton — a poorly structured post can't be saved by good content |
| **Content** | 30% | Substance is why the reader stays — accuracy and voice are equally critical |
| **Formatting** | 20% | Important but more fixable — the editorial team can catch some of these |
| **Image** | 20% | Enhances but doesn't make or break — images can be improved post-review |

**Overall score formula**:

```
overall = (architect * 0.30) + (content * 0.30) + (formatting * 0.20) + (image * 0.20)
```

## Pass Criteria

A draft passes when ALL of the following are true:

1. **Overall weighted average >= 8.0**
2. **No individual dimension (across ANY reviewer) scores below 6.0**

Both conditions must be met. A draft with an overall 8.5 but one dimension at 5.0 does not pass.

### Near-Miss Rule

If the overall score is >= 7.5 AND only ONE dimension across all reviewers is between 5.0 and 5.9:

- Flag as **"conditional pass"** at the next human checkpoint
- Present the specific dimension that's below floor
- User decides: accept as-is, or iterate to fix that one dimension
- This prevents infinite loops over a single stubborn dimension that may be a matter of taste

## Iteration Controls

### Autonomous Iterations

- **Maximum 3 autonomous iterations** before a mandatory human checkpoint
- During autonomous iterations, the main agent revises the draft based on reviewer feedback without asking the user
- Each iteration produces a new versioned draft (`v1` → `v2` → `v3`)

### Human Checkpoints

At each checkpoint (after iterations 3, 6, and 9), present the user with:

1. Current scores (per-agent and overall)
2. Score trend (improving, plateauing, or declining)
3. The specific dimensions that are preventing a pass
4. Four options:

| Option | Effect |
|---|---|
| **Continue** | 3 more autonomous iterations |
| **Steer** | User provides specific guidance, then 3 more autonomous iterations |
| **Accept** | Override the threshold — proceed to finalize with current draft |
| **Abandon** | Stop the process. Keep all drafts and reviews on disk. |

### Hard Ceiling

- **9 total iterations** (3 checkpoints) is the absolute maximum
- After 9 iterations, present the best-scoring version and ask the user to Accept or Abandon
- Rationale: diminishing returns after 9 iterations. If the draft hasn't passed by then, the problem is likely in the requirements (abstract), not the execution

### Early Exit

- If a draft passes (overall >= 8.0, all dimensions >= 6.0) BEFORE reaching a checkpoint, present it to the user immediately
- Don't wait for the checkpoint — passing early is the happy path
- User can still request another iteration even after a pass

## Revision Strategy

When revising between iterations, follow this priority order:

1. **Fix blockers first**: Any dimension below 6.0 gets addressed before anything else. These are hard failures.
2. **Biggest impact next**: Address the lowest-scoring dimension across all four reviewers. This typically moves the overall score the most.
3. **Resolve conflicts**: If reviewers give conflicting feedback (e.g., Architect says "add more detail" while Content says "too verbose"), use the blog type as tiebreaker:
   - Developer Blog → favor the more detailed/technical suggestion
   - Red Hat Blog → favor the more strategic/concise suggestion
4. **Quick wins last**: Editorial compliance fixes (heading case, Oxford commas, product names) are easy and reliable. Apply them in every iteration.
5. **Include changelog**: Add a brief changelog at the top of each new draft version listing what changed and why. This changelog is stripped during finalization.

### Changelog Format

At the top of each draft v2+:

```markdown
<!-- CHANGELOG — will be removed during finalization
v[N] changes:
- [Dimension]: [What changed and why]
- [Dimension]: [What changed and why]
-->
```

## Score Summary File

After every review cycle, update `drafts/reviews/score-summary.md`:

```markdown
# Score Summary: [Blog Topic]

## Current Status: [IN PROGRESS / PASSED / CONDITIONAL PASS / ACCEPTED / ABANDONED]

| Version | Architect | Content | Formatting | Image | Overall | Status |
|---|---|---|---|---|---|---|
| v1 | [score] | [score] | [score] | [score] | [overall] | [status] |
| v2 | [score] | [score] | [score] | [score] | [overall] | [status] |
| ... | | | | | | |

## Dimensions Below Floor (current version)
[List any dimensions below 6.0 across all reviewers, with the reviewer name and dimension name]

## Trend
[Per-agent trend: improving ↑, stable →, declining ↓]
[Note if any agent's score has plateaued for 2+ iterations]

## Checkpoint History
[Record each human checkpoint decision: Continue/Steer/Accept/Abandon + any steering guidance provided]
```

## Example Score Calculation

Given these reviewer normalized scores:
- Architect: 7.8
- Content: 8.2
- Formatting: 8.5
- Image: 7.0

Overall = (7.8 × 0.30) + (8.2 × 0.30) + (8.5 × 0.20) + (7.0 × 0.20)
        = 2.34 + 2.46 + 1.70 + 1.40
        = **7.90**

Result: **Below threshold** (7.90 < 8.0). Check individual dimensions — if all are >= 6.0, this is close and likely passes on the next iteration. If any dimension is below 6.0, that's the priority fix.
```

- [ ] **Step 2: Verify the file**

```bash
wc -l .claude/skills/create-blog/references/scoring.md
```

Expected: File exists, ~130-150 lines.

- [ ] **Step 3: Commit**

```bash
git add .claude/skills/create-blog/references/scoring.md
git commit -m "feat(create-blog): add scoring and iteration rules

Score aggregation (30/30/20/20 weights), pass criteria (8.0 overall,
6.0 floor), near-miss rule, iteration controls (3 autonomous, 9 max),
revision strategy, and score summary format."
```

---

### Task 8: Final verification and integration commit

**Files:**
- Verify: All 7 files in `.claude/skills/create-blog/`
- Verify: Cross-references between files are consistent

- [ ] **Step 1: Verify all files exist**

```bash
find .claude/skills/create-blog -type f | sort
```

Expected output:
```
.claude/skills/create-blog/references/qualifying-questions.md
.claude/skills/create-blog/references/reviewer-architect.md
.claude/skills/create-blog/references/reviewer-content.md
.claude/skills/create-blog/references/reviewer-formatting.md
.claude/skills/create-blog/references/reviewer-image.md
.claude/skills/create-blog/references/scoring.md
.claude/skills/create-blog/skill.md
```

- [ ] **Step 2: Verify cross-references**

Check that `skill.md` references all 6 files in `references/`:

```bash
grep -c "references/" .claude/skills/create-blog/skill.md
```

Expected: At least 6 matches (one per reference file).

Check that each reviewer references the correct output path pattern:

```bash
grep "vN-architect" .claude/skills/create-blog/references/reviewer-architect.md
grep "vN-content" .claude/skills/create-blog/references/reviewer-content.md
grep "vN-formatting" .claude/skills/create-blog/references/reviewer-formatting.md
grep "vN-image" .claude/skills/create-blog/references/reviewer-image.md
```

Expected: Each file references its own output filename pattern.

- [ ] **Step 3: Verify scoring normalization totals**

Check that each reviewer's max weighted total matches their normalization denominator:

| Reviewer | Dimensions | Max Weighted Total | Denominator in File |
|---|---|---|---|
| Architect | 2+2+1+2+1+1 = 9 weights × 10 | 90 | 90 |
| Content | 2+2+1+1+2+1 = 9 weights × 10 | 90 | 90 |
| Formatting | 1+1+2+1+1+2+1+1 = 10 weights × 10 | 100 | 100 |
| Image | 2+2+2+1+1+1 = 9 weights × 10 | 90 | 90 |

```bash
grep "/ 90" .claude/skills/create-blog/references/reviewer-architect.md
grep "/ 90" .claude/skills/create-blog/references/reviewer-content.md
grep "/ 100" .claude/skills/create-blog/references/reviewer-formatting.md
grep "/ 90" .claude/skills/create-blog/references/reviewer-image.md
```

Expected: Each file uses the correct denominator.

- [ ] **Step 4: Verify spec coverage**

Cross-reference against `docs/superpowers/specs/2026-04-14-create-blog-skill-design.md`:

| Spec Requirement | Covered In |
|---|---|
| Five phases (Qualify, Abstract, Draft, Review, Finalize) | `skill.md` |
| 9 qualifying questions with adaptive logic | `qualifying-questions.md` |
| Shortcut path for existing content | `qualifying-questions.md` |
| Existing draft review mode | `qualifying-questions.md` + `skill.md` |
| Domain folder auto-detection | `qualifying-questions.md` + `skill.md` |
| Abstract as written contract | `skill.md` |
| Image placeholders with prompts | `skill.md` |
| 4 reviewers (architect, content, formatting, image) | `reviewer-*.md` (4 files) |
| Rubric-based scoring per reviewer | Each `reviewer-*.md` |
| Score aggregation (30/30/20/20) | `scoring.md` |
| Pass criteria (8.0 overall, 6.0 floor) | `scoring.md` |
| Near-miss rule (7.5 overall, one dim 5.0-5.9) | `scoring.md` |
| 3 autonomous + checkpoint + 9 max | `scoring.md` |
| Revision strategy (blockers first) | `scoring.md` |
| Score summary file format | `scoring.md` |
| SEO file generation | `skill.md` Phase 5 |
| Google Doc creation (optional) | `skill.md` Phase 5 |
| Knowledge registry update | `skill.md` Phase 5 |
| Completion summary | `skill.md` Phase 5 |
| Blog creation guide as shared input | All reviewer files + `skill.md` |
| Brand standards checking | `reviewer-formatting.md` + `reviewer-image.md` |
| Versioned drafts (never overwrite) | `skill.md` + `scoring.md` |
| Versioned reviews | All `reviewer-*.md` output formats |
| create-diagram skill recommendation | `reviewer-image.md` |
| Playwright for JS-rendered blogs | `skill.md` tool reference |
| Google Workspace MCP integration | `skill.md` tool reference |

All spec requirements covered.

- [ ] **Step 5: Run the skill to test triggering**

Test that the skill description triggers correctly by verifying the frontmatter:

```bash
head -4 .claude/skills/create-blog/skill.md
```

Expected: YAML frontmatter with `name: create-blog` and a comprehensive `description` field containing trigger phrases.
