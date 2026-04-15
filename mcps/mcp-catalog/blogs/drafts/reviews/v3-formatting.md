# Formatting Review — v3

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Heading hierarchy | 9 | 1x | 9 |
| Code formatting | 10 | 1x | 10 |
| CTA placement | 10 | 2x | 20 |
| SEO readiness | 9 | 1x | 9 |
| Link strategy | 10 | 1x | 10 |
| Editorial compliance | 9 | 2x | 18 |
| Brand standards | 9 | 1x | 9 |
| Word count | 9 | 1x | 9 |
| **Total** | | | **94 / 100** → **9.4** |

## Line-Level Feedback

### Heading hierarchy

All four H2 headings use sentence case correctly. The cascade is clean: H1 for title (line 1), H2 for body sections (lines 17, 33, 68, 78). No skipped levels, no H3/H4 used (appropriate for this length and complexity). The bold text labels serving as tier headers within "10 MCP servers ready to use" continue to work well for scannability without introducing unnecessary heading levels.

The title on line 1 is 83 characters — long for a Drupal page title, but no longer an SEO concern because a separate 53-character meta title is now provided in the SEO metadata block. No changes needed.

### Code formatting

No code blocks in this post, which is correct for a Red Hat Blog thought leadership / announcement piece. No backticks used anywhere in the draft. Score: 10/10 — nothing to fix.

### CTA placement

All three CTA positions are filled correctly and match the CTAs defined in the abstract:

1. **Primary CTA near top (line 13)**: "**[Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial)** to explore the MCP Catalog yourself." — bolded, linked to redhat.com trial page, integrated into the opening section. Correct.
2. **Secondary CTA mid-article (line 58)**: "**[Explore the MCP Lifecycle Operator on GitHub](https://github.com/kubernetes-sigs/mcp-lifecycle-operator)**" — bolded, linked to GitHub repo. Placed after the 10-server listing and the Dynatrace before/after scenario. Correct per the guide ("External links (GitHub, upstream projects) are fine as secondary CTAs").
3. **Closing CTA (line 82)**: "**[Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial)** to explore the MCP Catalog and deploy your first MCP server on OpenShift." — bolded, linked to trial page, with a specific action prompt. Correct.

The primary CTA links to the Red Hat site system (trial page). All CTA links are valid redhat.com or github.com URLs. The closing CTA (v3) now includes a specific action ("deploy your first MCP server on OpenShift") rather than just repeating the top CTA — a nice improvement.

No issues. Full marks.

### SEO readiness

The v2 review's top recommendation was to add SEO metadata. v3 delivers this in full (lines 5-9):

- **Meta title**: "MCP Catalog in Red Hat OpenShift AI: 10 servers ready" — 53 characters. Within the 50-60 character target. Target keyword "MCP Catalog" is front-loaded. Correct.
- **Meta description**: 151 characters. Within the 150-160 character range. Contains "MCP Catalog", "MCP servers", "Red Hat OpenShift AI". Correct.
- **URL slug**: `/blog/mcp-catalog-openshift-ai` — concise, keyword-rich. Correct.
- **Target keywords**: "MCP Catalog, MCP servers, Red Hat OpenShift AI" — all three appear in the title, first paragraph and throughout the post. Correct.

One minor note: the meta description on line 7 ends with "Discover, deploy and manage on OpenShift." which is slightly generic. A more compelling variant could end with the ecosystem count: "...from Red Hat, partners and community — discover, deploy and manage 10 servers on OpenShift." However, the current version is within spec and functional.

- **Location**: Line 7
- **Current**: "Red Hat OpenShift AI 3.4 introduces the MCP Catalog with 10 MCP servers from Red Hat, partners and community. Discover, deploy and manage on OpenShift. (152 chars)"
- **Corrected**: Optional polish: "Red Hat OpenShift AI 3.4 ships the MCP Catalog with 10 MCP servers from Red Hat, partners and community. Discover, deploy and govern on OpenShift. (148 chars)" — "govern" reinforces the governance narrative. Not required.
- **Rule**: "Write a meta description (150-160 characters)."

### Link strategy

