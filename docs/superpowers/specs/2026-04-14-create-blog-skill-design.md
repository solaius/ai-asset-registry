# Design Spec: create-blog Skill

**Date**: 2026-04-14
**Author**: Peter Double / Claude
**Status**: Approved

---

## Overview

A multi-agent blog creation and review skill for Red Hat blog posts (redhat.com/blog and developers.redhat.com/blog). The skill handles two modes — creating blogs from scratch and reviewing existing drafts — through a unified pipeline with iterative quality improvement via four specialized sub-agent reviewers.

## Skill Structure

```
.claude/skills/create-blog/
├── skill.md                     # Workflow orchestration (~300 lines)
└── references/
    ├── qualifying-questions.md   # Question framework and adaptive logic
    ├── reviewer-architect.md     # Architect sub-agent prompt + rubric
    ├── reviewer-content.md       # Content sub-agent prompt + rubric
    ├── reviewer-formatting.md    # Formatting sub-agent prompt + rubric
    ├── reviewer-image.md         # Image sub-agent prompt + rubric
    └── scoring.md                # Score aggregation, thresholds, pass criteria
```

## Output Folder Structure

Blogs are organized under `docs/blogs/` by domain, mirroring the repo's domain workspaces:

```
docs/blogs/
├── blog-creation-guide.md              # Style reference (already exists)
├── mcps/
│   ├── mcp-gateway-openclaw/
│   │   ├── abstract.md
│   │   ├── final.md
│   │   ├── seo.md
│   │   └── drafts/
│   │       ├── v1.md
│   │       ├── v2.md
│   │       ├── v3.md
│   │       └── reviews/
│   │           ├── v1-architect.md
│   │           ├── v1-content.md
│   │           ├── v1-formatting.md
│   │           ├── v1-image.md
│   │           ├── v2-architect.md
│   │           ├── ...
│   │           └── score-summary.md
│   ├── ingestion-pipeline/
│   │   └── partner-mcp-onboarding/
│   │       └── ...
│   └── mcp-catalog/
│       └── ...
├── agents/
│   └── ...
├── skills/
│   └── ...
├── ai-assets/
│   └── ...
└── cross-cutting/
    └── ...
```

Domain folder is determined automatically during qualifying questions (based on products/topics discussed) and presented for user confirmation before proceeding.

---

## Phase 1: Qualify

### Purpose
Gather requirements through conversational questions to establish the blog's goal, audience, structure, and source material.

### Inputs
- `docs/knowledge-registry.md` — grounding in current project state
- `docs/blogs/blog-creation-guide.md` — style reference context
- User-provided source material (Google Docs, URLs, notes, transcripts)
- Relevant domain folder docs (auto-scanned)
- Web research for latest information where relevant

### Question Flow

Questions are asked one at a time, adapting based on prior answers:

1. **Blog type**: Red Hat Blog / Red Hat Developer Blog / Not sure
   - If "not sure": describe content, skill recommends based on audience and format
2. **Core thesis**: "What single problem does this post solve for the reader?"
3. **Target audience**: Multiple choice (IT decision makers, platform engineers, developers, data scientists, etc.)
   - Developer Blog path: ask about prerequisites, runtime environment
   - Red Hat Blog path: ask about business context, strategic framing
4. **Products/projects involved**: Free-form, skill validates against official product names
   - Skill proposes domain folder path, user confirms or overrides
5. **Source material**:
   - "I have a draft/notes" — user provides content or link
   - "I have reference docs" — user provides links, skill fetches
   - "Start from scratch" — skill scans domain folder + knowledge registry
   - In all cases: skill offers to scan related domain docs automatically
6. **Demo/code component?**
   - Yes: ask about code language, environment, prerequisites
   - No: skip
7. **Series context**:
   - Part of a series: which part? Links to prior posts?
   - Standalone: skip
8. **CTA target**: "What should the reader do after reading?"
   - Skill suggests options based on products mentioned
9. **Timing/event tie-in?**
   - Yes: which event/release? Target publication date?
   - No: skip

### Shortcut Path

If user provides a Google Doc link or substantial notes upfront:
1. Fetch and analyze content
2. Auto-fill extractable answers (thesis, products, audience, blog type)
3. Present findings: "Based on your doc, here's what I've gathered..."
4. Only ask about gaps

### Exit Condition

User confirms the qualifying summary:

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

---

