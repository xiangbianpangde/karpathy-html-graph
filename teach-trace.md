---
name: teach-trace
description: Generate a single-file HTML Cytoscape.js graph that traces how a "48h learning" (or similar short-cycle teaching) system is implemented. Supports academic, Claude/Anthropic, and minimal style palettes. v2.0 — Chinese-first labels, mind-map layout by default, auto-contrast text on colored nodes.
runAs: subagent
---

# Teach-Trace v2.0 — 48h Learning Implementation Graph

Generate a single self-contained HTML file that visualizes how a teaching/learning
system is built — from business concept, through design methods (Feynman, scaffolding,
cognitive model, FSRS, etc.), down to code modules and key files. Single file, inlined
CSS/JS, only Cytoscape CDN as external dep.

## v2.0 Changelog (vs v1.0)

| Issue in v1.0 | Fix in v2.0 |
|----------------|-------------|
| Tab 3/4 didn't render — preset layout callback referenced old `cy.nodes()` getting empty positions | **Pre-compute positions BEFORE creating cytoscape**; set via `position: {x, y}` on each element; use `{name: 'preset', fit: true, padding: N}` directly |
| Auto-laid-out graphs felt "scattered" (用户反馈:现在很散乱) | **Mind-map style by default** — root center, branches radiating in 4-5 fixed directions; positions hard-coded in `computePositions()` |
| English labels everywhere (用户反馈:前两幅图太多英文) | **Chinese-first labels**; English acronyms (FSRS, NLI, BKT, DKT, PDF) OK; technical terms translate to Chinese first, keep English in parens if needed |
| Color scheme looked "ugly" — primary-color contrast (navy/forest/plum/gold) too academic | **Claude/Anthropic style by default** — warm cream `#faf9f5` bg + Anthropic orange `#d97757` + warm brown hierarchy (5 shades of tan/bronze/rust) instead of contrasting primaries |
| Dark colored nodes had hard-to-read dark text (用户反馈:图里面的字建议使用白色) | **Auto-contrast text color** — luminance threshold (0.55) on background, white on dark nodes, dark on light nodes; add subtle text outline (0.3px, 15% opacity, opposite color) for crispness |
| Edge labels overlapped and cluttered the view | **Suppress edge labels by default**; only show for important edges or on hover (out of v2.0 scope — wire up on demand) |
| Reference docs split between MCP original and V3.1 design rewrite — only checking V3.1 missed rich details (6 strategies, BKT+DKT, cognitive load, PGFGA flow) | **Check BOTH sources** when available; V3.1 design rewrite is often lossy; MCP original `48H-SIMULATION-v2.md` style docs are the gold standard for scenario detail |
| Tab 4 timeline with all states/actions at same x=720 looked like a vertical line | **Spread states/actions along x** — anchors x=t (linear), states evenly spaced, actions in 2 rows distributed along x |
| User often wants to iterate (color → layout → text contrast) | **Pre-compute positions** (not auto-layout) makes iteration easy; also: pre-style CSS variables for one-touch palette swap |

---

## Inputs to collect

- **Project path** (required) — root directory of the project being visualized.
  Read `<project>/CLAUDE.md` or `<project>/README.md` first if present to confirm layout.
- **Reference design HTML** (optional) — if the user has a styled graph (e.g. `<project>/docs/plan/design-graph.html`) that they want to imitate, point to it. Extract its `:root` CSS variables.
- **48h learning entry hint** (optional) — if the user already knows which module/file is the entry point of the teaching flow, name it. Otherwise the skill searches `docs/plan/design/` for the four typical core modules automatically.
- **Style preference** (optional, v2.0) — `academic` (default, warm cream + Source Serif Pro), `claude` (Anthropic palette), or `minimal` (light gray + 1 accent). If unspecified, default to `claude` (most universally appealing for AI-product audiences).
- **Language preference** (optional, v2.0) — `zh` (default for Chinese projects), `en`, or `bilingual` (Chinese label + English acronym). Default to `zh` when `<project>/CLAUDE.md` is in Chinese.

## Procedure

### Step 1 — Detect environment
- Read `<project>/CLAUDE.md` or `<project>/README.md` for project layout.
- If `<project>/docs/plan/design-graph.html` (or similar) exists, read it as the style reference and extract its `:root` palette. Otherwise use the **default Claude palette** (see `references/default-style.md`).
- Check both **MCP original** (e.g. `<project-parent>/ai-tutor/doc/`) and **V3.1 design rewrite** (e.g. `<project>/docs/plan/design/`) for scenario docs. The original is often richer.

