# Formatting Review — v2

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Heading hierarchy | 9 | 1x | 9 |
| Code formatting | 10 | 1x | 10 |
| CTA placement | 9 | 2x | 18 |
| SEO readiness | 6 | 1x | 6 |
| Link strategy | 9 | 1x | 9 |
| Editorial compliance | 8 | 2x | 16 |
| Brand standards | 9 | 1x | 9 |
| Word count | 8 | 1x | 8 |
| **Total** | | | **85 / 100** → **8.5** |

## Line-Level Feedback

### Heading hierarchy

All four H2 headings use sentence case correctly. The cascade is clean: H1 for title (line 1), H2 for body sections (lines 11, 27, 62, 72). No skipped levels, no misuse of H3/H4. The structure is flat — four H2s with no H3s — which is appropriate for the article's length and complexity.

The "10 MCP servers ready to use" section (line 27) could benefit from H3 subheadings for the three tiers ("Red Hat MCP servers", "Technology partner MCP servers", "Community MCP servers"), but this is a stylistic suggestion, not an error. The bold text labels currently serving as tier headers work fine for scannability.

One minor issue: the title on line 1 is 83 characters, which is long for a Drupal page title. This does not violate heading hierarchy rules, but it will be noted under SEO readiness.

- **Location**: Line 72
- **Current**: "## Get started"
- **Corrected**: No change needed. Sentence case is correct here ("Get started" not "Get Started"). Flagging only to confirm this was checked.
- **Rule**: Sentence case headings — only capitalize the first word and proper nouns.

### Code formatting

No code blocks in this post, which is correct for a Red Hat Blog thought leadership / announcement piece. No backticks used anywhere in the body text. Score: 10/10 — nothing to fix.

### CTA placement

Major improvement over v1. All three CTA positions are now filled:

1. **Primary CTA near top (line 7)**: "**[Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial)**" — bolded, linked to redhat.com trial page. Correct placement within the second paragraph.
2. **Secondary CTA mid-article (line 52)**: "**[Explore the MCP Lifecycle Operator on GitHub](https://github.com/kubernetes-sigs/mcp-lifecycle-operator)**" — bolded, linked to GitHub. Placed after the 10-server listing and the Dynatrace before/after scenario. Correct.
3. **Closing CTA (line 76)**: "**[Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial)**" — bolded, linked to trial page. Correct.

The primary CTA links to the Red Hat site system (trial page). The secondary CTA links to an external GitHub repo, which is acceptable per the guide ("External links (GitHub, upstream projects) are fine as secondary CTAs"). Both match the CTAs defined in the abstract.

One minor issue: the primary CTA on line 7 reads as a standalone sentence rather than being woven into the paragraph. It works, but is slightly abrupt after the preceding sentence.

- **Location**: Line 7
- **Current**: "**[Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial)** to explore the MCP Catalog yourself."
- **Corrected**: No change strictly needed — this is functional. If polish is desired: integrate into the preceding sentence or rephrase as "Get started by **[trying Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial)** to explore the MCP Catalog yourself."
- **Rule**: CTA placement guide says "Primary CTA: near the top, bolded and linked." This is met.

### SEO readiness

This is the weakest dimension, unchanged from v1. No SEO metadata has been added to the draft.

- **Location**: Missing from draft
- **Current**: No meta title provided
- **Corrected**: Add a meta title of 50-60 characters. Suggested: "MCP Catalog in Red Hat OpenShift AI: 10 servers ready" (54 characters)
- **Rule**: "Write a meta title (50-60 characters)"

- **Location**: Missing from draft
- **Current**: No meta description provided
- **Corrected**: Add a meta description of 150-160 characters. Suggested: "Red Hat OpenShift AI 3.4 introduces the MCP Catalog with 10 MCP servers from Red Hat, partners and community. Discover, deploy and manage on OpenShift." (152 characters)
- **Rule**: "Write a meta description (150-160 characters)"

- **Location**: Missing from draft
- **Current**: No custom URL slug proposed
- **Corrected**: Propose a URL slug such as `mcp-catalog-openshift-ai` or `introducing-mcp-catalog`
- **Rule**: "Create a custom URL — Drupal defaults are poor; make it concise with your target keyword"

