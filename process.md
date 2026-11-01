# Process

How a command-line application goes from an idea to a release, then keeps growing, with Claude Code. Its web counterpart is [astro-static-website-template](https://github.com/aunimhsn/astro-static-website-template/blob/main/process.md): a lesson that applies to every project goes to both.

The process is not a straight line. Its phases are **tools**, picked for each change according to what it needs: a new option goes straight to the build, a new screen of a terminal interface goes through its wireframe and mock-up first. What is not being done lives in a **backlog**; what is chosen goes to the **roadmap**, one pull request per step. Each tool ends with something the owner validates, from the cheapest to the most expensive: changing a sentence in the specifications costs less than changing a wireframe, which costs less than changing code.

This file is a living document: after each cycle, update it with what worked and what didn't (see [Retrospective](#retrospective)).

![The process: the backlog feeds the roadmap; each change goes through the tools it needs to a pull request; a cycle ends with the release and the retrospective, whose leftovers go back to the backlog.](docs/process-light.svg)

## How it works

1. **Start a project** from the template, and size it with the owner: which tools this project uses, and how far (see [Size the project](#size-the-project)).
2. **The first cycle** takes the application to its first release, through the tools it needs in order: specifications, structure, then wireframes, mock-up and design system for a terminal interface, build, release, retrospective.
3. **Then the loop**: with the owner, pick from the [backlog](#the-backlog) what comes next, write it in `roadmap.md` as steps, size each change and split it into sub-steps when it helps ([Size a change](#size-a-change)), and run only the tools it needs. A group of steps with a goal (« JSON output and Windows support ») is a **cycle**: it ends with its release (a minor or major version) and its retrospective.
4. **Outside the roadmap**, two kinds of work: an [urgent fix](#urgent-fixes) that can't wait, and an [exploration](#explorations) that tries an idea before deciding.

## Size the project

At the setup, Claude Code asks how large the project is and proposes the tools and their depth; the owner decides, and the choice is written at the top of `roadmap.md`.

| Project | Tools |
|---|---|
| A small command (one action, a few options) | Short specifications with the usage, the help and an example of each output; build; release. |
| A tool of several commands | Specifications, the structure of the commands and their options in Excalidraw, build, release. |
| A terminal interface (screens, keys, colors) | Every tool, in order, for the first cycle. |

## Size a change

At the start of each step, Claude Code says which tools the change needs, and why; the owner may add or remove one.

| Change | Tools |
|---|---|
| A message, a fix, a dependency | Build. |
| A new option of a command | Specifications (its usage and output), then build. |
| A new command | Specifications, structure (where it fits among the others), build. |
| A new output to design (a table, colors, a progress bar) | Mock-up, then build; the decision in `specs.md`. |
| A new screen of a terminal interface | Specifications, wireframe, mock-up, build. |
| A new tool, a redesign | Every tool, in order. |

Only when it helps, a step is split into sub-steps, one pull request each, each one coherent and tested on its own: a step that needs several tools, or more than one review (for example 4a (specifications and structure of a new command), 4b (its mock-up) and 4c (its build)). A small step stays whole, since each pull request also costs a review, the checks and an archive.
When a step turns out larger than sized, stop and size it again with the owner rather than stretching the pull request.

## The backlog

`backlog.md` holds everything noted and not being done: an evolving list, read when the next work is chosen, not at every session (`roadmap.md` stays short).

- **What goes in**: the touches set aside during a step (polish), the edge cases that need thought, the features for later, the large works, and what waits on someone else.
- **One entry per item**: what it is in one sentence, its kind (polish, edge case, feature, large work, waiting), what it depends on, and the date it was noted. Claude Code adds an entry as soon as an idea comes up outside the current step, rather than keeping it only in its memory.
- **From the backlog to the roadmap**: when the roadmap has no step left, or at the end of a cycle, Claude Code proposes the next items with their cost and dependencies; the owner picks; the chosen items leave the backlog and become steps of `roadmap.md`.
- **Polish**: after a release, a series of small touches from the backlog, released together as a patch version, is often worth doing before anything new.
- At each retrospective, the backlog is sorted: what is done or no longer wanted leaves it.

## Urgent fixes

A fix that can't wait (a crash, a security alert, data written to the wrong place) jumps the roadmap:

1. Branch `fix/<short-description>` from `main`, even while a step is in progress (in a worktree if the owner is testing the step in the main folder).
2. The fix, the test that would have caught it, the code review, a pull request opened at once, and a patch release.
3. Once merged, one line in `roadmap.md`, under the step in progress, and the step goes on (rebased on `main`).

## Explorations

An idea that needs trying before deciding (a library, a terminal interface, a faster algorithm) is an exploration, not a step. It happens at any time: during a step, when a choice needs trying before it is made (on sylent.art, whether a 3D scene runs on a phone, during the mock-up); or between steps, from an idea of the backlog (the launch videos).

1. A branch `explore/<short-description>`, or a program in the scratchpad: nothing of it is merged as it is.
2. Bounded: the owner and Claude Code agree on what it must answer (« does this library read the files fast enough? »).
3. It ends with its result written down: during a step, the decision goes back to the step (and to `docs/`); otherwise a decision in `docs/technical-choices.md` (kept or set aside, and why), an entry of the backlog, or a step of the roadmap that rebuilds it properly. The branch is then deleted, or kept with a line in the backlog that says why.

## The tools

| Tool | Use it when | Output | Done when |
|---|---|---|---|
| [Start a project](#start-a-project) | Once, at the start | Repository from the template, its settings, the size of the project | `main` is protected and the package has its name. |
| [Specifications](#specifications) | The application does something new | `specs.md`, the data sources | The owner has answered the open questions and merged them. |
| [Structure](#structure) | The commands and their options change | The structure in Excalidraw, written in `specs.md` | The owner has moved the cards and chosen a lead. |
| [Wireframes](#wireframes) | A screen of a terminal interface | `docs/wireframes/*.excalidraw` | The owner has edited and approved the layout. |
| [Mock-up](#mock-up) | Any visual choice of a terminal interface or an output | `docs/mockups/*.html` | Every scenario and state looks right at every size. |
| [Design system](#design-system) | At the end of a mock-up, a new token | Design section of `specs.md` | Colors, sizes and typography are written as tokens. |
| [Build](#build) | Always | One pull request per step | The step's box of `roadmap.md` is checked. |
| [Release](#release) | The end of a cycle, a patch of fixes | Version tag, GitHub release | The archives are published and the README explains how to use them. |
| [Retrospective](#retrospective) | The end of a cycle, or a tool that went badly | Changes to this file, the templates and the backlog | The lessons are written down. |

## Start a project

1. Create the repository from [rust-cli-template](https://github.com/aunimhsn/rust-cli-template) (see the README, **Start a new project**).
2. Apply the pull request settings: squash merging only, branches deleted once merged.
3. Protect `main` with a ruleset. On a free GitHub account, rulesets only apply to **public** repositories: make the repository public, or keep it private and accept that `main` is not protected.
4. Size the project with the owner ([Size the project](#size-the-project)), and write the tools kept at the top of `roadmap.md`, with the steps of the first cycle.
5. First pull request: rename the package (`Cargo.toml`, `Cargo.lock`), adapt the README, check the license, choose a port of its own for the mock-up server if the tool has a terminal interface (`docs/technical-choices.md`).
6. Open Claude Code in the project folder. Its memory is stored per project: a new session doesn't remember a conversation held in another project. To carry context over, paste a summary of it.
7. Check the connectors: the template turns off the claude.ai connectors (`disableClaudeAiConnectors` in `.claude/settings.json`), whose instructions and tools would be sent with every message. If the project needs one (Claude Docs, Gmail…), remove the setting; `/mcp` lists the servers that are on, and `/context` what fills the context. In the same file, `skillOverrides` hides from the model the skills the project doesn't use (still available with `/<name>`): adjust the list to the project, or use `/skills`.

## Specifications

Goal: agree on **what** the application does before deciding **how**. For a change after the first cycle, only the sections it touches.

1. Describe the idea in a few sentences, then let Claude Code ask its questions, in batches of a few at a time. The usual ones:
   - Who uses it, on which systems, in which languages?
   - What is in version 1, and what comes later? What must be easy to add later?
   - Which commands, options and arguments? What does each print, and in which formats (text, JSON)?
   - Plain commands, or a terminal interface (TUI) with screens and keys?
   - Where does the data come from? Which files does it read or write?
2. **Check the data sources early**: documentation, rate limits, terms of use, licenses of the images. Read the source code of a service when its limits aren't documented. Record the limits and every risk the owner accepts, with its date, in a note of `docs/` (e.g. `docs/<service>-usage.md`) linked from `roadmap.md`.
3. Write `specs.md`: behavior only (what the user sees, what each action does, edge cases, local files, error handling).
4. Write the technical choices with their reasons (and the alternatives rejected) in `docs/technical-choices.md`, then the steps in `roadmap.md`, each small enough for one pull request. `roadmap.md` is read at every session: keep it short, with links to the notes of `docs/`.
5. Open the pull request; the owner reads the files on GitHub (**Files changed**) or in VS Code.

The wireframes, the mock-up and the design system apply when the tool has a terminal interface (TUI) or a rich output to design (tables, colors, progress). A plain command skips them: its usage, its help and an example of each output go in `specs.md`, and the build starts.

## Structure

Goal: agree on how the commands, their options and the screens of a terminal interface fit together before writing them.

1. Claude Code draws the structure in Excalidraw (`excalidraw-wireframes` skill): one card per command or screen, the current state and **two leads** side by side, a parking area for the items not placed yet and the open questions, so that the owner moves the cards by hand.
2. The owner moves the cards, compares the leads, and chooses; Claude Code redraws the chosen lead, completed, and writes it in `specs.md`.

## Wireframes

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

## Mock-up

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

## Design system

Goal: write down the visual choices once, so that the application and its future screens stay consistent.

Write them in the design section of `specs.md` (or in `docs/design-system.md` when they grow), as named tokens:

- **Colors**: background, panels, borders, text, weak text, accent, warning, error, success; the colors of the domain (e.g. item rarities); dark and light themes if needed.
- **Sizes**: a spacing scale (e.g. 4, 8, 12, 16, 24 px), corner radius, the breakpoints, minimum window size.
- **Typography**: fonts, sizes per role (title, body, caption, large number), weights, number formatting per language.
- **Icons and images**: where they come from, their license, what is bundled in `assets/` (with an `assets/NOTICE.md` for images that aren't under the project license) and what is loaded at runtime.
- **Tone of the messages**: formal or informal address, length, how errors are worded.

The mock-up is then updated to use exactly these tokens. At the end of the phase, add an entry to [design-journal.md](design-journal.md) in the template: the choices kept, the ones rejected, and why.

## Build

For each step of `roadmap.md`, in order:

1. Branch `<type>/<short-description>` from `main`. If the owner isn't testing anything, work in the main folder so that they can follow the changes in VS Code; otherwise, in a Git worktree outside the project.
2. Code, with the tests; then `cargo fmt --all --check`, `cargo clippy --all-targets --all-features -- -D warnings`, `cargo test --all-features`.
3. Run the `code-reviewer` subagent: its notes, in French, go to `outputs/`. Claude Code summarizes them; the owner chooses which to apply.
4. Commit (Conventional Commits), push, open the pull request; it checks the step's box in `roadmap.md`.
5. The CI passes; the owner reviews and squash merges; `git pull` on `main`.
6. When a step changes the behavior, update `specs.md` and `roadmap.md` in the same pull request.

Steps that touch the user's system (clipboard, terminal, files) are verified on the owner's machine, on every target system, as early as possible in the roadmap. When a bug reaches the owner that a test could have caught, the fix adds the test.

## Release

1. Executable name, application icon, README (usage, help per system, services called and their risks).
2. Version in `Cargo.toml`, tag `vX.Y.Z` on the merged commit: the release workflow publishes the archives.
3. Commitments to third parties (e.g. tell a data provider once the releases reach a number of downloads).

## Retrospective

At the end of each cycle (the first one ends with the first release), or after any tool that went badly:

1. What took longer than expected? What had to be redone? What did the owner like most?
2. Sort the backlog: the leftovers and the ideas of the cycle go in, what is done or no longer wanted goes out.
3. Read Claude Code's memory of the project: a rule or a preference that holds for the whole project moves to `CLAUDE.md` or this file, where the next sessions and the templates find it.
4. Update this file, and the template (`CLAUDE.md`, `README.md`, the skills) when the lesson applies to every project, and the web template when it applies to it too.

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
| sylent.art | The classification of the offers ran as a mini-cycle of its own (specifications, then wireframe and mock-up, then build): the unit of the process is the change, not the project. |
| sylent.art | Ideas kept only in Claude Code's memory are easily lost between sessions, and some wait on other people: a `backlog.md` holds them, and the roadmap only what is chosen. |
