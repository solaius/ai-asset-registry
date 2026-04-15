# Image Review — v1

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Placement rationale | 7 | 2x | 14 |
| Prompt specificity | 5 | 2x | 10 |
| Brand compliance | 7 | 2x | 14 |
| Aspect ratio & sizing | 7 | 1x | 7 |
| Alt text quality | 6 | 1x | 6 |
| Image count | 7 | 1x | 7 |
| **Total** | | | **58 / 90** |

**Normalized score: 6.4 / 10**

## Per-Image Feedback

### Image Placeholder 1: MCP Catalog in AI Hub

- **Placement**: Appropriate. Placed after the "from discovery to deployment" section, which describes the catalog experience. Showing the reader what the UI looks like directly reinforces the text. Good position — the reader just read about browsing, selecting and deploying, and now sees the interface.
- **Prompt quality**: Needs more detail. The prompt says "screenshot-style illustration" which is ambiguous — is this a real screenshot or an AI-generated mockup? If it is a real screenshot, a generation prompt is unnecessary; the team should capture the actual UI. If it is a generated image, the prompt lacks critical specifics:
  - No specification of how many cards to show or what text appears on them.
  - "Grid of MCP server cards" is underspecified — how many columns? What metadata is visible on each card (description, version, deploy button)?
  - "Partner logos" is listed but no guidance on how logos should be rendered — as icons within the cards, as separate badges, as watermarks?
  - No mention of the MCP Lifecycle Operator or the "deploy" action path visible in the UI, which is a key narrative point in the preceding text.
  - Missing any indication of the navigation structure (sidebar items, breadcrumbs, tabs) beyond just "tabs."
  - **Suggestion**: If the actual RHOAI 3.4 UI is available, capture a real screenshot. Screenshots are more credible for product announcements than generated mockups. If a generated image is required, add: exact card count (e.g., "show 6 of 10 cards in a 3x2 grid"), visible card fields (name, logo, short description, tier badge, deploy button), sidebar navigation items (e.g., "AI Hub > MCP Catalog"), and a selected-state card showing the deploy action.
- **Brand compliance**: Partially compliant. References #151515 for sidebar, #F5F5F5 for content area and #EE0000 for accents — these are valid palette colors. However:
  - #F5F5F5 is not in the official brand palette. The nearest neutrals are #F0F0F0 or #F2F2F2. Use one of those instead.
  - No typography direction. For a UI mockup, text labels should use Red Hat Text for body and Red Hat Display for headings. The prompt does not mention fonts.
  - "Modern, clean enterprise UI style" is generic. Reference the RHOAI/PatternFly design system or the brand Illustration sub-page for style specificity.
- **Aspect ratio**: 16:9 is correct for a hero/wide UI screenshot. Appropriate for the context.
- **Alt text**: Adequate but could be more purposeful. Current text describes what is shown ("MCP server cards from Red Hat, technology partners and community projects available for deployment") but does not convey *why* — the alt text should communicate that this is a governed, deploy-ready catalog, not just a listing. Suggested revision: "The MCP Catalog in the Red Hat OpenShift AI Hub displaying MCP server cards organized by tier — Red Hat, partner and community — each with a deploy action, showing the governed path from discovery to deployment."
- **Recommendation**: Revise prompt. Strongly consider replacing with a real screenshot if the UI is available. If a generated image is necessary, significantly increase prompt detail as noted above and fix the #F5F5F5 color.

### Image Placeholder 2: Three-tier MCP ecosystem

