# Image Review — v2

## Scores

| Dimension | Raw (1-10) | Weight | Weighted |
|---|---|---|---|
| Placement rationale | 7 | 2x | 14 |
| Prompt specificity | 7 | 2x | 14 |
| Brand compliance | 8 | 2x | 16 |
| Aspect ratio & sizing | 8 | 1x | 8 |
| Alt text quality | 8 | 1x | 8 |
| Image count | 6 | 1x | 6 |
| **Total** | | | **66 / 90** |

**Normalized score: 7.3 / 10**

## Per-Image Feedback

### Image Placeholder 1: MCP Catalog in AI Hub

- **Placement**: Appropriate. Correctly placed after the "from discovery to deployment" section at line 19. The reader just learned about the catalog's browse-select-deploy flow, and seeing the UI immediately afterward reinforces the narrative. This placement was good in v1 and remains good in v2.
- **Prompt quality**: Significantly improved over v1. The v1 feedback asked for: screenshot-vs-mockup disambiguation, specific card count and layout, visible card fields, sidebar navigation items, and deploy action path. The v2 prompt now addresses all of these:
  - Explicitly distinguishes screenshot (preferred) from generated mockup with a conditional ("If the RHOAI 3.4 UI is available, capture a real screenshot... If a generated mockup is needed:").
  - Specifies a 3x2 grid of MCP server cards.
  - Lists visible card fields: server name, provider logo area, one-line description, tier badge (Red Hat / Partner / Community), and a "Deploy" button.
  - Specifies sidebar navigation items ("AI Hub > MCP Catalog").
  - Remaining issue: The prompt does not specify which 6 of the 10 MCP servers to show on the visible cards. A generator will have to guess. Listing the 6 card names (e.g., "OpenShift, Ansible Automation Platform, Confluent Kafka, Dynatrace, MongoDB, MariaDB") would eliminate ambiguity and ensure each tier is represented.
  - Minor: "provider logo area" is vague — does this mean a placeholder rectangle, a generic icon, or actual partner logos? For a generated mockup, specifying "placeholder icon or colored rectangle for partner branding" would be clearer and avoid trademark issues.
- **Brand compliance**: Good improvement. The v1 issue with #F5F5F5 is fixed — v2 uses #F0F0F0, which is in the official neutral palette. Other colors are valid: #212427 (near-black, in palette), #EE0000 (Red Hat Red). Typography is now specified: Red Hat Text for card body, Red Hat Display for headings. PatternFly design system is referenced. Compliant.
- **Aspect ratio**: 16:9 specified. Correct for a wide UI screenshot/mockup. No issues.
- **Alt text**: Improved. v1 alt text described what was shown; v2 alt text now conveys purpose: "showing the governed path from discovery to deployment." This directly addresses the v1 feedback about communicating *why* the image matters. The phrase "organized by tier — Red Hat, partner and community" is also useful for screen reader users. Solid.
- **Recommendation**: Keep. Minor revisions: specify which 6 MCP servers appear on visible cards, and clarify "provider logo area" to avoid ambiguity.

### Image Placeholder 2: Three-tier MCP ecosystem

- **Placement**: Appropriate. Placed at line 54, after the full bulleted listing of all 10 MCP servers and the Dynatrace before/after scenario. This serves as a visual summary and scannable anchor after dense text. Good positioning, same rationale as v1.
- **Prompt quality**: Substantially improved. The v1 feedback flagged that the concentric ring layout was cluttered and underspecified. v2 has completely reworked the composition:
  - Changed from concentric rings to a clean horizontal three-column layout — directly following the v1 suggestion.
  - Each column has a labeled header bar with a specific color.
  - Individual MCP server names are listed as text-label rows within each column.
  - A center title is specified with exact font and weight ("Red Hat Display 600 weight").
  - Column body text specifies "Red Hat Text 400 weight."
  - Thin #D2D2D2 borders between columns — a valid neutral from the palette.
  - Remaining issue: The prompt says "text labels" for each row but does not specify whether each row has an icon, bullet marker, or just plain text. For a clean diagram, specifying "left-aligned text labels with no bullets" or "text labels preceded by a small colored dot matching the header bar" would remove ambiguity.
  - Minor: The prompt does not indicate vertical alignment — are the three columns top-aligned with their content, or centered? Top-alignment is typical and should be stated explicitly.
  - **Recommendation**: This placement would be better served by a technical diagram. Consider using the `create-diagram` skill for this image, which can produce an accurate three-column layout with proper Red Hat branding, precise node grouping and exact text content. The prompt is now detailed enough to serve as a good specification for either a generated image or a `create-diagram` build.
