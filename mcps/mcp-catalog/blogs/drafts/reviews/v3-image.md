# Image Review — v3

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Placement rationale | 8 | 2x | 16 |
| Prompt specificity | 8 | 2x | 16 |
| Brand compliance | 9 | 2x | 18 |
| Aspect ratio & sizing | 9 | 1x | 9 |
| Alt text quality | 8 | 1x | 8 |
| Image count | 6 | 1x | 6 |
| **Total** | | | **73 / 90** |

**Normalized score: 8.1 / 10**

## Per-Image Feedback

### Image Placeholder 1: MCP Catalog in AI Hub

- **Placement**: Appropriate. Positioned at line 25, immediately after the "select, deploy, connect" summary sentence that closes the catalog explanation. The reader has just learned what the catalog does and how the flow works (Catalog -> Lifecycle Operator -> Gateway -> Gen AI Studio); seeing the UI at this point reinforces the narrative. No change needed from v2.
- **Prompt quality**: Improved over v2. The v2 review flagged two remaining issues: (1) the prompt did not specify which 6 of the 10 MCP servers appear on the visible cards, and (2) "provider logo area" was vague. The v3 prompt now explicitly lists the 6 visible cards: "OpenShift, Ansible, Confluent, Dynatrace, Microsoft Azure and MongoDB." This is a good selection — it represents all three tiers (2 Red Hat, 2 partner, 1 community, plus one more partner), ensuring the reader sees ecosystem breadth at a glance. The "provider logo area" wording remains unchanged — this is a minor residual ambiguity (see below) but does not materially affect generation quality since the card structure is otherwise well-specified.
  - Remaining minor issue: "provider logo area" — for a generated mockup, this could produce anything from a colored rectangle to a generic icon to an attempt at actual partner logos. Specifying "colored placeholder rectangle matching the tier badge color" or "generic server icon" would eliminate the last ambiguity. This is minor — the prompt is otherwise production-ready.
- **Brand compliance**: Fully compliant. All colors reference the official palette: #212427 (near-black neutral), #F0F0F0 (light gray neutral), #EE0000 (Red Hat Red). Typography specifies Red Hat Text for card body, Red Hat Display for headings. PatternFly design system is referenced for UI consistency. No brand issues.
- **Aspect ratio**: 16:9 specified. Correct for a wide UI screenshot or mockup. Consistent and appropriate.
- **Alt text**: Strong. "The MCP Catalog in the Red Hat OpenShift AI Hub displaying MCP server cards organized by tier — Red Hat, partner and community — each with a deploy action, showing the governed path from discovery to deployment." This communicates both content (what you see) and purpose (governed path from discovery to deployment). A screen reader user would understand why this image matters. No changes needed.
- **Recommendation**: Keep. One optional refinement: clarify "provider logo area" to "colored placeholder rectangle" or "generic server icon" for mockup generation. This is polish, not blocking.

### Image Placeholder 2: Three-tier MCP ecosystem

- **Placement**: Appropriate. Positioned at line 60, after the full 10-server bulleted listing and the Dynatrace before/after comparison. Serves as a visual summary — the reader has absorbed the details and this diagram provides a scannable anchor. The placement rationale explicitly states this purpose ("gives the reader a scannable summary after the detailed listing"). Good.
- **Prompt quality**: Improved over v2. The v2 review flagged two issues: (1) no specification of row styling (bullets, icons, or plain text) and (2) no vertical alignment direction. The v3 prompt now specifies: "Each row is the same height with centered text." This directly addresses the row styling concern — uniform row height with centered text is clear enough for a generator to produce consistent output. The centered-text specification implicitly answers the vertical alignment question (rows are uniform, content is centered within them).
  - Minor residual: The prompt does not state whether there are visual separators between rows (horizontal rules, alternating background shading, or nothing). For a 2-3-5 column layout, some row separation aids readability. Specifying "thin #F0F0F0 horizontal dividers between rows" or "no row dividers" would be fully unambiguous. This is a polish item.
  - **Recommendation**: This placement would still be well-served by the `create-diagram` skill for pixel-perfect accuracy. The prompt is now detailed enough to serve as a complete specification for either generation approach.