## Phase 2: Abstract

### Purpose
Create a written contract for what the blog will contain, ensuring alignment before drafting begins.

### Output: `abstract.md`

Contains:
- Thesis statement
- Target audience
- Blog type
- Key points (3 maximum)
- Products/projects involved
- CTA
- Source materials referenced
- Proposed section outline
- Series context (if applicable)
- Qualifying summary (embedded)

### Exit Condition

User approves the abstract.

---

## Phase 3: Draft

### Purpose
Produce the first version of the blog post.

### Two Entry Paths

**From scratch**: Generate v1 based on abstract, source materials, knowledge registry, and fresh web research.

**From existing content**: User-provided draft or notes become v1 raw material. Skill restructures and enhances per the abstract.

### Image Placeholders

The draft includes image placeholders where visuals would aid understanding. Each placeholder contains:

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

### Exit Condition

`drafts/v1.md` written to disk.

---

## Phase 4: Review Loop

### Purpose
Iteratively improve the draft through parallel sub-agent review until quality threshold is met.

### Sub-Agent Reviewers

Four reviewers spawned in parallel, each with a focused evaluation lens:

#### Architect (Structure & Narrative)
| Dimension | Weight | Focus |
|---|---|---|
| Thesis clarity | 2x | Problem stated in paragraph 1, reader knows "what's in it for me" |
| Section flow | 2x | H2s form logical progression, each builds on the last |
| Depth calibration | 1x | Matches blog type (strategic vs step-by-step) |
| Opening hook | 2x | First paragraph creates tension or names a cost |
| Closing strength | 1x | Restates value, delivers CTA naturally |
| Series coherence | 1x | Works standalone AND connects (if series) |

#### Content (Substance & Voice)
| Dimension | Weight | Focus |
|---|---|---|
| Technical accuracy | 2x | Claims correct, backed by current state, cross-checked against knowledge registry |
| Red Hat voice | 2x | Open, authentic, helpful, brave. First person. Not corporate boilerplate |
| Audience alignment | 1x | Language matches target reader's level |
| Originality | 1x | Offers perspective/insight, not a docs rewrite |
| Evidence & examples | 2x | Backed by benchmarks, architecture details, real scenarios |
| Product positioning | 1x | Products mentioned naturally, not forced marketing |

#### Formatting (Editorial Compliance)
| Dimension | Weight | Focus |
|---|---|---|
| Heading hierarchy | 1x | Sentence case, H2/H3/H4 cascade, no H1 in body |
| Code formatting | 1x | Monospace indicated, no backticks, runnable and correct |
| CTA placement | 2x | Primary near top (bolded, linked to Red Hat site), secondary mid-article |
| SEO readiness | 1x | Keywords in title and first paragraph, appropriate title length |
| Link strategy | 1x | Product pages linked on first mention, internal links present |
| Editorial compliance | 2x | No Oxford commas, official product names, no jargon/hyperbole |
| Word count | 1x | Appropriate for type, flags for series split if over 2000 |

#### Image (Visual Communication)
| Dimension | Weight | Focus |
|---|---|---|
| Placement rationale | 2x | Each image aids comprehension, no decorative filler |
| Prompt specificity | 2x | Detailed enough to generate correct image on first try |
| Brand compliance | 2x | Red Hat palette, clean modern style, not overloaded |
| Aspect ratio & sizing | 1x | Ratios specified per placement context |
| Alt text quality | 1x | Descriptive, accessible, conveys purpose |
| Image count | 1x | 10 or fewer, each earns its place |

### Shared Inputs (All Reviewers)

- Current draft (`drafts/vN.md`)
- Abstract (`abstract.md`)
- Blog creation guide (`docs/blogs/blog-creation-guide.md`)
- Qualifying summary (embedded in abstract)
- Their specific rubric from `references/reviewer-*.md`

### Review Output Format

Each reviewer produces a versioned review file (`drafts/reviews/vN-<agent>.md`) containing:
- Score per dimension (1-10 multiplied by weight)
- Specific line-level feedback with corrections
- Single paragraph summary of the most important change needed

### Score Aggregation

Per-agent scores normalized to 1-10, then weighted:

| Agent | Weight | Rationale |
|---|---|---|
| Architect | 30% | Structure is the skeleton |
| Content | 30% | Substance is why the reader stays |
| Formatting | 20% | Important but fixable by editorial team |
| Image | 20% | Enhances but doesn't make or break |

