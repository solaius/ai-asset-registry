# Formatting Review — v1

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Heading hierarchy | 8 | 1x | 8 |
| Code formatting | 10 | 1x | 10 |
| CTA placement | 8 | 2x | 16 |
| SEO readiness | 6 | 1x | 6 |
| Link strategy | 5 | 1x | 5 |
| Editorial compliance | 7 | 2x | 14 |
| Brand standards | 8 | 1x | 8 |
| Word count | 8 | 1x | 8 |
| **Total** | | | **75 / 100** → **7.5** |

## Line-Level Feedback

### Heading hierarchy

All headings use sentence case correctly. The cascade is clean: H1 for title (line 1), then H2 for body sections (lines 9, 25, 56, 68). No skipped levels, no H3/H4 needed for this structure.

One issue: the H1 on line 1 is the blog title, which is correct in the Markdown draft, but worth confirming that in Drupal this will be set as the page title and removed from the body. The body content should start at H2. As a draft convention this is fine, but flagging for awareness.

- **Location**: Line 1
- **Current**: "# Introducing the MCP Catalog in Red Hat OpenShift AI: 10 MCP servers ready to deploy"
- **Corrected**: No change needed in the draft. Ensure this is entered as the Drupal page title and the body begins at the first H2.
- **Rule**: H1 reserved for page title (set in Drupal). Body starts at H2.

### Code formatting

No code blocks in this post, which is appropriate for a Red Hat Blog thought leadership / announcement piece. No backticks used for inline code or formatting. Score: 10/10 — nothing to fix.

### CTA placement

The draft has good CTA placement overall:

1. **Primary CTA near top (line 5)**: Bolded, linked to redhat.com trial page. Correct.
2. **Closing CTA (line 72)**: Bolded, linked to the same trial page. Correct.
3. **Missing mid-article CTA**: The blog creation guide recommends a secondary CTA mid-article. There is none between the opening and closing CTAs.

- **Location**: Between lines 54 and 56 (after the "10 MCP servers" section, before "Building the enterprise MCP ecosystem")
- **Current**: No mid-article CTA present
- **Corrected**: Add a secondary CTA linking to the MCP Lifecycle Operator GitHub repo or another relevant resource. Example: "**[Explore the MCP Lifecycle Operator on GitHub](https://github.com/kubernetes-sigs/mcp-lifecycle-operator)** to see how MCP servers are deployed and managed on Kubernetes."
- **Rule**: Blog creation guide CTA placement: "Primary CTA: near the top, bolded and linked. Secondary CTA: mid-article, bolded and linked. Closing CTA: in your conclusion."

Additionally, the abstract defines a secondary CTA — "Explore the MCP Lifecycle Operator on GitHub" — that is never used in the draft.

- **Location**: Throughout the draft
- **Current**: The MCP Lifecycle Operator GitHub repo is never linked
- **Corrected**: Add the link inline where the Lifecycle Operator is first mentioned in detail (line 13 or as a standalone CTA between sections)
- **Rule**: Abstract defines the secondary CTA as the GitHub repo link; the draft should include it.

### SEO readiness

- **Location**: Line 1 (title)
- **Current**: "Introducing the MCP Catalog in Red Hat OpenShift AI: 10 MCP servers ready to deploy" (83 characters)
- **Corrected**: Shorten to 50-60 characters for meta title. Suggested meta title: "MCP Catalog in Red Hat OpenShift AI: 10 servers ready" (54 characters). The full title can remain as the Drupal page title, but a separate meta title should be prepared.
- **Rule**: "Write a meta title (50-60 characters) and meta description (150-160 characters)"

The blog creation guide also calls for a custom URL slug. None is proposed.

- **Location**: Missing from draft metadata
- **Current**: No custom URL slug
- **Corrected**: Propose a URL slug such as `mcp-catalog-openshift-ai` or `introducing-mcp-catalog-openshift-ai`
- **Rule**: "Create a custom URL — Drupal defaults are poor; make it concise with your target keyword"

Keyword strategy is decent: "MCP Catalog" appears in the title and first paragraph. "MCP servers" also appears prominently. However, "Red Hat OpenShift AI" is the primary product and should ideally be front-loaded closer to the start of the title rather than after the colon.