### Step 2 — Locate the 4 core teaching modules
Search `<project>/docs/plan/design/` for module design docs (`FF-M-XXX-*.md`,
`DD-M-XXX-*.md`, or similar naming). The four core modules of a 48h learning
system are typically:

| Role | Example ID | Responsibility |
|------|-----------|----------------|
| Teaching orchestration | M-009 | Overall round scheduling, method selection |
| Feynman scoring | M-010 | LLM-based explanation quality scoring |
| FSRS scheduler | M-011 | py-fsrs spaced repetition, Again throttling |
| Stage calibration | M-012 | Hysteresis-based level up/down |

If module IDs differ, ask the user once to confirm the four roles; do not guess.

### Step 3 — Read design documents
For each core module, read its:
- Module design doc (FF or DD markdown) — interface contract + key functions
- Module plan (MP markdown) — dependencies + responsibilities
- Relevant ADR / TDR / DDR (Architecture / Technical / Detailed Design Records)

**ALSO read the corresponding MCP original** (if it exists). The MCP version often has:
- Richer scenario details (cold start, error recovery, time calibration)
- Optimization history ("优化 #1, #2, ...")
- Concrete data flow examples

Extract a 3-layer map (this is the heart of the graph):
- **L1 业务概念** — 48h learning goals, sub-capabilities, user scenarios
- **L2 设计方法** — Feynman, scaffolding, cognitive model, FSRS, rest, recall, etc.
- **L3 代码实现** — Module IDs + key files + key functions (with `file:line`)

### Step 4 — Build the 4-tab data structure

See `references/tab-schemas.md` for the exact node/edge shape per tab:

- **Tab 1 · 学习能力图谱** — L1 nodes, parent-child decomposition + lateral support
- **Tab 2 · 设计方法图谱** ⭐ — L2 nodes, method-to-method relationships
- **Tab 3 · 概念→设计→代码** ⭐⭐ — all 3 layers, preset 3-column layout
- **Tab 4 · 认知旅程** — timeline of 48h learning + cognitive state + system response

**v2.0 additions to data shape:**

- **Chinese-first labels** — all `label` fields in Chinese by default; keep English acronyms (FSRS, NLI, BKT, DKT, PDF, API) and code identifiers (`M-009`, `orchestrator.py`) as-is.
- **L3 nodes must have a real `file:line` reference** — point to design docs (e.g. `FF-M-009-AITutor-V3.1-20260602.md:16-21`) when project is in design phase, or to source code when implemented.
- **Per-tab constraints**: 20-40 nodes, ≤80 edges.

### Step 5 — Confirm outline with user (CRITICAL — do not skip)

Print for each tab: `"Tab N: X 节点, Y 边. 样例: [id,label,ref]"` and wait for
explicit approval before writing the file. This catches direction errors early
— much cheaper than regenerating a 2000-line HTML.

**Watch for these user pushback patterns from v1.0 → v2.0 sessions:**
- "过于精简" / "太少了" → expand the timeline with 6 strategies / BKT+DKT / cognitive load / PGFGA flow / interrupt handling
- "现在很散乱" → switch to pre-computed mind-map layout, not auto-layout
- "前两幅图太多英文" → translate labels to Chinese
- "现在很丑" / "类似的claude" → switch palette to warm cream + Anthropic orange
- "建议使用白色" → enable auto-contrast text color

If the user says "可以" or similar single-word approval, treat as "ship it" with current outline.

### Step 6 — Generate the single HTML file

**Path**: `<project>/docs/plan/48h-learning-graph.html` (default; let user override).

**Critical v2.0 pattern — pre-compute positions in JS, NOT in layout callback:**

```js
// GOOD — positions set on elements, then preset layout respects them
function computePositions(tabId) { /* hard-coded x/y per node */ return pos; }
function buildElements(tabId) {
  const positions = computePositions(tabId);
  return {
    nodes: nodes.map(n => ({ data: {...}, position: positions[n.id] })),
    edges: edges.map(...)
  };
}
cy = cytoscape({
  elements: [...elems.nodes, ...elems.edges],
  layout: { name: 'preset', fit: true, padding: 50 },  // just respects existing positions
  ...
});

// BAD — referencing cy inside layout callback gets the OLD cytoscape
cy = cytoscape({
  layout: { name: 'preset', positions: (() => {
    const pos = {};
    cy.nodes().forEach(n => pos[n.id()] = n.position());  // <-- cy is undefined here!
    return pos;
  })()},
  ...
});
```

**Layout patterns (v2.0):**

