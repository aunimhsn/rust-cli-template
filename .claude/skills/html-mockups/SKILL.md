---
name: html-mockups
description: Build the interactive HTML mock-up of a user interface from its approved wireframe, in docs/mockups/, comparing 3 or 4 styles with tabs, then iterate with the owner one decision at a time (one tab group per open decision) until the mock-up becomes the visual reference, and write the design tokens. Use it after the wireframes are approved, when the owner asks for a mock-up, a style, a visual design, or to compare variants of a screen before coding it.
---

# HTML mock-ups

Goal: choose the style with real content, every scenario and every state, before writing the interface. The mock-up answers one question at a time: the layout is already settled by the wireframe (`excalidraw-wireframes` skill), so the discussion is only about the look.

## 1. Build the page

Start from `assets/mockup-template.html` (read its comments), copied to `docs/mockups/<name>.html`:

- **One self-contained page**: the CSS and the JavaScript inline, only the fonts and the images of `assets/` outside.
- **Every frame of the wireframe becomes a window**, at its real size, with sample data close to real ones (long names, large numbers, the other language). Add every scenario and state in `SECTIONS`: empty, loading, waiting, each error, each kind of content. **A new section goes at the top** of `SECTIONS`, so that the owner finds it at once; past ten sections, the page shows a table of contents under its header, one link per section.
- **3 or 4 styles on the same page**, switched with the tabs at the top; the address keeps the choice (`?theme=…`) to share it. Each style only sets **tokens** (CSS variables under `:root[data-theme="…"]`): fonts, colors, radius, spacing. The components use nothing else, so that a style is cheap to add or tweak.
- The styles explore **new directions** for this application, its users and its subject, from sober to spectacular: the owner decides by seeing, and a variant set aside is not wasted. If the project has a `design-journal.md`, it records past experiences: it may inspire one style, never all of them, and nothing in it is a default.
- **Fonts the application can embed** (e.g. SIL Open Font License, from Google Fonts), preloaded with `font-display: block`, so that the page and its screenshots only finish loading once the fonts are there.
- **Icons as SVG lines** in the text color, not text symbols (⚙, ⌕, ✓), which render small and off-center.

Pitfalls met before:

- A class shared by two components (`top`, `online`) silently broke the layout twice: prefix the classes of the application (`app-`) and of the states (`is-`).
- A JavaScript function named like a browser global (`top`, `name`, `status`) breaks the page: give the components specific names.

## 2. Show it

Copy `assets/serve.py` to `docs/mockups/serve.py`, and serve the repository root with it, so that the page reaches `assets/` even in a sandboxed browser (Flatpak, Snap) that only receives the HTML file, and a reload never mixes old and new files (it sends `Cache-Control: no-store`). Use the port of the project, written in `docs/technical-choices.md`: two projects on the same port took each other's server over. Keep the server running in the background:

```sh
python3 docs/mockups/serve.py <port>
```

Each time the mock-up changes, check that the page answers with the change, then give the owner `http://127.0.0.1:<port>/docs/mockups/<name>.html` with the section to look at; they reload after each change. If the page answers 404, check which project answers on the port before restarting.

Before asking for a review, check each change with a headless screenshot (`scripts/screenshot.sh <url> <output.png> [width] [height]`, Firefox normal or Flatpak), kept in the scratchpad, and look at it. To compare the styles at a glance, export one large PNG per style to `outputs/`.

- A test that needs extra code (a delay, a simulated click) runs on a copy of the page (`docs/mockups/test-*.html`, deleted after), never on the file the owner has open: they reload it at any time. Stop the helper servers by their PID, never with `pkill -f` and a pattern that also matches the shell's own command.
- A headless browser renders without the GPU: a color or WebGL bug the owner reports doesn't show in a screenshot. Build a small test page of variants and ask the owner to name the wrong ones.
- `firefox --screenshot` shoots before the scripts settle: an animation is measured or shot with a headless browser driven by a script.

## 3. Iterate with the owner

1. The owner picks a style, or mixes two. Iterate on it alone, **one change at a time** (the main color, then the logo, then a font…).
2. **Give each open decision its own group of tabs** in `VARIANTS` (two button shapes, three header variants, five offsets…), so that the owner compares on screen instead of imagining; once decided, remove the group, keep the choice in the tokens, and write the decision in the design section of `specs.md` in the same commit, with the options set aside. About a hundred small decisions went this way on the first project, each in a minute.
3. **Pixel alignments are tuned by eye on the owner's real screen**, with a tab of offsets (0, 0.5, 1, 1.5 px): the display scale of headless screenshots differs, so they only give the direction. Align icons on the lowercase letters next to them, not on the box of the text.
4. When the owner points at something, change only that, and say what changed.
5. From then on, **the mock-up is the reference** of the interface: say so in `specs.md`; the wireframe only fixed the first layout.

## 4. Finish: the design tokens

- Write the chosen style in `specs.md` (visual style section) as named tokens: colors (bands, text, weak text, accent, warning, error, the colors of the domain), sizes (spacing, radius, heights), typography (fonts, size and weight per role, number formatting), icons and images (source, license, what is bundled in `assets/` with an `assets/NOTICE.md`), shapes and motion.
- Remove the styles that were not chosen from the page, or keep them in a section that says they were rejected.
- If the project has a `design-journal.md` (or the template it came from), add an entry: the context, the choices kept, the ones rejected, and why.
- The application then implements exactly these tokens, in one module, so that changing a color or a size happens in one place.