Major improvement over v2 (which scored 9). v3 adds 5 new links, bringing the total to 16. All Red Hat products, the competitor catalogs and the key upstream projects are now linked:

**New in v3:**
1. [MCP Gateway](https://github.com/Kuadrant/mcp-gateway) — line 15. GitHub repo. Correct.
2. [Red Hat Connectivity Link](https://www.redhat.com/en/technologies/cloud-computing/connectivity-link) — line 15. Product page. Correct.
3. [Smithery](https://smithery.ai/) — line 19. Competitor catalog, contextual. Correct.
4. [Docker MCP Catalog](https://hub.docker.com/) — line 19. Competitor catalog, contextual. Correct.
5. [official MCP registry](https://registry.modelcontextprotocol.io/) — line 19. MCP spec registry. Correct.

**Carried from v2 (all still present):**
6. [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) — line 11
7. [Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai) — line 13
8. [Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift) — line 13
9. [Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial) — line 13
10. [MCP Lifecycle Operator](https://github.com/kubernetes-sigs/mcp-lifecycle-operator) — line 15
11. [Red Hat Universal Base Image](https://www.redhat.com/en/blog/introducing-red-hat-universal-base-image) — line 21
12. [Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift) — line 39 (second mention, duplicate link acceptable)
13. [Red Hat Ansible Automation Platform](https://www.redhat.com/en/technologies/management/ansible) — line 40
14. [Red Hat Insights](https://www.redhat.com/en/technologies/management/insights) — line 41
15. [Explore the MCP Lifecycle Operator on GitHub](https://github.com/kubernetes-sigs/mcp-lifecycle-operator) — line 58
16. [Try Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai/trial) — line 82

The v2 gap — partner and community MCP server entries not linked — remains. Lines 45-54 mention Confluent Kafka, EnterpriseDB, HashiCorp Terraform, Microsoft Azure, Dynatrace, MongoDB and MariaDB without links to their product pages or MCP server repos. This is not a strict violation of the guide ("Link to Red Hat product pages on first mention" is the rule; partner links are advisory), and at 16 links the post is already well-linked. Flagging for awareness only.

No issues requiring correction. Full marks.

### Editorial compliance

**Oxford commas**: None found in the body text. Correct.

**Official product names**: All Red Hat products use full official names on first mention. "RHOAI" no longer appears anywhere in the draft (it was in the v2 image prompt and has been fixed). "Red Hat OpenShift AI" is used consistently throughout.

**Jargon check**: The two issues flagged in v2 have been addressed:

1. "AgentOps" (line 40) — now explained on first use as "agent-driven operations (AgentOps)". The parenthetical pattern is inverted from the typical convention (usually the abbreviation comes second), but it works in context because the author leads with the descriptive phrase and puts the jargon in parentheses, which is actually clearer for the target audience.

2. "Gen AI Studio" (line 15) — still explained inline as "the interface in Red Hat OpenShift AI where you build and test AI applications." The inline definition is good practice. Verify that "Gen AI Studio" is the official feature name per the Red Hat product names list — if the feature is still in flux, the editorial team may change it. Not a formatting issue per se, but worth confirming before Workfront submission.

One remaining jargon item:

- **Location**: Line 21
- **Current**: "production-grade connectivity (streamable HTTP transport)"
- **Corrected**: This parenthetical was restored in v3. For IT decision makers, "streamable HTTP transport" may be opaque. Consider "production-grade connectivity (modern HTTP-based transport)" or removing the parenthetical entirely, as the reader does not need to know the wire protocol to understand the value. If the term is kept for technical credibility, it functions acceptably as a parenthetical — the reader can skip it.
- **Rule**: "No jargon, hyperbole, or unsupported claims." This is borderline — the parenthetical form mitigates it, but it does not eliminate it.

**Image alt text**: Both image placeholders (lines 25 and 60) include descriptive alt text. Correct.

**Image count**: 2 image placeholders. Under the 10-image limit. Correct.

**No animated GIFs**: None referenced. Correct.

**Features at tech preview+**: The post refers to "Developer Preview" throughout. Acceptable.

### Brand standards

Image generation prompts reference appropriate Red Hat brand elements:

- **Image 1 (lines 29)**: Uses #212427 (dark neutral from the neutral family), #F0F0F0 (light gray), #EE0000 (Red Hat Red). References Red Hat Text for card body text and Red Hat Display for headings. Mentions PatternFly design system. Specifies visible cards: OpenShift, Ansible, Confluent, Dynatrace, Microsoft Azure and MongoDB. All correct per the Brand Standards Quick Reference.
- **Image 2 (line 64)**: Uses #FFFFFF (white), #EE0000 (Red Hat Red), #0066CC (blue, interactive), #3D7317 (green), #D2D2D2 (light gray border). References Red Hat Display 600 weight and Red Hat Text 400 weight. All colors and weights are from the official palette.

No brand standards issues in the body text. Visual asset specifications are well-aligned with brand guidelines.

One minor note on Image 1: the prompt specifies a "3x2 grid" showing 6 named servers. This is a good improvement over v2 (which left the visible cards unspecified). No change needed.

### Word count

The publishable blog content (excluding the changelog on line 3, the SEO metadata block on lines 5-9, and the image generation metadata blocks) is approximately 1,192 words. The abstract targets ~1,000 words. The blog type is "thought leadership / announcement" which maps to the 800-1,300 word range in the blog creation guide. At 1,192 words, this is within range but approaching the upper bound.

The increase from v2 (1,105 words) to v3 (1,192 words) reflects the additions: MCP Gateway and Connectivity Link in the deployment story, the curation learnings paragraph, the competitor catalog references and the strengthened Insights entry. The additions are substantive and justified — no padding.

If the word count continues to grow in future iterations, consider whether any paragraph can be tightened. The curation paragraph (line 74) at ~67 words could potentially be shortened, but it adds genuine value (explaining why some servers did not ship). No action required at this count.

## Editorial Compliance Checklist

- [x] All headings in sentence case
- [x] No Oxford commas
- [x] No backticks (monospace indicated correctly)
- [x] Official product names on first mention — "Red Hat OpenShift AI" used consistently throughout
- [x] Official product names throughout — "RHOAI" no longer appears anywhere in the draft
- [x] No H1 in body (H1 used correctly as draft title only)
- [x] All images have alt text
- [x] No animated GIFs
- [x] Features at tech preview+ only (Developer Preview)
- [x] Word count appropriate (1,192 words, target ~1,000, range 800-1,300)
- [x] SEO metadata prepared — meta title (53 chars), meta description (151 chars), custom URL slug, target keywords

## v2 Issue Resolution

Tracking the key issues flagged in v2:

| v2 Issue | Status | Notes |
|---|---|---|
| No SEO metadata (meta title, description, URL slug) | **Fixed** | Full SEO metadata block added at lines 5-9. Meta title 53 chars, meta description 151 chars, URL slug and target keywords all present. |
| "RHOAI" in image prompt (line 23) | **Fixed** | "RHOAI" no longer appears anywhere in the draft. Image prompt now uses "Red Hat OpenShift AI". |
| "AgentOps" jargon unexplained | **Fixed** | Now reads "agent-driven operations (AgentOps)" on line 40 — parenthetical explanation on first use. |
| Partner/community links missing (advisory) | **Not addressed** | Lines 45-54 still lack links to partner product pages or MCP server repos. Advisory only — not a rule violation. |
| Image 1 visible cards unspecified | **Fixed** | Prompt now specifies 6 named server cards in a 3x2 grid. |

## Summary

The v3 draft resolves all three mandatory issues from v2: SEO metadata is now complete and well-formed (meta title 53 chars, meta description 151 chars, custom URL slug, target keywords), "RHOAI" has been removed from the image prompt, and "AgentOps" is explained on first use. Link strategy improved significantly with 5 new links (MCP Gateway, Red Hat Connectivity Link, three competitor catalogs), bringing the total to 16 — all valid and well-placed. The only remaining editorial consideration is the restored "streamable HTTP transport" parenthetical on line 21, which is borderline jargon for the target audience but is mitigated by its parenthetical placement. The draft is editorially clean and ready for Workfront submission. Score: 9.4, up from 8.5.
