# Contributing

Thanks for taking a look. This project is small on purpose — one HTML file,
one skill spec, two reference docs. Most of the value lives in `teach-trace.md`
(the recipe) and the structure baked into the HTML file (the result).

## Lowest-friction ways to help

1. **Try `teach-trace` on your own project.** The skill is the
   "portable recipe." If it produces a graph that breaks, looks scattered,
   or has wrong labels, open an issue with the failing input and the broken
   output. That feedback directly shapes the next revision.
2. **Open the HTML file in a browser and click around.** Found a node
   with wrong data, a broken `file:line` reference, or a translation that
   reads weird? File an issue or PR with the fix.
3. **Swap the palette.** Edit the `:root` CSS variables at the top of the
   HTML. If you find a combo that works for a different audience
   (academic, dark mode, neon, etc.), consider opening a PR that adds
   it to `docs/references/default-style.md` as a new style block.
4. **Translate the labels.** The graph is Chinese-first. If you want
   English / bilingual / other locales, the data arrays near the bottom
   of the HTML are the source of truth.

## Style

- Keep the graph **single-file**. Don't introduce a build step. The whole
  point is "double-click to open."
- Keep Cytoscape local. Don't re-add the CDN.
- Mind-map layout, not auto-layout, for Tabs 1–2. Pre-compute positions
  in JS, set them on each element, use `{name: 'preset', fit: true, padding: N}`.
- Auto-contrast text. White on dark, dark on light. Luminance threshold 0.55.
- Edge labels off by default.

## Pull request checklist

- [ ] I opened the HTML in a real browser and confirmed it still loads.
- [ ] I read `teach-trace.md` and made sure my change is consistent with it.
- [ ] If I changed the palette, I added a style block in
      `docs/references/default-style.md` so others can reuse it.
- [ ] If I changed a node's data, the `file:line` reference points to a
      real file.

## License

By contributing, you agree your contributions are licensed under the
project's MIT license.
