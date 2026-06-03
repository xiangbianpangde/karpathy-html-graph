# Tab Schemas — node and edge shape per tab (v2.0)

Each tab has its own data layer. The LLM builds these arrays in JS (as in
`STAGES` / `MODULES` / `FEATURES` / `DECISIONS` in design-graph.html) and feeds
them to cytoscape via `elements` + `style` + `layout`.

The total node budget is **20-40 per tab**. Keep the graph readable, not exhaustive.

**v2.0 changes from v1.0:**
- Layout patterns are now pre-computed (mind-map / 3-column / timeline), NOT auto-layout
- Chinese-first labels in `label` field
- Edge labels suppressed by default
- L3 nodes MUST have real `file:line` references (design docs OK in design phase)

---

## Tab 1 · 学习能力图谱 (L1 — business capability) — **mind-map layout**

**Perspective**: what does the 48h learning system actually deliver?

**Node types**:
- `goal` — top-level 48h learning goal (e.g. "48 小时冲刺学习")
- `subcap` — sub-capability (e.g. "概念讲解", "主动召回", "跨域迁移")
- `metric` — measurable outcome (e.g. "85% 概念掌握")

**Required fields per node**:
```js
{ id, label, kind: 'goal'|'subcap'|'metric',
  desc,           // 1-2 sentence explanation (Chinese)
  ref,            // file:line where this concept is defined (real file)
  metric?: string // for metric nodes only
}
```

**Edges**:
- `goal → subcap` (decomposition) — solid
- `subcap → subcap` (lateral support) — dashed
- `subcap → metric` (measurable) — solid arrow

**Layout (v2.0 mind-map)**: Pre-computed positions, not auto.
- Root at canvas center (e.g. `cx=700, cy=450`)
- 4 main direction clusters (top / right / bottom / left)
- Each cluster contains related subcaps, anchored near cluster center
- Avoid crossing edges; group by phase/role

```js
function computePositions(tabId) {
  const pos = {};
  const cx = 700, cy = 450;
  pos['l1_root'] = { x: cx, y: cy };
  // 上方 (启动+教学准备) - 7 节点
  pos['l1_bootstrap'] = { x: cx - 280, y: cy - 280 };
  pos['l1_ingest'] = { x: cx - 100, y: cy - 320 };
  // ... etc
  return pos;
}
```

---

## Tab 2 · 设计方法图谱 (L2 — design methodology) ⭐ — **mind-map layout**

**Perspective**: which learning-science methods were chosen, and how do they collaborate?

**Typical core methods** (pick the ones that actually appear in the project):
- `feynman` — Feynman technique (output-driven learning)
- `scaffold` — Scaffolding / progressive difficulty
- `cognitive` — Cognitive model (working memory, attention cycle)
- `fsrs` — Free Spaced Repetition Scheduler
- `rest` — Spaced rest / sleep consolidation
- `recall` — Active recall (vs passive re-reading)
- `interleave` — Interleaved practice

**Node types**:
- `method` — the design method itself (large ellipse in v1.0, round-rectangle in v2.0)
- `rule` — concrete sub-rule or parameter (small round-rectangle)

**Required fields per node**:
```js
{ id, label, kind: 'method'|'rule',
  summary,        // 1-sentence description (Chinese)
  ref,            // file:line where the method is implemented (real file)
}
```

**Edges (v2.0 — labels suppressed)**:
- `method → rule` — solid
- `method → method` — solid (collaboration)
- NO labels by default (was major clutter source in v1.0)

**Layout (v2.0 mind-map — pentagon)**:
- 5 core methods at pentagon vertices around center
- Rules cluster near their parent method
- Other methods in outer ring

```js
const cx = 750, cy = 450;
pos['m_feynman'] = { x: cx - 250, y: cy - 280 };  // NW
pos['m_scaffold'] = { x: cx, y: cy - 330 };       // N
pos['m_cognitive'] = { x: cx + 250, y: cy - 280 }; // NE
pos['m_fsrs'] = { x: cx + 350, y: cy + 50 };      // E
pos['m_hysteresis'] = { x: cx + 180, y: cy + 320 }; // SE
// ... etc
```

---

## Tab 3 · 概念→设计→代码 (L1+L2+L3 — three-layer trace) ⭐⭐ — **3-column preset**

**This is the core tab.** Shows the full trace from business concept to code, side by side.

**Three preset columns** (use `layout: { name: 'preset', fit: true }` after setting positions):

| Column | x | color | content |
|--------|---|-------|---------|
| Left (L1 业务概念) | 180 | warm tan `#c2a878` | business goals, sub-capabilities |
| Middle (L2 设计方法) | 600 | bronze `#b08552` | design methods, key parameters |
| Right (L3 代码实现) | 1080 | deep rust `#a8482b` | module IDs, key files, key functions |