- **Tab 1 / Tab 2 — Mind-map (default)**:
  - Root at canvas center (e.g. `cx=700, cy=450`)
  - 4-5 main branches in cardinal/diagonal directions
  - Sub-branches cluster around each main branch's anchor point
  - Avoid crossing edges; group nodes by phase/role

- **Tab 3 — 3-column preset**:
  - L1 at x=180, L2 at x=600, L3 at x=1080
  - y positions stacked per column
  - L1 → L2 dashed lines (implementedBy), L2 → L3 dashed (realizedIn)

- **Tab 4 — Timeline preset**:
  - Anchors on top row, x = t (linear scale)
  - States in middle row, evenly distributed along x
  - Actions on bottom row(s), distributed in 2 rows along x
  - Avoid stacking all states/actions at same x

**Style palette (v2.0 default = Claude/Anthropic):**

```css
:root {
  --bg: #faf9f5;          /* warm cream (Claude main bg) */
  --paper: #ffffff;       /* pure white panels */
  --ink: #1f1f1e;         /* warm dark text */
  --ink-soft: #6b6b66;    /* warm gray */
  --rule: #e8e3d7;        /* warm tan divider */
  --rule-soft: #f3eee2;   /* lighter cream */
  --orange: #d97757;      /* Anthropic orange (terracotta) */
  --orange-soft: #e6a08b; /* light orange */
  --rust: #a8482b;        /* deep rust */
  --tan: #c2a878;         /* warm tan */
  --bronze: #b08552;      /* warm bronze */
  --gold: #a88865;        /* muted gold */
  --coffee: #3d2f24;      /* dark coffee */
  --brown: #5a4a3e;       /* warm brown */
  --serif: "Source Serif Pro", "Noto Serif SC", Georgia, "Times New Roman", serif;
  --sans:  "Inter", "PingFang SC", "Microsoft YaHei", system-ui, -apple-system, sans-serif;
  --mono:  "JetBrains Mono", "SF Mono", Consolas, Menlo, monospace;
}
```

**Node palette (5 shades of warm brown hierarchy, NOT contrasting primaries):**

| Kind | Color | Use case |
|------|-------|----------|
| `goal` (L1 top) | `#d97757` Claude orange | Most prominent — root / highlight |
| `concept` (L1) | `#c2a878` warm tan | Business concepts |
| `subcap` / `method` (L1/L2) | `#b08552` bronze | Most common nodes |
| `metric` / `anchor` (L1/4) | `#a88865` muted gold | Time/measurement points |
| `code` / `action` (L3) | `#a8482b` / `#b8643a` rust/sienna | Code and system actions |
| `state` / `rule` | `#3d2f24` / `#5a4a3e` coffee/brown | Lowest-contrast, stable |

**Auto-contrast text color (v2.0 NEW):**

```js
'color': (ele) => {
  const bg = KIND_COLORS[ele.data('kind')];
  const hex = bg.replace('#', '');
  const r = parseInt(hex.substring(0,2),16);
  const g = parseInt(hex.substring(2,4),16);
  const b = parseInt(hex.substring(4,6),16);
  const lum = (0.299*r + 0.587*g + 0.114*b) / 255;
  return lum > 0.55 ? '#1f1f1e' : '#ffffff';  // bright bg → dark text, dark bg → white text
},
'text-outline-color': (ele) => /* opposite of color */,
'text-outline-width': 0.3,
'text-outline-opacity': 0.15,
```

**Edge style (v2.0 — minimal):**
- Color: `#c5beac` (very light tan)
- Opacity: 0.55
- Arrow shape: triangle
- NO labels by default (was a major clutter source in v1.0)

**Layout — top masthead (158px) / left filter (200px) / center canvas / right drawer (360px) / footer.**

Wire interactions: `tap` → open drawer with detail + `file:line` ref; tab click → rebuild graph; checkbox filter → toggle `.filtered-out` class on node kinds.

### Step 7 — Self-verify

- Open the file in a headless check (or just review the script): all 4 tabs switch without throwing.
- Every node has a non-empty drawer body and at least one `file:line` reference.
- All references point to files that actually exist in the project (grep through `<project>/` to confirm). If project is in design phase, references can point to design docs in `docs/plan/design/`.
- No console errors when loading.
- **v2.0 specific checks**:
  - Text is readable on every node (white on dark, dark on light — verify with eyeball or contrast checker)
  - Mind-map layout looks clean (no scattered nodes, no crossing edges in default position)
  - Tab 4 timeline has states/actions spread along x, not all stacked at one x value