- **Brand compliance**: Strong. All colors are valid palette entries: #EE0000 (Red Hat Red), #0066CC (Blue interactive), #3D7317 (Green), #FFFFFF (white background), #D2D2D2 (neutral border). Typography specifies Red Hat Display 600 for the title, Red Hat Text 400 for body labels. All compliant.
- **Aspect ratio**: 16:9 specified. Correct for a wide inline diagram with three horizontal columns. Appropriate.
- **Alt text**: Good. The v3 alt text lists all 10 servers by name within their tiers and ends with "illustrating the breadth of the day-one ecosystem." This conveys both content and purpose. The v2 review noted the alt text was long (55 words) and suggested trimming to under 40 words. The v3 alt text has not been shortened — it remains at approximately the same length. This is not a defect (there is no hard character limit for alt text), but shorter alt text is generally preferred for screen reader usability. A trimmed version such as: "Three-tier diagram of the MCP Catalog: Red Hat (OpenShift, Ansible Automation Platform, Insights), partners (Confluent, EnterpriseDB, HashiCorp, Microsoft, Dynatrace) and community (MongoDB, MariaDB) — 10 servers at launch" would be tighter without losing meaning.
- **Recommendation**: Keep. Consider producing with `create-diagram` skill. Two optional refinements: (1) specify row divider treatment (dividers or none), (2) trim alt text to under 40 words.

## Missing Image Opportunities

### 1. Hero image (top of post, before first H2)

Flagged in v1 and v2 reviews. Still not present in v3. The task description notes this is "acceptable," and the author has chosen not to add it. For the record, the recommendation stands: most Red Hat blog posts feature a hero image above the fold. The editorial team may add one during Drupal production, but specifying one in the draft gives the author control over the visual opening. If deliberately omitted, no further action required.

### 2. Roadmap/timeline visual in "Building the enterprise MCP ecosystem" section

Flagged in v1 and v2 reviews. Still not present in v3. Also noted as "acceptable" per task description. Lines 68-76 describe three forward-looking themes (quickstarts, ecosystem growth, governance) that would benefit from a simple horizontal timeline or phased diagram. The `create-diagram` skill recommendation from v2 still applies. If deliberately omitted, no further action required.

### 3. CTA visual in "Get started" section

Flagged in v1 and v2 reviews. Not present in v3. This was already noted as lower priority in v2 — the editorial team often adds CTA styling during production. No further action required.

## v2 Issue Resolution Tracker

| v2 Issue | Status in v3 |
|---|---|
| Image 1: specify which 6 MCPs show on cards | Fixed — now lists OpenShift, Ansible, Confluent, Dynatrace, Microsoft Azure and MongoDB |
| Image 1: clarify "provider logo area" | Not addressed — minor residual ambiguity |
| Image 2: specify row styling (bullets/icons/plain text) | Fixed — "each row is the same height with centered text" |
| Image 2: specify vertical alignment | Fixed — uniform row height with centered text implies top-aligned columns |
| Image 2: trim alt text to under 40 words | Not addressed — current alt text still ~55 words |
| Missing hero image | Not addressed (accepted as deliberate omission) |
| Missing roadmap/timeline visual | Not addressed (accepted as deliberate omission) |
| Missing CTA visual | Not addressed (accepted as deliberate omission, low priority) |

## Summary

v3 resolves the two actionable issues from v2: Image 1 now specifies which 6 MCP servers appear on visible cards (representing all three tiers), and Image 2 now specifies row styling with uniform height and centered text. Both prompts are detailed enough to produce the intended image on the first try with minimal ambiguity. Brand compliance remains strong across both placeholders — all colors, typography and style references are valid. The score moves from 7.3 to 8.1, driven by prompt specificity improvements and the cumulative effect of three rounds of refinement. The remaining gaps are minor polish items (clarify "provider logo area" in Image 1, specify row dividers in Image 2, trim Image 2 alt text) and the deliberate omission of hero and roadmap images. If either of those missing images is added in a future iteration, the image count score would rise from 6 to 8, pushing the overall score above 8.5. The two existing placeholders are now production-ready.