**Node types**:
- `concept` — L1, tan
- `method` — L2, bronze
- `code` — L3, rust

**Required fields per node**:
```js
{ id, label, layer: 'L1'|'L2'|'L3',
  desc,             // Chinese
  ref,              // file:line — L3 MUST have real file:line
}
```

**Edges (v2.0 — labels suppressed)**:
- `L1.concept → L2.method` — "implemented by" (dashed)
- `L2.method → L3.code` — "realized in" (dashed)
- `L1.concept → L3.code` — "traces to" (long-span, very thin)

**Layout (v2.0 3-column preset)**:
```js
const xs = { L1: 180, L2: 600, L3: 1080 };
T3_NODES.forEach(n => {
  const list = T3_NODES.filter(x => (x.layer || 'L1') === n.layer);
  const idx = list.indexOf(n);
  pos[n.id] = { x: xs[n.layer], y: 80 + idx * 110 };  // y stacked per column
});
```

**CRITICAL (v2.0 bug fix)**: Do NOT reference `cy.nodes()` inside the layout callback. Set positions on elements BEFORE creating cytoscape, then use `{name: 'preset', fit: true}`.

---

## Tab 4 · 认知旅程 (timeline + cognitive state) — **timeline preset**

**Perspective**: what does the user experience across 48 hours, and what does the system do at each beat?

**Node types**:
- `anchor` — time anchor (diamond, e.g. "T=0", "T+12h", "T+48h")
- `state` — user cognitive state (ellipse, e.g. "陌生", "理解", "能复述")
- `action` — system teaching action (round-rectangle, e.g. "概念讲解", "FSRS 调档")

**Required fields per node**:
```js
{ id, label, kind: 'anchor'|'state'|'action',
  t?: number,         // for anchor: minutes from start
  ref                 // file:line (real)
}
```

**Edges (v2.0 — labels suppressed)**:
- `anchor → anchor` — solid (time sequence)
- `anchor → state` — solid (user enters)
- `state → action` — solid (system responds)
- `action → state` — dashed (transitions to)

**Layout (v2.0 timeline preset — v1.0 bug fix)**:
**v1.0 BUG**: all states/actions at same x=720, looked like a vertical line.
**v2.0 FIX**: spread states/actions along x.

```js
// Anchors: x = t (linear)
const minT = -1440, maxT = 2880;
const xScale = (t) => 90 + ((t - minT) / (maxT - minT)) * 1320;
T4_NODES.filter(n => n.kind === 'anchor').forEach(n => {
  pos[n.id] = { x: xScale(n.t), y: 100 };
});

// States: spread along x (evenly distributed)
const states = T4_NODES.filter(n => n.kind === 'state');
states.forEach((n, i) => { pos[n.id] = { x: 180 + i * 220, y: 320 }; });

// Actions: 2 rows distributed along x
const actions = T4_NODES.filter(n => n.kind === 'action');
actions.forEach((n, i) => {
  const row = Math.floor(i / 9);
  const col = i % 9;
  pos[n.id] = { x: 100 + col * 165, y: 540 + row * 90 };
});
```

---

## Drawer detail schema (all tabs)

When a node is tapped, the right drawer renders:

```
┌────────────────────────────────────────┐
│ KIND · <type tag>                      │
│ <node label, serif H2>                 │
│ ────────────────────────────────────   │
│ <subtitle / one-liner>                 │
│                                        │
│ 描述 (DESCRIPTION)                     │
│ <1-2 sentence explanation (Chinese)>   │
│                                        │
│ 引用 (REFERENCE)                       │
│ <code>path/to/file.md:42</code>        │
│                                        │
│ TAGS                                   │
│ [pill] [pill] [pill]                   │
└────────────────────────────────────────┘
```

Field labels are mono uppercase (e.g. `描述`, `引用`, `标签`); the
content under each label is in body font. The KEY QUOTE block has a left
border in `--orange` (`#d97757`) to set it off.

---

## v2.0 Pitfalls (lessons learned)

| Pitfall | Fix |
|---------|-----|
| Auto-laid-out graph looks scattered ("现在很散乱") | Pre-compute positions, use preset layout |
| Preset layout callback references `cy.nodes()` (old instance) | Compute positions BEFORE creating cytoscape |
| All text dark → hard to read on dark nodes | Auto-contrast via luminance threshold |
| Edge labels overlap and clutter | Suppress by default |
| English labels everywhere | Chinese-first; keep English acronyms only |
| Tabs 3/4 don't render | The preset callback bug — see above |
| Timeline states all stack at same x | Spread states along x with index-based x position |