- **Placement**: Appropriate. Placed after the bulleted list of all 10 MCP servers organized by tier. The visual reinforces the three-tier structure and provides a scannable summary. Good placement — the reader just consumed a dense bulleted list and the infographic serves as a visual anchor.
- **Prompt quality**: Moderate but needs improvement. Issues:
  - "Three concentric tiers" with inner/middle/outer rings is a reasonable composition concept, but concentric ring diagrams can look cluttered with text labels. No guidance on whether the rings contain text labels, logos, icons or just color bands.
  - The prompt lists partner names but does not specify whether to show logos (which raises trademark issues) or text labels.
  - "Center label: MCP Catalog" is clear.
  - Missing: visual hierarchy direction. Which tier should draw the eye first? The inner ring (Red Hat) should likely be most prominent, but the prompt does not specify relative sizing or visual weight.
  - Missing: how the 10 individual MCP server names are displayed — within their respective rings? As callout labels? As icons?
  - **Suggestion**: Specify layout more precisely. Consider a horizontal three-column layout instead of concentric rings — it may be clearer and easier to label. If keeping rings, specify: ring thickness, whether server names appear inside the rings or as external labels with connector lines, and font sizes for readability.
  - **Recommendation**: This placement would be better served by a technical diagram. Consider using the `create-diagram` skill for this image, which can produce accurate architecture diagrams with proper Red Hat branding. A Cytoscape.js diagram could render the three tiers with proper node grouping, partner logos as node labels, and precise brand color coding.
- **Brand compliance**: Good use of the extended color families. Red #EE0000 for Red Hat tier, Blue #0066CC for partners, Green #3D7317 for community — all valid palette colors. However:
  - Specifies "Red Hat Display font" which is correct for headings and labels but should also reference Red Hat Text for any body-level text within the diagram.
  - "#FFFFFF white background" is fine.
  - No mention of neutral colors for borders, dividers or secondary elements.
- **Aspect ratio**: 4:3 is reasonable for an inline infographic. Acceptable for the context, though 16:9 could work if the three tiers are laid out horizontally rather than as concentric rings.
- **Alt text**: Functional but could be better. Current text ("three tiers of the MCP Catalog: Red Hat MCP servers in the center, technology partner servers in the middle ring and community servers in the outer ring") describes the visual structure. It would be stronger if it communicated the purpose: "Infographic showing the three tiers of MCP servers in the catalog — three from Red Hat, five from technology partners and two from the open source community — illustrating the breadth of the day-one ecosystem."
- **Recommendation**: Replace with `create-diagram` skill, or revise prompt significantly with explicit layout instructions, label specifications and visual hierarchy direction.

## Missing Image Opportunities

1. **"Get started" section (lines 69-74)**: The closing CTA section has no visual. A small banner-style image or a stylized CTA card linking to the trial page would provide a visual endpoint and break up the text-heavy close. Even a simple branded CTA block with the Red Hat OpenShift AI logo and "Try it now" would be more effective than a bare hyperlink. Placement rationale: provides a visual call-to-action that draws the eye and gives the post a strong visual close.

2. **"Building the enterprise MCP ecosystem" section (lines 56-66)**: This section describes a roadmap of future capabilities (quickstarts, governance, supply chain management). A simple timeline or roadmap diagram showing the progression from the current "MCP Catalog + 10 servers" through "quickstarts" to "full governance" would help the reader visualize the investment trajectory. Placement rationale: transforms an abstract roadmap paragraph into a scannable visual timeline, reinforcing that Red Hat has a concrete plan. This could be produced with the `create-diagram` skill.

3. **Hero image**: The blog creation guide recommends a primary CTA near the top. A hero image at the very top (before the first H2) is common for Red Hat blog posts and would set the visual tone. Currently the post opens with text only. A hero image showing the MCP Catalog concept — perhaps a stylized visual of the "select, deploy, connect" flow — would strengthen the opening. Placement rationale: most Red Hat blog posts have a hero image; the editorial team may add one during production, but specifying one gives control over the visual message.

## Summary

The draft has two well-placed image placeholders that correspond to the right moments in the narrative, but the generation prompts need significantly more specificity to produce useful images on the first try. Image 1 should ideally be a real screenshot rather than a generated mockup — for a product announcement blog, authentic UI captures carry more credibility. Image 2 (three-tier infographic) would be better produced as a technical diagram using the `create-diagram` skill, which can render precise node groupings with brand-compliant styling. Both prompts need typography references (Red Hat Display/Text), and Image 1 uses a non-standard neutral (#F5F5F5) that should be corrected to #F0F0F0 or #F2F2F2. The alt text across both images describes visual content but misses the opportunity to convey purpose — revising alt text to communicate *why* the image matters (not just *what* it shows) would improve accessibility. Finally, the post would benefit from one or two additional visuals: a roadmap/timeline diagram in the "Building the enterprise MCP ecosystem" section and a hero image to set the visual tone at the top.
