---
name: create-diagram
description: Create or iterate on Cytoscape.js architecture diagrams optimized for executive presentations on projectors. Covers setup, styling, node/edge patterns, review scoring, and iterative improvement.
---

# Create Architecture Diagram

Build interactive Cytoscape.js architecture diagrams with a dark theme, projector-optimized typography, and layered swim-lane structure. This skill covers initial creation, iterative review, and the specific patterns that survive executive scrutiny.

## When to Use

- Creating a new system architecture diagram from scratch
- Iterating on an existing Cytoscape.js diagram to improve quality
- Running a review/improve cycle on a diagram version

## Phase 1: Setup

### File Structure

Each diagram version is a self-contained HTML file in `sys-arch-diagram/`:

```
sys-arch-diagram/
  v1.html          # first version
  v1-screenshot.png # Playwright capture
  v2.html          # next iteration
  v2-screenshot.png
  ...
```

### HTML Skeleton

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AI Asset Registry — System Architecture v{N}</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Red+Hat+Display:wght@400;500;600;700;900&family=Red+Hat+Text:wght@400;500;600;700&family=Red+Hat+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/cytoscape@3.30.4/dist/cytoscape.min.js"></script>
<!-- CSS + body + script -->
</head>
```

### Required CSS

```css
body {
  font-family: 'Red Hat Text', -apple-system, sans-serif;
  background: #151515;
  color: #d2d2d2;
}
#cy-container {
  width: 100%; max-width: 1200px;
  background: #1a1a1a;
  border: 1px solid #2d2d2d;
  border-radius: 8px;
  overflow: hidden; position: relative;
}
#cy-container::before {  /* red accent bar */
  content: ''; position: absolute;
  top: 0; left: 0; right: 0; height: 3px;
  background: linear-gradient(90deg, #EE0000, #A30000, #EE0000);
  z-index: 10;
}
#cy { width: 100%; height: 1050px; }
```

### Cytoscape Instance Config

```javascript
const cy = cytoscape({
  container: document.getElementById('cy'),
  userZoomingEnabled: false,   // ALWAYS false for presentations
  userPanningEnabled: false,   // ALWAYS false for presentations
  boxSelectionEnabled: false,
  autounselectify: true,
  layout: {
    name: 'preset',            // ALWAYS preset — manual coordinates
    padding: 40,               // tight padding, no wasted space
    animate: true,
    animationDuration: 600,
    animationEasing: 'ease-out',
    fit: true,                 // auto-scale to container
  },
});
```

**Critical**: `fit: true` scales the diagram to fill the container. This means the bounding box of all nodes determines the zoom level. Adding invisible nodes far from the content (like anchor nodes) will shrink everything. Only place nodes where they need to be visible.

---

## Phase 2: Grid System & Node Positioning

### Column Grid

Use a strict column grid for horizontal alignment. All nodes in a layer should snap to grid columns:

```javascript
const CX = 500;                          // center X
const COL_SPACING = 190;                 // equal spacing
const COL1 = CX - 1.5 * COL_SPACING;    // ~215
const COL2 = CX - 0.5 * COL_SPACING;    // ~405
const COL3 = CX + 0.5 * COL_SPACING;    // ~595
const COL4 = CX + 1.5 * COL_SPACING;    // ~785
```

For 3-column layers (like Platform), use a SPREAD constant from center:

```javascript
const SPREAD = 250;
// Left: CX - SPREAD, Center: CX, Right: CX + SPREAD
```

**Rule**: If two layers share the same column positions (e.g., Platform and Consumption), use the same SPREAD constant so nodes align vertically.

### Vertical Layer Spacing

Define Y constants for each horizontal layer. Spacing should be proportional to content complexity:

```javascript
const Y_SOURCES     = 55;    // top
const Y_MERGE       = 120;   // funnel point
const Y_VALIDATION  = 195;   // intake processing
const Y_GOVERNANCE  = 310;   // central hub (dominant)
const Y_PLATFORM    = 650;   // implementation layer
const Y_CONSUMPTION = 790;   // bottom
```

**Rule**: The gap between layers that have feedback loops (Governance-to-Platform) must be larger (300-340px) than gaps between simpler layers (130-145px). Feedback routing needs vertical room.

### Node Types

Define semantic `nodeType` values in node data for styling:

| nodeType | Purpose | Example |
|----------|---------|---------|
| `source` | Input sources | Internal Dev, ISV Partners |
| `merge` | Invisible funnel point | 10x10 dot |
| `phase2` | Future/planned items | Dashed border, reduced opacity |
| `registry` | Central dominant element | Largest, red-bordered |
| `blue` | Discovery/catalog | Blue-tinted |
| `purple` | Deployment/operator | Purple-tinted |
| `teal` | Runtime/gateway | Teal-tinted |
| `green` | Consumption/output | Green-tinted |

---

## Phase 3: Styling

### Font Hierarchy (Projector-Optimized)

These sizes are the MINIMUM for legibility at 20+ foot viewing distance on a projector:

| Element | Font Family | Size | Weight | Color |
|---------|-------------|------|--------|-------|
| Node text (base) | Red Hat Text | **15px** | 500 | #e8e8e8 |
| Node text (hero) | Red Hat Display | **18px** | 700 | #fff |
| Data flow labels | Red Hat Text | **16px** | 700 | #fff |
| Source/governed labels | Red Hat Text | **15px** | 600 | #fff |
| Feedback labels | Red Hat Text | **15px** | 600 | #D4954A |
| Layer labels | Red Hat Mono | **15px** | 600 | #ccc |
| Legend | Red Hat Mono | **14px** | 400 | #bbb |

**Rule**: Never go below 14px for any visible text. 13px and below are unreadable on projectors.

### Color Palette

**Backgrounds**:
- Canvas: `#1a1a1a`
- Body: `#151515`
- Default node: `#2d2d2d` bg, `#4a4a4a` border