- **Brand compliance**: Strong. All colors are valid palette entries: #EE0000 (Red Hat Red), #0066CC (Blue interactive), #3D7317 (Green), #FFFFFF (white), #D2D2D2 (neutral). Typography specifies Red Hat Display for the title and Red Hat Text for body — both correct per brand standards. No issues.
- **Aspect ratio**: 16:9 specified. Correct for a wide inline diagram with three horizontal columns. This is an improvement over v1's 4:3 for the concentric ring layout — the horizontal three-column composition naturally fits a wider ratio.
- **Alt text**: Improved. v2 alt text now lists all 10 servers by name within their tiers and ends with "illustrating the breadth of the day-one ecosystem" — conveying purpose, not just structure. This directly addresses the v1 feedback. The alt text is detailed enough that a screen reader user would understand both the content and the intent. One minor note: the alt text is long (55 words). While there is no strict limit, shorter alt text (under 40 words) is generally preferred for readability. Consider trimming to: "Three-tier diagram of the MCP Catalog: Red Hat (OpenShift, Ansible Automation Platform, Insights), partners (Confluent, EnterpriseDB, HashiCorp, Microsoft, Dynatrace) and community (MongoDB, MariaDB) — 10 servers at launch."
- **Recommendation**: Keep. Consider producing with `create-diagram` skill for pixel-perfect accuracy. Minor revisions: specify row styling (plain text vs. bulleted) and vertical alignment.

## Missing Image Opportunities

### 1. Hero image (top of post, before first H2)

This was flagged in the v1 review and has not been addressed. Most Red Hat blog posts feature a hero image above the first H2 that sets the visual tone. The current draft opens with text only (lines 1-9). A hero image would:
- Establish the visual identity of the post before the reader begins.
- Provide the editorial team with a specified image rather than leaving it to production.
- Reinforce the "MCP Catalog" concept immediately.

**Suggested placement**: Between line 3 (end of changelog) and line 5 (opening paragraph), or between line 9 (end of opening section) and line 11 (first H2).

**Suggested prompt direction**: A stylized visual of the "select, deploy, connect" flow — three connected stages rendered as a horizontal pipeline with the MCP Catalog at the center. Use #EE0000, #0066CC and neutrals (#212427, #F0F0F0). Red Hat Display for any text. 16:9 hero aspect ratio. Clean, modern, not overloaded — consistent with Red Hat brand illustration style.

**Suggested alt text**: "Visual showing the MCP Catalog workflow: select an MCP server, deploy it on OpenShift and connect it to your AI workloads — the governed path from discovery to deployment."

### 2. Roadmap/timeline visual in "Building the enterprise MCP ecosystem" section

Also flagged in v1 and not addressed. Lines 62-70 describe a forward-looking roadmap: quickstarts coming, ecosystem growth, governance layer next. This is currently three text paragraphs with bold lead-ins. A simple horizontal timeline or phased roadmap diagram would:
- Transform an abstract roadmap into a scannable visual.
- Reinforce that Red Hat has a concrete, phased plan.
- Break up the text-heavy closing sections.

**Suggested placement**: After line 70 (end of governance paragraph), before the "Get started" H2.

**Suggested prompt direction**: Horizontal three-phase timeline. Phase 1 (current, highlighted): "MCP Catalog + 10 servers." Phase 2 (next): "Partner quickstarts." Phase 3 (future): "Enterprise governance — supply chain, trust tiers, access policies, auditability." Use #EE0000 for the current phase, #6A6E73 for future phases. Red Hat Display for phase labels. 16:9 wide aspect ratio. Clean, minimal — timeline style, not Gantt chart.

**Recommendation**: This placement would be better served by a technical diagram. Consider using the `create-diagram` skill for this image, which can produce an accurate timeline with proper Red Hat branding.

### 3. No CTA-style visual in "Get started" section

Flagged in v1. Lines 74-80 contain the closing CTA but no visual anchor. A branded CTA block or banner image would draw the reader's eye to the trial link. This is lower priority than the hero image and roadmap visual — the editorial team often adds CTA styling during Drupal production — but specifying one gives the author control over the visual close.

## v1 Issue Resolution Tracker

| v1 Issue | Status in v2 |
|---|---|
| Prompt ambiguity: screenshot vs. mockup | Fixed — conditional prompt structure |
| #F5F5F5 not in official palette | Fixed — changed to #F0F0F0 |
| Missing card count and layout details | Fixed — 3x2 grid specified |
| Missing card field specifications | Fixed — name, logo area, description, tier badge, deploy button |
| Missing sidebar navigation items | Fixed — "AI Hub > MCP Catalog" specified |
| Missing typography references | Fixed — Red Hat Text and Red Hat Display specified |
| Concentric ring layout cluttered | Fixed — changed to horizontal three-column layout |
| Alt text describes "what" not "why" | Fixed — both alt texts now convey purpose |
| Missing hero image | Not addressed |
| Missing roadmap/timeline visual | Not addressed |
| Missing CTA visual in "Get started" | Not addressed |

## Summary

v2 shows meaningful improvement in prompt specificity, brand compliance and alt text quality — the three weakest areas from v1. Both existing prompts are now substantially more detailed and actionable: Image 1 disambiguates screenshot vs. mockup, specifies card layout and fields, and fixes the off-palette color; Image 2 replaces the cluttered concentric ring concept with a clean three-column layout. Alt text across both images now communicates purpose, not just visual content. The primary gap remaining is image count and coverage: v1 identified three missing image opportunities (hero image, roadmap visual, CTA visual) and none have been added in v2. Adding at least the hero image and the roadmap timeline — the two that most directly aid reader comprehension — would move the image count score up and provide meaningful visual support for the post's weakest visual sections (the opening and the forward-looking close). The roadmap visual in particular is a strong candidate for the `create-diagram` skill.