## Output contract (v2.0)

- One HTML file: `48h-learning-graph.html` (default path shown above).
- Self-contained: inline CSS/JS, only Cytoscape CDN (`https://unpkg.com/cytoscape@3.30.2/dist/cytoscape.min.js`).
- ≥4 tabs covering L1 / L2 / L3 mapping, with Tab 2 and Tab 3 visually marked as core (⭐ glyph).
- Every node → drawer shows: kind / label / role / `file:line` ref / key quote.
- **v2.0 defaults**: Chinese labels, Claude/Anthropic palette, mind-map layout, auto-contrast text, pre-computed positions, suppressed edge labels.
- If a reference HTML was provided, its style tokens are used as-is (override defaults).

## Failure handling (v2.0)

- **No reference HTML, no CLAUDE.md** — fall back to default Claude palette; ask user to point at the teaching orchestration entry file before continuing.
- **Module IDs differ from M-009/010/011/012** — ask user once to map the 4 roles to actual module IDs. Do not guess.
- **48h learning lives only in `docs/plan/`, no `src/` implementation** — this is a design-doc project. Focus content extraction on `docs/plan/design/`. ALSO check `<project-parent>/ai-tutor/doc/` for MCP original (often richer).
- **Project is very small (<5 files)** — degrade to a 2-tab graph (Tab 2 + Tab 3 only) and note the simplification.
- **Cytoscape CDN blocked in user's environment** — switch to an inlined `cytoscape.min.js` (≈1MB); mention this in the output.
- **v2.0 specific**:
  - **Tabs 3/4 don't render** — this is the preset layout callback bug. Pre-compute positions in `computePositions()` and set on elements, NOT inside layout callback.
  - **User complains "scattered" / "ugly"** — switch from auto-layout to pre-computed mind-map positions; switch from primary-color palette to warm cream + Anthropic orange.
  - **User complains "too much English"** — translate all `label` fields to Chinese; keep English acronyms (FSRS, NLI, BKT, DKT, PDF, API) and code identifiers as-is.
  - **User complains "text hard to read"** — enable auto-contrast text color via luminance threshold.

## Iteration philosophy (v2.0 NEW)

Real users iterate. Typical iteration loop:
1. Ship v1 (academic / auto-layout / English mix)
2. User: "scattered" → switch to mind-map positions
3. User: "too much English" → translate labels
4. User: "ugly" → switch to Claude palette
5. User: "white text" → enable auto-contrast
6. User: "可以" / "good" → ship v2

**Each iteration is small and additive** — don't try to anticipate all needs upfront. The pre-compute positions pattern makes color/text/layout changes trivial.

## Examples

### Example 1 — typical design-doc project (the canonical case, v2.0)

Project root: `C:\Users\yhn\Desktop\AI-enabled learning-v1.0`
Style preference: `claude` (default)
Language: `zh` (default)
Core modules: M-009 / M-010 / M-011 / M-012 (教学编排 / 费曼评分 / FSRS / 阶段校准)
MCP original reference: `C:\Users\yhn\Desktop\ai-tutor\doc\48H-SIMULATION-v2.md`
V3.1 design reference: `docs/plan/design/08-系统模拟运行/48h-冲刺学习流程-场景文档.md`

→ Skill reads both MCP original AND V3.1 design, extracts:
  - 4 core modules from V3.1 design
  - Rich scenario details (6 strategies, BKT+DKT, cognitive load, PGFGA) from MCP original
  - File:line references pointing to V3.1 design docs (design phase, no `src/` yet)

→ Builds 4-tab graph:
  - Tab 1: 25 nodes / 28 edges (L1 业务能力, mind-map layout)
  - Tab 2: 22 nodes / 30 edges (L2 设计方法, mind-map layout, 5-core pentagon)
  - Tab 3: 22 nodes / 26 edges (L1+L2+L3 三列, preset 3-column)
  - Tab 4: 33 nodes / 38 edges (48h 时间线, timeline preset, states/actions spread along x)

→ Writes to `docs/plan/48h-learning-graph.html`, ~54KB.

→ Expected user iteration: v1 ships → user wants "Clau风格" → v2 changes palette → user wants white text → v2.1 adds auto-contrast.

### Example 2 — small project without a style reference
Project root: `C:\Users\yhn\Desktop\mini-tutor`
No reference HTML, no CLAUDE.md.
→ Skill uses default Claude palette, asks user "which file is the teaching entry point?", reads that file + any sibling docs, builds 2-3 tab graph, writes to `<project>/docs/plan/48h-learning-graph.html`.