- **Location**: Line 1
- **Current**: "Introducing the MCP Catalog in Red Hat OpenShift AI: 10 MCP servers ready to deploy"
- **Corrected**: Consider reordering for SEO: "Red Hat OpenShift AI introduces the MCP Catalog: 10 MCP servers ready to deploy" — though the current version is acceptable since "MCP Catalog" is the primary keyword target.
- **Rule**: "Front-load keywords in your headline"

No meta description is prepared. The first paragraph could serve as the basis for one, but it should be explicitly drafted (150-160 characters).

- **Location**: Missing
- **Corrected**: Draft a meta description, e.g.: "Red Hat OpenShift AI 3.4 introduces the MCP Catalog with 10 pre-loaded MCP servers from Red Hat, partners and community. Discover, deploy and manage." (155 characters)
- **Rule**: "Write a meta title (50-60 characters) and meta description (150-160 characters)"

### Link strategy

The draft has only one link, used twice (the trial page CTA). For a post that mentions numerous products and projects, this is insufficient.

- **Location**: Line 5 (first mention of "Red Hat OpenShift AI")
- **Current**: The full product name "Red Hat OpenShift AI" is mentioned but linked only to the trial page via a CTA
- **Corrected**: Link "Red Hat OpenShift AI" to the product page on first mention: [Red Hat OpenShift AI](https://www.redhat.com/en/technologies/cloud-computing/openshift/openshift-ai)
- **Rule**: "Link to Red Hat product pages on first mention"

- **Location**: Line 31 (first mention of "Red Hat OpenShift")
- **Current**: "**Red Hat OpenShift**" — bolded but not linked
- **Corrected**: Link to the OpenShift product page: [Red Hat OpenShift](https://www.redhat.com/en/technologies/cloud-computing/openshift)
- **Rule**: "Link to Red Hat product pages on first mention"

- **Location**: Line 32 (first mention of "Red Hat Ansible Automation Platform")
- **Current**: "**Red Hat Ansible Automation Platform**" — bolded but not linked
- **Corrected**: Link to the Ansible Automation Platform product page: [Red Hat Ansible Automation Platform](https://www.redhat.com/en/technologies/management/ansible)
- **Rule**: "Link to Red Hat product pages on first mention"

- **Location**: Line 33 (first mention of "Red Hat Insights")
- **Current**: "**Red Hat Insights (Lightspeed)**" — bolded but not linked
- **Corrected**: Link to the Red Hat Insights product page
- **Rule**: "Link to Red Hat product pages on first mention"

- **Location**: Lines 37-41 (partner product mentions)
- **Current**: Partner products (Confluent Kafka, EnterpriseDB, HashiCorp Terraform, Microsoft Azure, Dynatrace) are mentioned but none are linked
- **Corrected**: Link each partner product to its relevant page (partner website or partner MCP server repo) on first mention
- **Rule**: "Link to upstream project repos (GitHub) where relevant"

- **Location**: Line 7 (first mention of "MCP Lifecycle Operator")
- **Current**: "MCP Lifecycle Operator" — not linked
- **Corrected**: Link to the GitHub repo: [MCP Lifecycle Operator](https://github.com/kubernetes-sigs/mcp-lifecycle-operator)
- **Rule**: "Link to upstream project repos (GitHub) where relevant"

- **Location**: Line 3 (first mention of "Model Context Protocol (MCP)")
- **Current**: "The Model Context Protocol (MCP)" — not linked
- **Corrected**: Link to the MCP specification or the modelcontextprotocol.io site
- **Rule**: Readers unfamiliar with MCP should have a reference link on first mention

### Editorial compliance

**Oxford commas**: No Oxford commas found. Correct.

**Official product names**: Mostly correct, with some issues:

- **Location**: Line 5
- **Current**: "Red Hat OpenShift AI 3.4" — uses the version number with the full official name. Correct on first mention.
- **Rule**: Use full official name on first mention.

- **Location**: Line 58
- **Current**: "RHOAI 3.4"
- **Corrected**: "Red Hat OpenShift AI 3.4" — while abbreviation after first mention is sometimes acceptable, "RHOAI" is not an official abbreviation recognized in the Red Hat product names list. Use the full name or establish a parenthetical abbreviation on first mention if abbreviation is needed later.
- **Rule**: "Official product names: Always use the full official name on first mention. Abbreviated forms acceptable after first mention if the abbreviation is official."

- **Location**: Line 70
- **Current**: "Red Hat OpenShift AI 3.4 Developer Preview" — correct usage.

**Jargon check**: A few terms that could be opaque to the target audience:

- **Location**: Line 13
- **Current**: "UBI-based container images"
- **Corrected**: "Red Hat Universal Base Image (UBI)-based container images" or add a brief explanation. "UBI" is Red Hat-internal jargon that IT decision makers may not recognize.
- **Rule**: "No jargon, hyperbole, or unsupported claims"

- **Location**: Line 13
- **Current**: "streamable HTTP transport"
- **Corrected**: This is MCP-specific terminology. For the target audience (IT decision makers, enterprise architects), consider a brief parenthetical: "streamable HTTP transport (the production-grade MCP communication protocol)" or simply drop the technical detail and say "production-grade connectivity."
- **Rule**: Target audience is IT decision makers and enterprise architects, not MCP protocol specialists.

**Image alt text**: Both image placeholders include alt text. Correct.

**Image count**: 2 image placeholders. Under the 10-image limit. Correct.

**No animated GIFs**: Correct — no GIFs referenced.

**Features at tech preview+**: The post refers to "Developer Preview" throughout. Developer Preview is the Red Hat equivalent of tech preview. This is acceptable for a blog post, though the distinction between "Developer Preview" and "Technology Preview" should be confirmed with the product team.

### Brand standards

Image generation prompts reference appropriate Red Hat brand colors (#EE0000, #151515, #F5F5F5, #0066CC, #3D7317, #FFFFFF). Font references mention "Red Hat Display font" correctly. Overall alignment with the Brand Standards Quick Reference is good.

- **Location**: Line 21 (image prompt 1)
- **Current**: References "#EE0000 for accent elements" — correct Red Hat Red usage.
- **Corrected**: No change needed.

- **Location**: Line 52 (image prompt 2)
- **Current**: References multiple brand colors and "Red Hat Display font" — all correct.
- **Corrected**: No change needed.

No other typographic or visual references exist in the body text. Brand standards are well-maintained for the visual assets described.

### Word count

The blog content (excluding image placeholder blocks) is approximately 903 words. The abstract targets ~1,000 words. The blog type is "thought leadership / announcement" which maps to the 600-800 or 800-1,300 word range in the guide. At 903 words, this is solidly within range and appropriate for the topic depth.

No word count issues.

## Editorial Compliance Checklist

- [x] All headings in sentence case
- [x] No Oxford commas
- [x] No backticks (monospace indicated correctly)
- [ ] Official product names on first mention — "RHOAI" used on line 58 instead of "Red Hat OpenShift AI"
- [x] No H1 in body (H1 used correctly as draft title only)
- [x] All images have alt text
- [x] No animated GIFs
- [x] Features at tech preview+ only (Developer Preview)
- [x] Word count appropriate (903 words, target ~1,000)

## Summary

The draft is editorially clean in many respects — sentence case headings, no Oxford commas, no backticks, appropriate word count, proper alt text on images and good brand color usage in image prompts. The two most impactful issues to fix before submission are: (1) **the near-total absence of hyperlinks** — Red Hat products, partner products, the MCP Lifecycle Operator GitHub repo and the Model Context Protocol itself are all mentioned without links, which violates the "link to Red Hat product pages on first mention" rule and deprives the reader of pathways to explore further; and (2) **missing SEO metadata** — no meta title, no meta description and no custom URL slug are proposed, which will create friction in the Workfront submission process. A secondary issue is the use of "RHOAI" as an unofficial abbreviation on line 58, and some unexplained jargon ("UBI", "streamable HTTP transport") that may not land with the target audience of IT decision makers and enterprise architects.