### Pass Criteria

- Overall weighted average >= 8.0
- No individual dimension across any agent below 6.0
- **Near-miss rule**: If overall >= 7.5 and only one dimension is between 5.0-5.9, flagged as "conditional pass" at human checkpoint

### Score Summary File

`drafts/reviews/score-summary.md` updated after every review cycle:

```markdown
# Score Summary: [topic]

## Current Status: [IN PROGRESS / PASSED / ACCEPTED / ABANDONED]

| Version | Architect | Content | Formatting | Image | Overall | Status |
|---|---|---|---|---|---|---|
| v1 | 6.2 | 5.8 | 7.1 | 6.5 | 6.3 | Below threshold |
| v2 | 7.8 | 7.5 | 8.2 | 7.0 | 7.6 | Below threshold |
| v3 | 8.5 | 8.3 | 8.8 | 8.0 | 8.4 | PASSED |

## Dimensions Below Floor (current version)
[list any below 6.0]

## Trend
[per-agent trend with direction]
```

### Iteration Controls

- **Max 3 autonomous iterations** before mandatory human checkpoint
- **At checkpoint, user chooses**: Continue (3 more) / Steer (give guidance) / Accept (override threshold) / Abandon (keep drafts)
- **Hard ceiling**: 9 iterations (3 checkpoints)
- **Early exit**: If draft passes before checkpoint, present immediately for user approval

### Revision Strategy

When revising between iterations, the main agent:
1. Fixes any dimension below 6.0 first (blockers)
2. Addresses lowest-scoring dimension across all agents (biggest impact)
3. Resolves conflicting feedback using blog type as tiebreaker
4. Applies quick wins (editorial compliance fixes)
5. Includes brief changelog at top of new draft (removed during finalization)

---

## Phase 5: Finalize

### Purpose
Produce submission-ready artifacts and close the loop.

### Sequence

1. **Strip internal changelog** from passing draft
2. **Pre-fill blog submission form** template at top of draft:
   - Publication type, byline (defaults to Peter Double, overridable during qualifying), reviewers needed, image checklist, pre-submission checklist
3. **Write `final.md`**: Submission form + clean draft
4. **Generate `seo.md`**:
   - Meta title (50-60 chars, keywords front-loaded)
   - Meta description (150-160 chars, action-oriented)
   - Primary and secondary keywords
   - Suggested URL slug
   - Internal link suggestions
5. **Update `score-summary.md`** with final status
6. **Offer Google Doc creation** (optional):
   - If yes: create via `mcp__google-workspace__create_doc` with submission form + draft content
   - If no: skip, local artifacts are complete
7. **Update `docs/knowledge-registry.md`**:
   - Section 12 Blog Examples: add new entry
   - Section 11: add any new stakeholders mentioned
8. **Present completion summary**

### Completion Summary

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

---

## Existing Draft Review Mode

When invoked with an existing draft rather than creating from scratch:

1. **Qualify**: Shortened — skill reads draft, infers blog type/audience/thesis, confirms with user
2. **Abstract**: Generated from existing draft rather than before it
3. **Draft**: Existing content becomes v1 (copied to `drafts/v1.md` as-is)
4. **Review Loop**: Normal — sub-agents review v1, iteration begins
5. **Finalize**: Same as creating from scratch

---

## Integration Points

| Existing Asset | How create-blog Interacts |
|---|---|
| `add-knowledge-source` skill | Pattern followed for knowledge registry updates at finalization |
| `create-diagram` skill | Image reviewer flags placements that need architecture diagrams rather than generated images, recommending this skill |
| `docs/blogs/blog-creation-guide.md` | Style reference loaded by all sub-agent reviewers |
| `docs/knowledge-registry.md` | Read at start for grounding; updated at end with new blog entry |
| Google Workspace MCP | Used for fetching source Google Docs and optionally creating final Google Doc |
| Playwright MCP | Used for fetching blog examples and web research (Red Hat blogs are JS-rendered) |

---

## Future Extension Points

- **Image generation integration**: Image reviewer evolves to review generated images, not just placeholders
- **Fifth reviewer**: Could add a Security/Compliance reviewer for posts touching security claims
- **Rubric tuning**: Weights and dimensions can be adjusted per `references/scoring.md` without touching workflow
- **Series management**: A `series.md` manifest that tracks all posts in a series and their cross-linking status