Keyword strategy is adequate: "MCP Catalog" appears in the title and first paragraph. "MCP servers" appears prominently throughout. "Red Hat OpenShift AI" appears early. The title at 83 characters is too long for a meta title but works as a Drupal page title — the issue is that no separate meta title has been prepared.

**Recommendation**: Add an SEO metadata block at the top of the draft (below the changelog), formatted as:

```
**Meta title**: MCP Catalog in Red Hat OpenShift AI: 10 servers ready (54 chars)
**Meta description**: Red Hat OpenShift AI 3.4 introduces the MCP Catalog with 10 MCP servers from Red Hat, partners and community. Discover, deploy and manage on OpenShift. (152 chars)
**URL slug**: /blog/mcp-catalog-openshift-ai
**Target keywords**: MCP Catalog, MCP servers, Red Hat OpenShift AI
```

### Link strategy

Major improvement over v1 (which scored 5). The draft now links to product pages on first mention for all Red Hat products and to the MCP specification.

Links present (11 total):
1. [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) — line 5, first mention. Correct.
2. [Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai) — line 7, product page. Correct.
3. [Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift) — line 7, product page. Correct.
4. [Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial) — line 7, primary CTA. Correct.
5. [MCP Lifecycle Operator](https://github.com/kubernetes-sigs/mcp-lifecycle-operator) — line 9, GitHub repo. Correct.
6. [Red Hat Universal Base Image](https://www.redhat.com/en/blog/introducing-red-hat-universal-base-image) — line 15, blog post link. Correct.
7. [Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift) — line 33, second mention (duplicate link is fine).
8. [Red Hat Ansible Automation Platform](https://www.redhat.com/en/technologies/management/ansible) — line 34, product page. Correct.
9. [Red Hat Insights](https://www.redhat.com/en/technologies/management/insights) — line 35, product page. Correct.
10. [Explore the MCP Lifecycle Operator on GitHub](https://github.com/kubernetes-sigs/mcp-lifecycle-operator) — line 52, secondary CTA. Correct.
11. [Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial) — line 76, closing CTA. Correct.

Remaining gaps — partner and community MCP server entries (lines 39-48) are not linked. This is less critical than the Red Hat product links (which are now all present), but linking to partner product pages or their MCP server repos would add value for readers exploring the ecosystem.

- **Location**: Lines 39-48 (partner and community MCP server listings)
- **Current**: Partner and community products (Confluent Kafka, EnterpriseDB, HashiCorp Terraform, Microsoft Azure, Dynatrace, MongoDB, MariaDB) are mentioned without links
- **Corrected**: Consider linking each partner product to its MCP server GitHub repo or partner product page on first mention. Not strictly required by the guide ("Link to Red Hat product pages on first mention" is the rule), but beneficial for reader navigation.
- **Rule**: "Link to upstream project repos (GitHub) where relevant"

### Editorial compliance

**Oxford commas**: None found. Correct.

**Official product names**: Major improvement over v1. "RHOAI" is no longer used in the body text. Full product name "Red Hat OpenShift AI" is used consistently. However, "RHOAI" still appears in an image generation prompt on line 23.

- **Location**: Line 23 (image generation prompt)
- **Current**: "If the RHOAI 3.4 UI is available"
- **Corrected**: "If the Red Hat OpenShift AI 3.4 UI is available"
- **Rule**: Official product names should be used throughout, including in image prompts that may be seen by designers or image generation tools.

**Jargon check**: The v1 issues ("UBI" and "streamable HTTP transport") have been resolved. "Red Hat Universal Base Image" is now spelled out and linked (line 15). "Streamable HTTP" has been removed entirely.

Two new jargon concerns:

- **Location**: Line 34
- **Current**: "For AgentOps teams"
- **Corrected**: "AgentOps" is an emerging term that may not be familiar to all IT decision makers. Consider "For operations teams adopting agent-based automation" or "For teams building agent-driven operations workflows." If the term is intentional and aligns with Red Hat's product marketing terminology, add a brief parenthetical on first use: "For AgentOps (agent-driven operations) teams."
- **Rule**: "No jargon, hyperbole, or unsupported claims." Target audience is IT decision makers, enterprise architects and platform engineers.

- **Location**: Line 15
- **Current**: "Gen AI Studio, the interface in Red Hat OpenShift AI where you build and test AI applications"
- **Corrected**: The inline explanation ("the interface in Red Hat OpenShift AI where you build and test AI applications") is well done — it explains the term in context. Verify that "Gen AI Studio" is the official feature name per the Red Hat product names list. If it is a colloquial or pre-release name, use the official name instead.
- **Rule**: Use official product names. The inline explanation pattern is good practice for any term that needs context.

**Image alt text**: Both image placeholders (lines 19 and 54) include descriptive alt text. Correct.

**Image count**: 2 image placeholders. Under the 10-image limit. Correct.

**No animated GIFs**: None referenced. Correct.

**Features at tech preview+**: The post refers to "Developer Preview" throughout. Acceptable.

### Brand standards

Image generation prompts reference appropriate Red Hat brand elements:

- **Image 1 (line 23)**: Uses #212427 (dark neutral), #F0F0F0 (light gray), #EE0000 (Red Hat Red). References Red Hat Text and Red Hat Display fonts. Mentions PatternFly design system. All correct per the Brand Standards Quick Reference.
- **Image 2 (line 58)**: Uses #FFFFFF (white), #EE0000 (Red Hat Red), #0066CC (blue), #3D7317 (green), #D2D2D2 (light gray border). References Red Hat Display 600 weight and Red Hat Text 400 weight. All colors and fonts are from the official palette.

One minor note:

- **Location**: Line 58 (image prompt 2)
- **Current**: "Red Hat Display 600 weight"
- **Corrected**: The Brand Standards Quick Reference lists Red Hat Display weights as 400, 500, 600, 700, 900. Weight 600 is valid. No change needed.
- **Rule**: Typography — use correct font families and weights per brand standards.

No brand standards issues in the body text. Visual asset specifications are well-aligned with brand guidelines.

### Word count

The publishable blog content (excluding the changelog on line 3 and the image generation metadata blocks) is approximately 1,105 words. The abstract targets ~1,000 words. The blog type is "thought leadership / announcement" which maps to the 800-1,300 word range in the blog creation guide. At 1,105 words, this is solidly within range.

The word count is slightly higher than v1 (903 words), reflecting the additions requested in v1 feedback (before/after Dynatrace scenario, mid-article CTA, deeper Red Hat MCP descriptions). The increase is justified by the added substance.

No word count issues.

## Editorial Compliance Checklist

- [x] All headings in sentence case
- [x] No Oxford commas
- [x] No backticks (monospace indicated correctly)
- [x] Official product names on first mention — "Red Hat OpenShift AI" used consistently in body text
- [ ] Official product names throughout — "RHOAI" still appears in image prompt on line 23
- [x] No H1 in body (H1 used correctly as draft title only)
- [x] All images have alt text
- [x] No animated GIFs
- [x] Features at tech preview+ only (Developer Preview)
- [x] Word count appropriate (1,105 words, target ~1,000)
- [ ] SEO metadata prepared — no meta title, meta description or custom URL slug

## v1 Issue Resolution

Tracking the key issues flagged in v1:

| v1 Issue | Status | Notes |
|---|---|---|
| Nearly all product mentions unlinked | **Fixed** | 11 links now present; all Red Hat products linked on first mention |
| No SEO metadata | **Not fixed** | Still no meta title, meta description or custom URL slug |
| "RHOAI" used as unofficial abbreviation | **Mostly fixed** | Removed from body text; remains in image prompt (line 23) |
| Unexplained jargon (UBI, streamable HTTP) | **Fixed** | UBI is now "Red Hat Universal Base Image" with link; streamable HTTP removed |
| Missing secondary CTA mid-article | **Fixed** | GitHub link to MCP Lifecycle Operator added at line 52 |

## Summary

The v2 draft shows substantial improvement in the two areas that dragged v1 down most: link strategy (5 to 9) and CTA placement (8 to 9). All Red Hat products are now linked on first mention, the MCP specification and Universal Base Image are linked, and all three CTA positions (top, mid-article, closing) are filled with appropriate targets. The jargon issues from v1 (UBI, streamable HTTP) are resolved. The most impactful remaining fix is **adding SEO metadata** — a meta title (50-60 characters), meta description (150-160 characters) and custom URL slug are still missing and will be required for Workfront submission. Secondary fixes: replace "RHOAI" in the image prompt on line 23 with the full product name, and consider whether "AgentOps" (line 34) needs a brief explanation for the target audience. Overall, the draft is editorially clean and close to submission-ready once the SEO block is added.