**Semantic node colors** (dark-on-dark with bright text):
- Registry (hero): `#401515` bg, `#cc0000` border, `#fff` text
- Blue (catalog): `#152f4c` bg, `rgba(0,102,204,0.7)` border, `#8ac4ff` text
- Purple (lifecycle): `#2c2750` bg, `rgba(103,83,172,0.7)` border, `#ccb8ff` text
- Teal (gateway): `#183a3a` bg, `rgba(0,149,150,0.7)` border, `#70f0f0` text
- Green (consumption): `#1a3218` bg, `rgba(62,134,53,0.7)` border, `#90e088` text
- Phase 2+: `rgba(103,83,172,0.15)` bg, `rgba(103,83,172,0.70)` border, `#d0c8f8` text, opacity 0.85

**Edge colors**:
- Data flow (primary): `#cc0000` (red), 3.5px width
- Source flow: `#707070` (gray), 1.5px width
- Feedback: `#D4954A` (amber), 1.5px width, dashed, opacity 0.6
- Governed: `#aaa` (light gray), 2px width

### Edge Width Hierarchy

This is critical for visual hierarchy. Wider = more important:

| Edge Type | Width | Arrow Scale | Purpose |
|-----------|-------|-------------|---------|
| Data flow | **3.5px** | 1.3 | Primary distribution — DOMINANT |
| Governed | **2px** | 1.0 | Access control |
| Source flow | **1.5px** | 0.8 | Intake paths |
| Feedback | **1.5px** | 0.8 | Telemetry/status — RECESSIVE |

### Node Style Definitions

```javascript
// Base node
{ selector: 'node', style: {
    'shape': 'round-rectangle', 'corner-radius': 10,
    'width': 200, 'height': 75,
    'background-color': '#2d2d2d', 'border-width': 1.5, 'border-color': '#4a4a4a',
    'label': 'data(label)', 'text-valign': 'center', 'text-halign': 'center',
    'font-family': '"Red Hat Text", sans-serif', 'font-size': 15, 'font-weight': 500,
    'color': '#e8e8e8', 'text-wrap': 'wrap', 'text-max-width': 185, 'line-height': 1.35,
}},

// Merge point (invisible funnel)
{ selector: 'node[nodeType="merge"]', style: {
    'width': 10, 'height': 10, 'background-color': '#999',
    'border-width': 0, 'shape': 'ellipse'
}},

// Source nodes (smaller)
{ selector: 'node[nodeType="source"]', style: {
    'width': 190, 'height': 55, 'font-size': 15, 'text-max-width': 175,
    'color': '#f5f5f5', 'border-color': '#555', 'border-width': 1
}},

// Phase 2+ (dashed, recessed)
{ selector: 'node[nodeType="phase2"]', style: {
    'width': 220, 'height': 55, 'border-style': 'dashed',
    'border-color': 'rgba(103, 83, 172, 0.70)',
    'background-color': 'rgba(103, 83, 172, 0.15)',
    'color': '#d0c8f8', 'opacity': 0.85, 'font-size': 14, 'text-max-width': 205,
}},

// Registry (hero element)
{ selector: 'node[nodeType="registry"]', style: {
    'width': 460, 'height': 85, 'background-color': '#401515',
    'border-color': '#cc0000', 'border-width': 2.5,
    'font-family': '"Red Hat Display", sans-serif', 'font-size': 18, 'font-weight': 700,
    'color': '#fff', 'text-max-width': 440, 'line-height': 1.4,
}},
```

