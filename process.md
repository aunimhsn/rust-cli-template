# Process

How a command-line application goes from an idea to a release, with Claude Code. Each phase ends with something to validate before the next one starts: changing a sentence in the specifications costs less than changing a wireframe, which costs less than changing code.

This file is a living document: after each application, update it with what worked and what didn't (see [Retrospective](#8-retrospective)). Its web counterpart is [astro-static-website-template](https://github.com/aunimhsn/astro-static-website-template/blob/main/process.md): a lesson that applies to every project goes to both.

```
1. Repository ─▶ 2. Specs ─▶ 3. Wireframes ─▶ 4. Mock-up ─▶ 5. Design system ─▶ 6. Build ─▶ 7. Release ─▶ 8. Retrospective ─▶ 9. Polish ─▶ 10. Next cycle
   template        specs.md     Excalidraw       HTML           tokens             one PR       tag             process.md           (optional)    back to 2
                   roadmap.md   (terminal interface only, 3 to 5)                  per step                                        small PRs     for its scope
                       ▲                                                                                                                             │
                       └─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘
```

| Phase | Output | Done when |
|---|---|---|
| [1. Repository](#1-repository) | Repository from the template, first pull request | `main` is protected and the package has its name. |
| [2. Specifications](#2-specifications) | `specs.md`, `roadmap.md` | The owner has answered the open questions and merged them. |
| [3. Wireframes](#3-wireframes) (terminal interface) | `docs/wireframes/*.excalidraw` | The owner has edited and approved the layout. |
| [4. Mock-up](#4-mock-up) (terminal interface) | `docs/mockups/*.html` | Every scenario and state looks right at every size. |
| [5. Design system](#5-design-system) (terminal interface) | Design section of `specs.md` | Colors, sizes and typography are written as tokens. |
| [6. Build](#6-build) | One pull request per roadmap step | Every box of `roadmap.md` is checked. |
| [7. Release](#7-release) | Version tag, GitHub release | The archives are published and the README explains how to use them. |
| [8. Retrospective](#8-retrospective) | Changes to this file and to the template | The lessons are written down. |
| [9. Polish](#9-polish) (optional) | Small pull requests from the « Later » list | The touches the owner picked are released. |
| [10. Next cycle](#10-next-cycle) | `specs.md` updated, new steps in `roadmap.md` | The owner has approved the scope of the cycle; the phases start again for it. |

## 1. Repository

1. Create the repository from [rust-cli-template](https://github.com/aunimhsn/rust-cli-template) (see the README, **Start a new project**).
2. Apply the pull request settings: squash merging only, branches deleted once merged.
3. Protect `main` with a ruleset. On a free GitHub account, rulesets only apply to **public** repositories: make the repository public, or keep it private and accept that `main` is not protected.
4. First pull request: rename the package (`Cargo.toml`, `Cargo.lock`), adapt the README, check the license, choose a port of its own for the mock-up server if the tool has a terminal interface (`docs/technical-choices.md`).
5. Open Claude Code in the project folder. Its memory is stored per project: a new session doesn't remember a conversation held in another project. To carry context over, paste a summary of it.
6. Check the connectors: the template turns off the claude.ai connectors (`disableClaudeAiConnectors` in `.claude/settings.json`), whose instructions and tools would be sent with every message. If the project needs one (Claude Docs, Gmail…), remove the setting; `/mcp` lists the servers that are on, and `/context` what fills the context. In the same file, `skillOverrides` hides from the model the skills the project doesn't use (still available with `/<name>`): adjust the list to the project, or use `/skills`.

## 2. Specifications

Goal: agree on **what** the application does before deciding **how**.

1. Describe the idea in a few sentences, then let Claude Code ask its questions, in batches of a few at a time. The usual ones:
   - Who uses it, on which systems, in which languages?
   - What is in version 1, and what comes later? What must be easy to add later?
   - Which commands, options and arguments? What does each print, and in which formats (text, JSON)?
   - Plain commands, or a terminal interface (TUI) with screens and keys?
   - Where does the data come from? Which files does it read or write?
2. **Check the data sources early**: documentation, rate limits, terms of use, licenses of the images. Read the source code of a service when its limits aren't documented. Record the limits and every risk the owner accepts, with its date, in a note of `docs/` (e.g. `docs/<service>-usage.md`) linked from `roadmap.md`.
3. Sketch the structure in Excalidraw when it isn't obvious: the commands and their options, the screens of a terminal interface, with the current state and two leads side by side, so that the owner moves them by hand.
4. Write `specs.md`: behavior only (what the user sees, what each action does, edge cases, local files, error handling).
5. Write the technical choices with their reasons (and the alternatives rejected) in `docs/technical-choices.md`, then the steps in `roadmap.md`, each small enough for one pull request. `roadmap.md` is read at every session: keep it short, with links to the notes of `docs/`.
6. Open the pull request; the owner reads the files on GitHub (**Files changed**) or in VS Code.

Phases 3 to 5 apply when the tool has a terminal interface (TUI) or a rich output to design (tables, colors, progress). A plain command skips them: its usage, its help and an example of each output go in `specs.md`, and the build starts.

## 3. Wireframes

Goal: agree on the **layout**, without colors or code, in a format the owner can edit.

The `excalidraw-wireframes` skill (`.claude/skills/`) carries out this phase: its Python helpers write the file and preview it.

1. Claude Code generates an Excalidraw file in `docs/wireframes/` (a script writes the JSON with the helpers of the skill): grayscale, sample data close to real ones, one frame per screen or window size, **one group per block** so that a block moves in one click (double-click a block to edit one of its elements, **Ctrl+Shift+G** to ungroup it).
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

Goal: choose the style with real content, every scenario and every state, before writing the application.

The `html-mockups` skill (`.claude/skills/`) carries out this phase, from a starting page with the style and decision tabs, and a headless screenshot script.

1. One self-contained HTML page in `docs/mockups/`, built from the approved wireframe: every frame of the wireframe becomes a window, with sample data close to real ones.
2. **Several styles on the same page, 3 or 4**, each with its own fonts, colors and density, switched with **tabs at the top of the page**. The address keeps the chosen style (e.g. `?theme=slate`) to share it. Each style only sets tokens (CSS variables); the components use them all, so that a style is cheap to add or tweak.
   - The styles explore **new directions** for this application, its users and its subject. [design-journal.md](design-journal.md) records the past experiences: it may inspire one of the styles, never all of them, and nothing in it is a default.
3. The owner picks a style, or mixes two; iterate on it alone, one change at a time (e.g. the main color, then the logo, then the font).
   - **Each open decision gets its own group of tabs** on the page (e.g. two button styles, three header variants, five offsets), so that the owner compares on screen; once decided, the tab is removed and the choice is kept.
   - **Pixel alignments are tuned by eye on the owner's real screen**, with a tab of offsets: the display scale differs from the headless screenshots, which only give the direction.
   - From then on, **the mock-up is the reference** of the interface; the wireframe only fixed the first layout.
4. Use fonts that can be embedded in the application (e.g. SIL Open Font License). Preload the font files, so that the page, and its screenshots, only finish loading once the fonts are there.
5. Serve the repository root without cache, on the port of the project (`docs/technical-choices.md`), so that the page reaches the files of the repository (icons and logo in `assets/`), even in a sandboxed browser (Flatpak, Snap) that only receives the HTML file, and a reload never mixes old and new files:

   ```sh
   python3 docs/mockups/serve.py <port>
   ```

   then open `http://127.0.0.1:<port>/docs/mockups/<file>.html`, and reload after each change. Two projects on the same port took each other's server over.
6. Claude Code checks its own changes with headless screenshots, kept in its scratchpad, before asking for a review, and exports one large PNG per style to `outputs/` to compare them at a glance. A test that needs extra code runs on a copy of the page, never on the file the owner has open.

## 5. Design system

Goal: write down the visual choices once, so that the application and its future screens stay consistent.

Write them in the design section of `specs.md` (or in `docs/design-system.md` when they grow), as named tokens:

- **Colors**: background, panels, borders, text, weak text, accent, warning, error, success; the colors of the domain (e.g. item rarities); dark and light themes if needed.
- **Sizes**: a spacing scale (e.g. 4, 8, 12, 16, 24 px), corner radius, the breakpoints, minimum window size.
- **Typography**: fonts, sizes per role (title, body, caption, large number), weights, number formatting per language.
- **Icons and images**: where they come from, their license, what is bundled in `assets/` (with an `assets/NOTICE.md` for images that aren't under the project license) and what is loaded at runtime.
- **Tone of the messages**: formal or informal address, length, how errors are worded.

The mock-up is then updated to use exactly these tokens. At the end of the phase, add an entry to [design-journal.md](design-journal.md) in the template: the choices kept, the ones rejected, and why.

## 6. Build

For each step of `roadmap.md`, in order:

1. Branch `<type>/<short-description>` from `main`. If the owner isn't testing anything, work in the main folder so that they can follow the changes in VS Code; otherwise, in a Git worktree outside the project.
2. Code, with the tests; then `cargo fmt --all --check`, `cargo clippy --all-targets --all-features -- -D warnings`, `cargo test --all-features`.
3. Run the `code-reviewer` subagent: its notes, in French, go to `outputs/`. Claude Code summarizes them; the owner chooses which to apply.
4. Commit (Conventional Commits), push, open the pull request; it checks the step's box in `roadmap.md`.
5. The CI passes; the owner reviews and squash merges; `git pull` on `main`.
6. When a step changes the behavior, update `specs.md` and `roadmap.md` in the same pull request.

Steps that touch the user's system (clipboard, terminal, files) are verified on the owner's machine, on every target system, as early as possible in the roadmap. When a bug reaches the owner that a test could have caught, the fix adds the test.

## 7. Release

1. Executable name, application icon, README (usage, help per system, services called and their risks).
2. Version in `Cargo.toml`, tag `vX.Y.Z` on the merged commit: the release workflow publishes the archives.
3. Commitments to third parties (e.g. tell a data provider once the releases reach a number of downloads).

## 8. Retrospective

After the release, or after any phase that went badly:

1. What took longer than expected? What had to be redone? What did the owner like most?
2. Sort what is left: the small touches go to the « Later » list of `roadmap.md` for the polish (phase 9), the larger ideas to the same list for a next cycle (phase 10).
3. Update this file, and the template (`CLAUDE.md`, `README.md`, the skills) when the lesson applies to every project, and the web template when it applies to it too.

## 9. Polish

Optional: once the version is released and the lessons written, the small touches that were noted and set aside (`roadmap.md`, Later), before anything new.

1. Claude Code lists the touches of the « Later » list (a message, an option, an error case, the speed of a command), each with its cost; the owner picks the ones worth doing now.
2. One small pull request per touch, or per group of touches on the same command, with the tests and the code review. No new command and no change of the behavior of a command here: those belong to a new cycle.
3. The touches are released together as a patch version.

## 10. Next cycle

The ideas that need more than a touch (a new command, a new output format, a terminal interface, another system) make the next cycle, which runs the phases again for its own scope.

1. With the owner, choose the scope of the cycle from the « Later » list and the new needs; give it a goal in one sentence.
2. Update `specs.md` first, then write the steps of the cycle in `roadmap.md` under a heading of their own (« Cycle 2: JSON output and Windows support »), numbered after the last step: the steps of the cycles done stay as one line each.
3. Run only the phases that apply: a new screen of a terminal interface goes through its wireframe and mock-up; a new option goes straight to the build.
4. The cycle ends with its release (a minor or major version) and its retrospective, then polish or start the next one.

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
| rust-exile-market | Mock-ups got much better once the layout was settled: one question per phase (layout, then style). |
| rust-exile-market | Three styles on one page with tabs made the choice easy; the main color stays white so that colors keep their meaning (item rarities). |
| rust-exile-market | One tab group per open decision, removed once decided, turned about a hundred small decisions into quick on-screen comparisons. |
| rust-exile-market | Pixel offsets measured on headless screenshots were wrong on the owner's scaled screen: tune them by eye with a tab of values. |
| rust-exile-market | The wireframe and mock-up phases worked so well that they became the `excalidraw-wireframes` and `html-mockups` skills, with their scripts. |
| sylent.art | The structure of the content came after the build, and the addresses changed twice: sketch the structure in Excalidraw during the specifications, cards moved by hand. |
| sylent.art | Bold variants showed the owner what they wanted: they couldn't have said « sober or spectacular » before seeing them. |
| sylent.art | The checks born from a bug kept it from coming back: when a bug reaches the owner, the fix adds the test. |
| sylent.art | A worktree outside the folder open in VS Code is invisible to the owner: work on the branch in the main folder while they test nothing there. |
| sylent.art | Open a pull request only once the owner has seen the result (the review applied, the drawing looked at). |
| sylent.art | Two mock-up servers of two projects on the same port broke the owner's page: one port per project, a server without cache. |
| sylent.art | Ideas kept only in Claude Code's memory are easily lost between sessions: a « Later » list in `roadmap.md` feeds the polish and the next cycle. |
