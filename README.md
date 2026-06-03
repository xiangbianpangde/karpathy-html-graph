# Karpathy Was Right: Make Your LLM Ship Interactive HTML, Not Markdown Walls

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)
[![Stars](https://img.shields.io/github/stars/xiangbianpangde/karpathy-html-graph?style=social)](#)
[![Forks](https://img.shields.io/github/forks/xiangbianpangde/karpathy-html-graph?style=social)](#)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](./CONTRIBUTING.md)
[![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)](./CHANGELOG.md)

> **"An LLM should be able to ship an HTML file with a working interactive
> UI, not just hand you a markdown wall you have to scroll."**
>
> — the idea behind this repo, paraphrased from Karpathy's public statements
> on LLM artifact formats.

> **TL;DR for the impatient:** Your LLM hands you 4,000 words of markdown
> when you asked for a graph. This is the open-source fix. Single HTML file,
> 4 tabs, 100+ nodes, fully offline. **Double-click to open. That's it.**
> ⭐ below and the repo is yours.

---

## Why This Exists

Karpathy has said it more than once: an LLM's output should be a **living,
interactive HTML page** — not a 50-page `.md` you have to scroll, not a
screenshot you can't click, not a Jupyter notebook that needs a kernel.

Most LLM artifacts today still fail this test:

- 🪦 **Markdown kills structure.** A long `.md` flattens hierarchies and
  cross-references into walls of text. You lose the map.
- 🔗 **References rot.** "`M-009 → M-010 → M-011`" is a sentence. It's
  not a graph. You can't click it, you can't zoom it.
- 🐢 **Static ≠ interactive.** A teaching design with 6 strategies,
  33 cognitive states, and 38 system actions should *be* a graph. Not
  a paragraph.
- 🌐 **CDN-first is a trap.** Pretty graph tools that need a build step
  or load JS from a CDN break offline, behind firewalls, and 5 years
  from now when the URL dies.
- 🤖 **LLMs default to markdown.** Ask an LLM to "explain this
  architecture" and you get prose. You wanted pixels.

**This repo is the artifact Karpathy asked for** — a working example of
what an LLM's output looks like when it ships HTML, plus the recipe
(`teach-trace.md`) that teaches any LLM to make one for your project.

## What You Get

A **single self-contained HTML file** (`48h-learning-graph.html`,
~54 KB) that turns a complex teaching-system design — 4 core modules,
3 layers of abstraction, 48 hours of cognitive journey — into a
**clickable, zoomable, fully offline knowledge graph**.

- 🖱️ **Click any node, see its details.** Drawer shows the kind, label,
  role, key quote, and a real `file:line` reference.
- 🔍 **Zoom, pan, filter by type.** Four pre-laid-out tabs (mind-map,
  pentagon, 3-column, timeline) — never scattered.
- 📦 **One file, fully offline.** `cytoscape.min.js` is bundled
  locally. No CDN, no build, no server.
- 🎨 **Claude / Anthropic warm palette.** Cream background, terracotta
  accent, 5 shades of warm brown hierarchy. Auto-contrast text — white
  on dark, dark on light. Luminance threshold 0.55.
- 🇨🇳 **Chinese-first labels.** Because most of the system being
  modeled is in Chinese. English acronyms (FSRS, NLI, BKT, DKT) stay.
- 🤖 **`teach-trace.md` teaches any LLM to make one.** Drop the spec
  into your LLM's skill folder, point it at a project, and it generates
  the same style of graph for that project. Repeatable, forkable.

## Quick Start

```text
1.  Download this repo (green "Code" button → Download ZIP)
2.  Unzip anywhere
3.  Double-click  48h-learning-graph.html
4.  Click around. That's it.
```

**Requirements**: any modern browser (Edge 90+, Chrome 90+, Firefox 90+,
Safari 15+). ES6 support is the only constraint.

**Want to teach your LLM to make one?** Drop
[`teach-trace.md`](./teach-trace.md) into your LLM's skill folder and
ask it to graph one of your own projects.

## How It Works

```
   Your LLM
       │
       │  (loads teach-trace.md as a skill)
       ▼
┌──────────────────────┐
│ 1. Detect project    │  reads CLAUDE.md / README.md
│    + 4 core modules  │  locates 4 teaching-system roles
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ 2. Read both sources │  V3.1 design rewrite + MCP original
│    extract L1/L2/L3  │  (3-layer map: concept → method → code)
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ 3. Build 4-tab data  │  capability / method / 3-layer / 48h timeline
│    + pre-compute pos │  positions in JS, not auto-layout
└──────────┬───────────┘
           ▼
┌──────────────────────┐
│ 4. Emit single HTML  │  inlined CSS/JS, local cytoscape
│    + commit to disk  │  writes  48h-learning-graph.html
└──────────┬───────────┘
           ▼
    Human opens it in a browser
           │
           │  clicks / zooms / filters
           ▼
    Understanding in 30 seconds
```

| Layer | What it does | Where it lives |
|---|---|---|
| **Skill spec** | Tells the LLM *how* to graph a project | `teach-trace.md` |
| **Style recipes** | Palette, typography, node colors | `docs/references/default-style.md` |
| **Tab schemas** | Node/edge shape per tab + layout patterns | `docs/references/tab-schemas.md` |
| **Deliverable** | The actual graph (the thing Karpathy asked for) | `48h-learning-graph.html` |
| **Runtime** | Renders the graph, fully offline | `cytoscape.min.js` |

## Use Cases

- 📚 **Teaching & tutoring system design** — visualize cognitive
  journey, method relationships, and module mapping at a glance.
- 🏗️ **Complex multi-module architecture** — when 4–6 core modules
  with 20+ features each need to be explained to a new joiner in
  under 5 minutes.
- 🔬 **Research-paper visual summary** — turn a 30-page paper into a
  4-tab graph: questions, methods, results, timeline.
- 📊 **Product / design reviews** — show stakeholders the *structure*
  of a system, not its bullet points.
- 🧠 **AI product R&D** — when the system you're building *is* an LLM
  teaching product, the artifact should itself be teaching-shaped.

## Comparison

| Aspect | A long markdown doc | This HTML graph |
|---|---|---|
| Time to first insight | 10+ min of scrolling | 30 sec of clicking |
| Cross-module navigation | broken inline refs | click any node |
| Zoom into a cluster | impossible | scroll wheel |
| Filter by type | grep | left-side checkboxes |
| Works offline | yes | yes |
| Works 5 years from now | depends on viewer | a `.html` file, forever |
| Hackable / remixable | yes | yes (open the file, edit `:root`) |
| Mobile-friendly | partial | full (pinch-zoom, pan) |
| Embeddable in a wiki | yes (as text) | yes (as `<iframe>`) |
| Generated by an LLM | yes (default) | yes (with `teach-trace`) |

## Roadmap

- [x] **v1.0** — 4 tabs, Claude/Anthropic palette, mind-map / 3-col /
      timeline layouts, `teach-trace.md` skill spec, fully offline.
- [ ] **v1.1** — More style palettes (academic / minimal / dark /
      neon) wired into `teach-trace` as a `style:` option.
- [ ] **v1.2** — Eval harness: scripted projects + automated checks
      (node count, label quality, layout sanity).
- [ ] **v1.3** — i18n: English-first / bilingual label modes for
      non-Chinese source projects.
- [ ] **v1.4** — Optional export: SVG / PNG of any tab, for slide
      decks and PDFs.

## Contributing

We love contributions. The lowest-friction ways to help:

1. 🧪 **Run `teach-trace` on your own project.** File an issue if the
   graph looks scattered, has wrong labels, or breaks.
2. 🎨 **Swap the palette.** Edit the `:root` CSS variables at the top
   of the HTML. If you find a combo that works, add it to
   `docs/references/default-style.md`.
3. 🇨🇳→🇺🇸 **Translate the labels.** The graph is Chinese-first. The
   data arrays near the bottom of the HTML are the source of truth.
4. 🐛 **Open the HTML in a browser and click around.** Found a broken
   `file:line` reference, a wrong node, a styling glitch? File a PR.

See [CONTRIBUTING.md](./CONTRIBUTING.md) for the full guide.

## License

[MIT](./LICENSE) — fork it, ship it, attribution is nice but not
required. The bundled `cytoscape.min.js` is also MIT (Cytoscape.js
consortium).

## ⭐ Star History

[![Star History Chart](https://api.star-history.com/svg?repos=xiangbianpangde/karpathy-html-graph&type=Date)](https://star-history.com/#xiangbianpangde/karpathy-html-graph&Date)

---

<p align="center">
  Built for the moment Karpathy said "LLMs should ship HTML, not markdown" —<br>
  and we actually shipped one.<br>
  <sub>Drop a ⭐ and tag <a href="https://twitter.com/karpathy">@karpathy</a> if you want him to see it.</sub>
</p>
