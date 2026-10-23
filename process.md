# Process

How an application goes from an idea to a release, with Claude Code. Each phase ends with something to validate before the next one starts: changing a sentence in the specifications costs less than changing a wireframe, which costs less than changing code.

This file is a living document: after each application, update it with what worked and what didn't (see [Retrospective](#8-retrospective)).

```
1. Repository ─▶ 2. Specs ─▶ 3. Wireframes ─▶ 4. Mock-up ─▶ 5. Design system ─▶ 6. Build ─▶ 7. Release ─▶ 8. Retrospective
   template        specs.md     Excalidraw       HTML           tokens             one PR       tag             process.md
                   roadmap.md                                                      per step
```

| Phase | Output | Done when |
|---|---|---|
| [1. Repository](#1-repository) | Repository from the template, first pull request | `main` is protected and the package has its name. |
| [2. Specifications](#2-specifications) | `specs.md`, `roadmap.md` | The owner has answered the open questions and merged them. |
| [3. Wireframes](#3-wireframes) | `docs/wireframes/*.excalidraw` | The owner has edited and approved the layout. |
| [4. Mock-up](#4-mock-up) | `docs/mockups/*.html` | Every scenario and state looks right at every size. |
| [5. Design system](#5-design-system) | Design section of `specs.md` | Colors, sizes and typography are written as tokens. |
| [6. Build](#6-build) | One pull request per roadmap step | Every box of `roadmap.md` is checked. |
| [7. Release](#7-release) | Version tag, GitHub release | The archives are published and the README explains how to use them. |
| [8. Retrospective](#8-retrospective) | Changes to this file and to the template | The lessons are written down. |

## 1. Repository

1. Create the repository from [rust-project-template](https://github.com/aunimhsn/rust-project-template) (see the README, **Start a new project**).
2. Apply the pull request settings: squash merging only, branches deleted once merged.
3. Protect `main` with a ruleset. On a free GitHub account, rulesets only apply to **public** repositories: make the repository public, or keep it private and accept that `main` is not protected.
4. First pull request: rename the package (`Cargo.toml`, `Cargo.lock`), adapt the README, check the license.
5. Open Claude Code in the project folder. Its memory is stored per project: a new session doesn't remember a conversation held in another project. To carry context over, paste a summary of it.

## 2. Specifications

Goal: agree on **what** the application does before deciding **how**.

1. Describe the idea in a few sentences, then let Claude Code ask its questions, in batches of a few at a time. The usual ones:
   - Who uses it, on which systems, in which languages?
   - What is in version 1, and what comes later? What must be easy to add later?
   - Command-line, desktop or web application? One window or several?
   - Where does the data come from?
2. **Check the data sources early**: documentation, rate limits, terms of use, licenses of the images. Read the source code of a service when its limits aren't documented. Record every risk the owner accepts, with its date, in `roadmap.md`.
3. Write `specs.md`: behavior only (what the user sees, what each action does, edge cases, local files, error handling).
4. Write `roadmap.md`: technical choices with their reasons (and the alternatives rejected), then the steps, each small enough for one pull request.
5. Open the pull request; the owner reads the files on GitHub (**Files changed**) or switches to the branch.

## 3. Wireframes

Goal: agree on the **layout**, without colors or code, in a format the owner can edit.

1. Claude Code generates an Excalidraw file in `docs/wireframes/` (a script writes the JSON): grayscale, sample data close to real ones, one frame per screen or window size, **one group per block** so that a block moves in one click (double-click a block to edit one of its elements, **Ctrl+Shift+G** to ungroup it).
2. It checks the result with an SVG preview before handing it over.
3. The owner edits it in VS Code with the **Excalidraw** extension (`pomdtr.excalidraw-editor`), or on excalidraw.com (**Open**, then **Save to…**).
4. Iterate: the owner draws their version, Claude Code rearranges it and fills the gaps, the owner draws the next version, and so on. Claude Code commits the owner's version before changing it, so that every iteration stays in the history.
5. Once the main frames are agreed, Claude Code draws the rest under them: other kinds of content, empty, loading and waiting states, errors, the other language.
6. Claude Code writes the agreed layout in `specs.md`.

For a responsive interface, start with the narrowest layout (mobile first): it forces to choose the essential, and the wider layouts then only rearrange the same blocks. Draw the panels that open over the content (settings) too.

Working with the file:

- The VS Code extension does **not** save by itself, unlike excalidraw.com: press **Ctrl+S** before asking Claude Code to read the file.
- The extension keeps the drawing in memory and doesn't reload the file when it changes on disk. Before Claude Code rewrites the file, save and close its tab; afterwards, reopen it, and never save the old tab over the new file.

## 4. Mock-up

Goal: check the layout with real content, every scenario and every state, before writing the application.

1. One self-contained HTML file in `docs/mockups/`, built from the approved wireframe, with controls above the mock window: size presets and sliders, scenarios (empty, normal, edge cases, errors), language, loading and error states.
2. URL parameters (e.g. `?w=1280&scenario=stack&lang=fr`) to share a given view.
3. Serve the repository root so that the page reaches the files of the repository (icons in `assets/`), even in a sandboxed browser (Flatpak, Snap) that only receives the HTML file:

   ```sh
   python3 -m http.server 8765 --bind 127.0.0.1
   ```

   then open `http://127.0.0.1:8765/docs/mockups/<file>.html`, and reload after each change.
4. Claude Code checks its own changes with headless screenshots (`firefox --headless --screenshot`) before asking for a review.

## 5. Design system

Goal: write down the visual choices once, so that the application and its future screens stay consistent.

Write them in the design section of `specs.md` (or in `docs/design-system.md` when they grow), as named tokens:

- **Colors**: background, panels, borders, text, weak text, accent, warning, error, success; the colors of the domain (e.g. item rarities); dark and light themes if needed.
- **Sizes**: a spacing scale (e.g. 4, 8, 12, 16, 24 px), corner radius, the breakpoints, minimum window size.
- **Typography**: fonts, sizes per role (title, body, caption, large number), weights, number formatting per language.
- **Icons and images**: where they come from, their license, what is bundled in `assets/` (with an `assets/NOTICE.md` for images that aren't under the project license) and what is loaded at runtime.
- **Tone of the messages**: formal or informal address, length, how errors are worded.

The mock-up is then updated to use exactly these tokens.

## 6. Build

For each step of `roadmap.md`, in order:

1. Branch `<type>/<short-description>` from `main`. If the owner isn't testing anything, work in the main folder so that they can follow the changes in VS Code; otherwise, in a Git worktree outside the project.
2. Code, with the tests; then `cargo fmt --all --check`, `cargo clippy --all-targets --all-features -- -D warnings`, `cargo test --all-features`.
3. Run the `code-reviewer` subagent: its notes, in French, go to `outputs/`. Claude Code summarizes them; the owner chooses which to apply.
4. Commit (Conventional Commits), push, open the pull request; it checks the step's box in `roadmap.md`.
5. The CI passes; the owner reviews and squash merges; `git pull` on `main`.
6. When a step changes the behavior, update `specs.md` and `roadmap.md` in the same pull request.

Steps that touch the user's system (clipboard, windows, files) are verified on the owner's machine, on every target system, as early as possible in the roadmap.

## 7. Release

1. Executable name, application icon, README (usage, help per system, services called and their risks).
2. Version in `Cargo.toml`, tag `vX.Y.Z` on the merged commit: the release workflow publishes the archives.
3. Commitments to third parties (e.g. tell a data provider once the releases reach a number of downloads).

## 8. Retrospective

After the release, or after any phase that went badly:

1. What took longer than expected? What had to be redone?
2. Update this file, and the template (`CLAUDE.md`, `README.md`) when the lesson applies to every project.

## Lessons learned

| Project | Lesson |
|---|---|
| rust-exile-market | Coded mock-ups were never exactly what the owner wanted: start with editable wireframes. |
| rust-exile-market | Read the terms of use of every data source during the specifications: a forbidden API changes the scope of version 1. |
| rust-exile-market | A Flatpak browser only receives the opened file: serve the repository with `python3 -m http.server`. |
| rust-exile-market | In the VS Code Flatpak, `~/.cargo/bin` is not on the `PATH` of the terminal: prepend it. |
| rust-exile-market | On a free GitHub account, rulesets only protect public repositories. |
| rust-exile-market | The owner draws, Claude Code rearranges and completes: the wireframe iterations made the interface personal. |
| rust-exile-market | The VS Code Excalidraw extension neither autosaves nor reloads a file changed on disk: save, close, reopen. |
