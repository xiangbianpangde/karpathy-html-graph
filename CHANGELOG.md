# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.0] - 2026-06-03

### Added
- `48h-learning-graph.html` — the headline deliverable. A single self-contained
  HTML file (~54 KB) with 4 interactive tabs (Capability Map, Method Map,
  Concept-to-Code 3-layer Map, 48h Cognitive Journey), inline CSS/JS, local
  `cytoscape.min.js` for fully offline use.
- `cytoscape.min.js` — Cytoscape.js 3.30.2 (MIT) bundled locally so the graph
  opens without internet.
- `teach-trace.md` — a portable skill spec (v2.0) that teaches any LLM how to
  generate this style of HTML graph (mind-map layout, Chinese-first labels,
  Claude/Anthropic palette, auto-contrast text).
- `docs/references/default-style.md` — Claude / Anthropic / minimal palette
  recipes.
- `docs/references/tab-schemas.md` — node/edge shapes for the 4 tabs and
  pre-computed layout patterns.
- `docs/README.zh-CN.md` — the original Chinese quickstart / file map.

### Design notes
- **No markdown wall.** The product is an HTML artifact, not a doc. Open
  `48h-learning-graph.html` in any modern browser to use it.
- **Single-file, offline-first.** No build step, no CDN, no server.
- **Open the box, read the code.** The HTML file is readable, hackable, and
  meant to be remixed.