---

## Phase 4: Edge Routing

### Downward Flows (Data Flow, Source Flow, Governed)

Use **taxi routing** with `downward` direction. This creates clean right-angle paths:

```javascript
// Base edge
{ selector: 'edge', style: {
    'width': 1.5, 'line-color': '#555', 'target-arrow-color': '#555',
    'target-arrow-shape': 'triangle', 'arrow-scale': 1.0,
    'curve-style': 'taxi', 'taxi-direction': 'downward',
    'taxi-turn': 20, 'taxi-turn-min-distance': 10,
}},

// Data flow (RED, dominant)
{ selector: 'edge[edgeType="data-flow"]', style: {
    'width': 3.5, 'line-color': '#cc0000', 'target-arrow-color': '#cc0000',
    'arrow-scale': 1.3,
    'curve-style': 'taxi', 'taxi-direction': 'downward',
    'taxi-turn': 50, 'taxi-turn-min-distance': 20,
    'target-label': 'data(label)',
    'target-text-margin-y': -32, 'target-text-offset': 1,
    'font-size': 16, 'font-weight': 700, 'color': '#fff',
}},
```

Tune `taxi-turn` per edge to control where the right-angle bend occurs:
- Center edges: `taxi-turn: 50` (wider turn)
- Side edges: `taxi-turn: 40` (tighter turn)

### Feedback Loops (Upward)

**Use taxi routing with `upward` direction and negative taxi-turn values.** This creates clean rectangular upward paths that stay out of the way of downward data flow.

```javascript
// Feedback base style
{ selector: 'edge[edgeType="feedback"]', style: {
    'width': 1.5, 'line-color': '#D4954A',
    'line-style': 'dashed', 'line-dash-pattern': [8, 5],
    'target-arrow-color': '#D4954A', 'arrow-scale': 0.8,
    'opacity': 0.6,
    'curve-style': 'taxi', 'taxi-direction': 'upward',
    'taxi-turn': 80,                    // base horizontal offset
    'taxi-turn-min-distance': 15,
    'label': 'data(label)',
    'font-size': 15, 'font-weight': 600,
    'color': '#D4954A',                 // label matches line color
}},

// Per-edge overrides for separation
{ selector: 'edge[id="fb-left"]',   style: { 'taxi-turn': -130 } },  // far left
{ selector: 'edge[id="fb-center"]', style: { 'taxi-turn': -50 } },   // near center
{ selector: 'edge[id="fb-right"]',  style: { 'taxi-turn': -130 } },  // far right
```

**Critical lessons learned about feedback routing**:

1. **DO NOT use `unbundled-bezier`** for feedback arcs in complex diagrams. Bezier control points (`control-point-distances`, `control-point-weights`, `source-endpoint`, `target-endpoint`) are fragile — any node position change requires recalculating all control points. They also create sweeping curves that visually dominate the diagram.

2. **DO use `taxi` with `upward` direction**. Right-angle routing is cleaner, more predictable, and doesn't need manual control point math.

3. **Negative taxi-turn values** route the horizontal segment to the opposite side of the edge direction (left for left-side nodes, right for right-side nodes).

4. **Separate feedback horizontal crossings from data-flow** by using large taxi-turn values (80-130). This pushes the horizontal segment far enough from the data-flow lines to be visually distinct.

5. **Make feedback text yellow** (`#D4954A`) to match the line color. This distinguishes feedback labels from the white data-flow labels.

### DO NOT use anchor nodes

Never add invisible "anchor" nodes to expand the bounding box. With `fit: true`, anchor nodes at extreme positions cause Cytoscape to zoom out, shrinking all text and nodes. This is the single most common cause of "text looks tiny" problems.

---

## Phase 5: Layer Labels & Swim Bands

### Layer Labels (HTML overlay)

Position labels dynamically after Cytoscape layout completes:

```javascript
cy.on('layoutstop', function() {
  const labelsEl = document.getElementById('layer-labels');
  labelsEl.innerHTML = '';

  const layers = [
    { nodeId: 'src-internal',  text: 'SOURCES' },
    { nodeId: 'val-pipeline',  text: 'VALIDATION' },
    { nodeId: 'registry',      text: 'GOVERNANCE', cls: 'governance' },
    { nodeId: 'catalog',       text: 'PLATFORM' },
    { nodeId: 'studio',        text: 'CONSUMPTION' },
  ];

  const positions = [];
  layers.forEach(({ nodeId, text, cls }) => {
    const node = cy.getElementById(nodeId);
    const pos = node.renderedPosition();
    positions.push(pos.y);
    const label = document.createElement('div');
    label.className = 'layer-label' + (cls ? ' ' + cls : '');
    label.textContent = text;
    label.style.top = (pos.y - 6) + 'px';
    labelsEl.appendChild(label);
  });

  // Swim-lane bands (alternating subtle stripes)
  const containerH = document.getElementById('cy').offsetHeight;
  const bandTops = [0];
  for (let i = 0; i < positions.length - 1; i++) {
    bandTops.push((positions[i] + positions[i + 1]) / 2);
  }
  bandTops.push(containerH);

  for (let i = 0; i < bandTops.length - 1; i++) {
    const band = document.createElement('div');
    band.className = 'swim-band' + (i % 2 === 0 ? ' even' : '');
    band.style.top = bandTops[i] + 'px';
    band.style.height = (bandTops[i + 1] - bandTops[i]) + 'px';
    labelsEl.appendChild(band);
  }
});
```

Swim band CSS:
```css
.swim-band.even { background: rgba(255, 255, 255, 0.07); }
```

---

## Phase 6: Legend

Always include a legend bar at the bottom of the diagram container:

```html
<div class="legend">
  <span style="letter-spacing: 0.1em; font-weight: 600; color: #999;">LEGEND:</span>
  <div class="legend-item"><div class="legend-line data-flow"></div><span>Data flow</span></div>
  <div class="legend-item"><div class="legend-line feedback"></div><span>Feedback loop</span></div>
  <div class="legend-item"><div class="legend-line governed"></div><span>Governed access</span></div>
  <div class="legend-item"><div class="legend-box"></div><span>Phase 2+</span></div>
  <span style="margin-left: auto; font-size: 13px; color: #999;">Author — Date</span>
</div>
```

---

## Phase 7: Review & Iteration

### Iteration Workflow

1. **Copy** the current version to a new file: `vN.html` -> `v(N+1).html`
2. **Screenshot** via Playwright at `http://127.0.0.1:8902/sys-arch-diagram/v(N+1).html`
3. **Review** using the scoring criteria below
4. **Apply fixes** to `v(N+1).html`
5. **Re-screenshot** and verify
6. Repeat until score exceeds 70/90

### Scoring Criteria (6 dimensions, weighted)

| Criterion | Weight | What to Check |
|-----------|--------|---------------|
| **Readability** | 2x | Font sizes >= 14px, no label overlap, sufficient contrast, no clipping at edges |
| **Alignment & Symmetry** | 2x | Grid alignment, balanced layer spacing, symmetric node placement |
| **Connector Quality** | 2x | No confusing overlaps, red lines dominant, feedback recessive, labels positioned cleanly |
| **Visual Hierarchy** | 1x | Hero element commands attention, top-to-bottom flow obvious, phase 2+ recedes |
| **Flow Clarity** | 1x | Viewer can trace full path without confusion, feedback distinguishable from primary flow |
| **Presentation Polish** | 1x | Legend complete, layer labels visible, swim bands subtle, professional impression |

**Scoring**: Each criterion 1-10, multiply by weight, sum for total out of 90. Target: 70+.

### Common Problems and Fixes

| Problem | Cause | Fix |
|---------|-------|-----|
| All text renders tiny | Anchor nodes expanding bounding box | Remove all invisible anchor nodes |
| Feedback lines cross data flow | Bezier curves routing through center | Switch to taxi upward with negative taxi-turn |
| Labels overlap nodes | text-margin values too small | Increase text-margin-y to -32 for target-labels |
| "CONSUMPTION" label clips | Left padding too small | Increase .layer-label left from 18px to 28px |
| Feedback zone cluttered | All 3 feedback lines share same horizontal band | Use different taxi-turn values per edge (-130, -50, -130) |
| Swim bands invisible | Opacity too low | Use rgba(255,255,255,0.07) minimum |
| Text backgrounds too heavy | Dark pill behind every label | Remove text-background-* properties for cleaner look |

### What NOT to Change Between Iterations

- Color palette (it was optimized once and is stable)
- Base node dimensions (they match the font hierarchy)
- Edge type visual hierarchy (width ratios are deliberate)
- Font families (Red Hat Display/Text/Mono is the design system)

### What TO Tune Between Iterations

- `taxi-turn` values (control right-angle bend positions)
- `text-margin-x` / `text-margin-y` (control label positions relative to edges)
- Y-coordinate spacing between layers (if feedback needs more room)
- Individual node X positions (for optical balance)
- Opacity values on secondary elements (if they compete with primary flow)
