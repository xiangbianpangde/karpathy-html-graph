# Default Style — Claude/Anthropic palette (v2.0 default)

Use these CSS variables as the default when the project does not have its own
`design-graph.html` or similar style template. If a reference is provided,
extract its `:root` tokens instead and skip this file.

**v2.0 change**: Claude/Anthropic warm palette is the new default. The academic
cream + primary-color contrast palette is still available as an alternative
("academic" style) — see "Alternative palettes" at the bottom.

## CSS variables (`:root`) — Claude/Anthropic default

```css
:root {
  --bg: #faf9f5;          /* warm cream (Claude main bg) */
  --paper: #ffffff;       /* pure white panels (masthead, drawer, filter) */
  --ink: #1f1f1e;         /* warm dark text (slightly off-black) */
  --ink-soft: #6b6b66;    /* warm gray (NOT cool blue-gray) */
  --rule: #e8e3d7;        /* warm tan divider */
  --rule-soft: #f3eee2;   /* lighter cream */
  --orange: #d97757;      /* Anthropic orange (terracotta) — brand accent */
  --orange-soft: #e6a08b; /* light orange */
  --rust: #a8482b;        /* deep rust red */
  --tan: #c2a878;         /* warm tan */
  --bronze: #b08552;      /* warm bronze */
  --gold: #a88865;        /* muted gold */
  --coffee: #3d2f24;      /* dark coffee */
  --brown: #5a4a3e;       /* warm brown */
  --plum: #9c7a9c;        /* muted plum (sparingly) */
  --serif: "Source Serif Pro", "Noto Serif SC", Georgia, "Times New Roman", serif;
  --sans:  "Inter", "PingFang SC", "Microsoft YaHei", system-ui, -apple-system, sans-serif;
  --mono:  "JetBrains Mono", "SF Mono", Consolas, Menlo, monospace;
}
```

## Node palette — 5 warm shades (Claude hierarchy)

| Kind | Color | Hex | Luminance | Text color |
|------|-------|-----|-----------|------------|
| `goal` (L1 top) | Anthropic orange | `#d97757` | 0.567 | dark |
| `concept` (L1) | warm tan | `#c2a878` | 0.668 | dark |
| `subcap` / `method` | bronze | `#b08552` | 0.549 | dark |
| `metric` / `anchor` | muted gold | `#a88865` | 0.555 | dark |
| `code` | deep rust | `#a8482b` | 0.382 | white |
| `action` | warm sienna | `#b8643a` | 0.472 | white |
| `state` | dark coffee | `#3d2f24` | 0.196 | white |
| `rule` | warm brown | `#5a4a3e` | 0.304 | white |

Use `computeTextColor(bg)` for auto-contrast (luminance threshold 0.55):
- luminance > 0.55 → dark text (`#1f1f1e`)
- luminance ≤ 0.55 → white text (`#ffffff`)
- Add `text-outline-color` (opposite) at 0.3px / 15% opacity for crispness on any bg.

## Four-pane layout (CSS sketch)

```
┌──────────────────────────────────────────────────────────┐
│ MASTHEAD: title + tabs + meta pills      (fixed top, 152px)│
├────────────┬─────────────────────────────────┬───────────┤
│            │                                 │           │
│  FILTER    │        CYTOSCAPE CANVAS          │  DRAWER   │
│  PANEL     │        (nodes + edges,           │  (360px,  │
│  (200px)   │         pre-computed positions)  │  slide-in)│
│            │                                 │           │
└────────────┴─────────────────────────────────┴───────────┘
```

CSS positions (fixed):
- Masthead: `top: 0; left: 0; right: 0; height: 152px;`
- Filter:   `top: 152px; left: 0; width: 200px; bottom: 0;`
- Canvas:   `top: 152px; left: 200px; right: 0; bottom: 0;`
- Drawer:   `top: 152px; right: 0; width: 360px; bottom: 0; transform: translateX(100%);`
- Drawer.open: `transform: translateX(0);`

## Cytoscape node shapes (semantic)

| Kind | Shape |
|------|-------|
| `goal` / `concept` | round-rectangle (large) |
| `subcap` / `method` | round-rectangle |
| `metric` / `anchor` | diamond (only non-rectangle) |
| `code` / `action` | round-rectangle |
| `state` | ellipse (only ellipse, for visual variety) |
| `rule` | round-rectangle (small) |

Keep shapes mostly uniform (round-rectangle) for Claude's clean minimalism. Use diamond only for time/measurement points and ellipse only for dynamic states.

## Edge semantics (v2.0 — minimal)

| Aspect | Value |
|--------|-------|
| Color | `#c5beac` (very light tan) |
| Opacity | 0.55 |
| Width | 1.2px |
| Arrow shape | triangle |
| Curve | bezier |
| **Labels** | **NONE by default** — too cluttered in v1.0 |

## Tab visual marker (core tabs)

Tabs 2 (设计方法) and 3 (概念→设计→代码) are the focal tabs. Mark them in the
tab strip with a `⭐` glyph in `--orange` color, so the user can find them
at a glance. Active tab has 2.5px bottom border in `--orange` (`#d97757`).

## Alternative palettes

If the user prefers academic or minimal style instead, override the palette:

### Academic (v1.0 default — still available)

```css
:root {
  --bg: #fafaf6;          /* cream paper */
  --paper: #ffffff;
  --ink: #1a1a1a;
  --ink-soft: #4a4a4a;
  --rule: #d4cfc3;
  --rule-soft: #ebe7dc;
  --navy: #1e3a5f;        /* primary */
  --sienna: #a8482b;      /* accent */
  --forest: #3a6b3e;
  --plum: #6a3a7a;
  --gold: #b08428;
  --warn: #b04a2b;
}
```

Node palette (5 contrasting primaries): navy/sienna/forest/plum/gold.

### Minimal (light + 1 accent)

```css
:root {
  --bg: #ffffff;          /* pure white bg */
  --paper: #fafafa;       /* light gray panel */
  --ink: #0a0a0a;
  --ink-soft: #6a6a6a;
  --rule: #e5e5e5;
  --rule-soft: #f0f0f0;
  --accent: #2563eb;      /* single blue accent */
  --serif: "Inter", system-ui, -apple-system, sans-serif;
  --sans:  "Inter", system-ui, -apple-system, sans-serif;
  --mono:  "JetBrains Mono", monospace;
}
```

Node palette: shades of gray + 1 accent. Most restrained, looks like Linear/Vercel.
