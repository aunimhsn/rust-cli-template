# CLAUDE.md

## Communication

- Always communicate with the user in French through the Claude Code harness (chat responses, explanations, questions).
- Address the user informally, with « tu » (tutoiement), never « vous », in every session.
- Ask a few numbered open questions at a time, in plain text, the main one first, rather than multiple-choice forms: the owner prefers to answer freely, often by voice dictation (interpret the misspelled names, and confirm when an answer is ambiguous). An unanswered question takes Claude Code's recommendation.
- Keep a critical eye on the owner's ideas: give a clear opinion with its reason, point out the consequence a choice hides, offer an alternative, then follow the owner's decision.
- The first time a technical term comes up that the owner may not know (design token, trait object, lifetime…), explain it in plain words with a short example.

## Naming conventions

- Follow Rust conventions: Rust source files and module directories use snake_case (e.g. `src/file_parser.rs`, `src/user_settings/`).
- All other files and directories use kebab-case (e.g. `docs/getting-started.md`, `scripts/`).
- Standard names keep their conventional form (e.g. `Cargo.toml`, `README.md`, `CLAUDE.md`).
- File and directory names must be in English.

## Language

- Everything in the project (code, identifiers, comments, documentation, commit messages) must be written in English.
- Exception: the translations of the user interface, if the project has any. Only the translated strings and their comments are in the other language; the code stays in English.
- If the user interface is translated, its texts are never hard-coded: each message goes into the translation file of every language (English is the reference), read with the library chosen in `docs/technical-choices.md`.
- Exception: the code review notes in `outputs/` are written in French.

## Specifications and roadmap

- Before writing code, describe the expected behavior of the application in `specs.md`, then split the work into steps in `roadmap.md`, and write the technical choices and their reasons in `docs/technical-choices.md`.
- At the start of each session, read `roadmap.md` to know the progress; when asked to continue, resume at the first unchecked step.
- At the start of each step, before writing any code, say which tools of `process.md` the change needs and why (Size a change); when a step turns out larger than sized, stop and size it again with the owner. Judge also whether splitting it into sub-steps (2a, 2b…) helps, and how finely: the split must be an advantage, never a burden, and its granularity is Claude Code's call at each step. A sub-step is one pull request with one coherent change that can be reviewed in one pass and tested on its own, without leaving the application broken in between; a small step stays whole, since each pull request also costs a review, the checks and an archive. Propose the split to the owner, then write it in `roadmap.md`.
- Each step is delivered in its own pull request, which checks the step's box in `roadmap.md`.
- Keep `roadmap.md` consistent with `specs.md` when the specifications change.
- Keep `roadmap.md` short, since it is read at every session: the steps to come in detail, one line per step done with its pull requests. When the pull request of a step (or of a sub-step) is merged, move the detail of the step to `docs/roadmap-archive.md`. Reference notes (technical choices, rules of the external services) live in `docs/`, linked from `roadmap.md`, and are read only by the steps that need them.
- When an idea, a small touch or an edge case comes up that is not part of the current step, add it to `backlog.md` (process.md, The backlog), with its kind, what it depends on and the date, rather than keeping it only in the memory. Read `backlog.md` only when the next work is chosen: when no step is left in `roadmap.md`, or at the end of a cycle, propose the next items to the owner with their cost and dependencies.
- Read `specs.md` by section: list its headings first (`grep -n '^#' specs.md`), then read only the sections the step needs.
- When the pull request of a step (or of a sub-step) is merged, tell the owner that the step is done and remind them to start a new session for the next step by typing `/clear` (give them the command): a fresh session costs far fewer tokens, and `roadmap.md` and the memory hold what is needed to resume.

## Connectors

- The claude.ai connectors (Claude Docs and the others) are turned off for the project in `.claude/settings.json` (`disableClaudeAiConnectors`): their instructions and tools would be sent with every message. Remove the setting only if the project needs one of them.
- The skills a project doesn't use (documents, slides, spreadsheets, browsers, charts, Claude API…) are hidden from the model in the same file (`skillOverrides`, `user-invocable-only`): their descriptions would be sent with every message. The owner can still run them with `/<name>`; to give one back to the model, remove its line or use `/skills`.
- The skills of the project live in `.claude/skills/`, copied into every project from the template, never shared from `~/.claude/skills`: each project has one source of truth. A lesson that improves a skill goes back to the template too.

## Working folders

Two folders are the exchange area between the owner and Claude Code. They are ignored by Git, except their `.gitkeep`, and hold only temporary files.

- `inputs/`: what the owner gives for context: screenshots, examples, references, copied texts. Read them when the owner points to them; never commit, move or delete them unless asked.
- `outputs/`: what Claude Code produces for the owner: reports, code review notes, screenshots to compare styles. Name them `<YYYY-MM-DD>-<subject>.<ext>` when they are meant to be kept for a while. Claude Code's own checks (wireframe previews, test screenshots) stay in its scratchpad, never in `outputs/`, so that the owner doesn't take them for the deliverable.
- Anything meant to last goes elsewhere and is committed: documentation in `docs/`, images used by the application in `assets/`, test data next to the tests.

## Process

- Follow `process.md`: the first cycle runs the tools the project needs in order (specifications, structure, then wireframes, mock-up and design system for a terminal interface, build, release, retrospective); after it, each change runs only the tools it needs, picked from the backlog into the roadmap. At the retrospective of a cycle, sort the backlog, and move to `CLAUDE.md` or `process.md` the rules of the memory that hold for the whole project.
- An urgent fix jumps the roadmap (a `fix/` branch from `main`, its test, a pull request at once and a patch release, then one line in `roadmap.md`). An idea that needs trying before deciding is an exploration: a branch `explore/<short-description>` that is never merged as it is, bounded by the question it must answer, and ended by its result written down (a decision in `docs/`, a backlog entry or a roadmap step).
- Sketch any question of structure (the commands and their options, the screens of a terminal interface) as an editable Excalidraw drawing, with the current state and two leads side by side, before writing it in `specs.md`.
- For a terminal interface, start with editable Excalidraw wireframes in `docs/wireframes/` (grayscale, one group per block; `excalidraw-wireframes` skill), and build the HTML mock-up only once the owner has approved the layout (`html-mockups` skill).
- During the specifications, check the documentation, rate limits and terms of use of every external service, and record its limits and the risks the owner accepts, with their date, in a note of `docs/` linked from `roadmap.md` (e.g. `docs/<service>-usage.md`).
- For the mock-up, compare 3 or 4 styles that explore new directions for this application. `design-journal.md` records the past design experiences: use it as a source of ideas for one style at most, never as default rules. Give each open decision its own tab group on the mock-up page, and remove it once decided. A new section of the mock-up goes at the top of the page, so that the owner finds it at once; past ten sections, a table of contents under the header links to each.
- Each time the mock-up changes (a new mock-up, a new section, any change to its page or its files), give the owner its link at once, served: `python3 docs/mockups/serve.py <port>` (no cache), on the port of this project written in `docs/technical-choices.md`, kept running in the background; check that the page answers with the change, and give `http://127.0.0.1:<port>/docs/mockups/<name>.html`, with the section and the windows to look at. Never a `file://` path: a sandboxed browser may not open it.
- A test that needs extra code (a delay, a simulated click) runs on a copy of the mock-up page, never on the file the owner has open. Stop the helper processes by their PID, never with `pkill -f` and a pattern that also matches Claude Code's own command.
- When a deliverable is handed to the owner to look at (a drawing, the mock-up, a build to try), wait for their approval before writing it into `specs.md` or opening the pull request: « let's go » is not an approval of something they haven't seen yet.

## Git workflow

- Never commit or push directly to `main`.
- For each task, create a dedicated branch from `main`, commit and push only on that branch.
- Every change must go through a pull request targeting `main`.
- Each time a pull request is opened or updated, give the owner its full link (`https://github.com/<owner>/<repository>/pull/<number>`), not only its number.
- Merge pull requests with **Squash and merge** only, so that each pull request becomes one commit on `main`, titled in Conventional Commits. The repository allows only squash merging and deletes the branch once merged; check these settings on every new repository (`gh repo edit --enable-squash-merge --enable-merge-commit=false --enable-rebase-merge=false --delete-branch-on-merge`).
- Keep only one pull request open at a time: open the next one once the previous one is merged. Two open pull requests often edit the same files (`roadmap.md`, `docs/`), and the second one conflicts once the first is merged. If urgent work comes up meanwhile, prepare its branch and push it, then open its pull request once the open one is merged.
- When several pull requests are open anyway, resolve their conflicts before the owner sees them: as soon as the owner says a pull request is merged, rebase the others on `main` (`git rebase origin/main`, then `git push --force-with-lease`), run the checks again, and tell the owner when each one is ready to merge.
- Name branches `<type>/<short-description>` in kebab-case, using the commit types below (e.g. `feat/json-output`, `fix/config-path`), or `explore/` for an exploration.
- Work on the branch in the main project folder, which the owner has open in VS Code: a worktree outside it is invisible to them. Only when the owner may be testing another branch there (for example with `cargo run`), ask, and if so work in a separate Git worktree outside the project (`git worktree add <directory> -b <branch> origin/main`), give its exact path, and remove it with `git worktree remove <directory>` once the work is pushed.

## Commit messages

Follow [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/). Pull request titles use the same format.

```
<type>(<optional scope>): <description>

<optional body: what and why>

<optional footer>
```

| Type | Use for |
|---|---|
| `feat` | A new feature for the user. |
| `fix` | A bug fix. |
| `docs` | Documentation only (`README.md`, `specs.md`, `roadmap.md`, `CLAUDE.md`, doc comments). |
| `style` | Formatting with no code change. |
| `refactor` | A code change that neither fixes a bug nor adds a feature. |
| `perf` | A performance improvement. |
| `test` | Adding or fixing tests only. |
| `build` | Build system or dependencies (`Cargo.toml`, `Cargo.lock`). |
| `ci` | CI configuration (`.github/`). |
| `chore` | Other maintenance (tooling, `.gitignore`, Claude Code agents). |
| `revert` | Reverting a previous commit. |

- Scope: optional, the module or area concerned (e.g. `feat(settings): ...`, `fix(parser): ...`).
- Description: imperative mood, lowercase, no final period, 72 characters max (e.g. `feat(settings): save the settings atomically`).
- Breaking change: add `!` after the type or scope (e.g. `feat(settings)!: ...`) and explain it in a `BREAKING CHANGE:` footer.

## Code review

- Before opening any pull request that changes code or its build (`src/`, tests, `Cargo.toml`, `build.rs`, `rust-toolchain.toml`, `.github/workflows/`), run the `code-reviewer` subagent (`.claude/agents/code-reviewer.md`) on the branch.
- It writes its recommendations, in French, to `outputs/<YYYY-MM-DD>-<branch>-code-review.md`. These notes are ignored by Git.
- Summarize the recommendations to the owner, apply only the ones they approve, then open the pull request: never before the owner has chosen.
- When the owner asks to look at the Dependabot pull requests (the dependency updates, `.github/dependabot.yml`), run the `dependency-reviewer` subagent (`.claude/agents/dependency-reviewer.md`): it writes, in French, to `outputs/<YYYY-MM-DD>-dependency-review.md`, a recommendation for each one, with its link and the comment to post. Give the owner its summary table; never merge, close or comment a Dependabot pull request yourself unless the owner asks.

## Development

- Before committing, run and make sure all of these pass:
  - `cargo fmt --all --check`
  - `cargo clippy --all-targets --all-features -- -D warnings`
  - `cargo test --all-features`
- The same checks run in CI (`.github/workflows/ci.yml`) on every pull request.
- When a bug reaches the owner that a test or a check could have caught, add it in the same pull request as the fix.
- `unsafe` code is forbidden and Clippy pedantic lints are enabled (see `[lints]` in `Cargo.toml`).
- `rust-version` in `Cargo.toml` is the minimum Rust version: CI also builds with it. Raise it when a dependency or a language feature needs a newer Rust.
- Once the tests of a module run past a few hundred lines, move them to a file of their own, `<module>/tests.rs` (`src/session.rs` ends with `#[cfg(test)] mod tests;`, its tests in `src/session/tests.rs`), so that reading the code doesn't read its tests too; as a child module, they still reach its private items (`use super::*;`). The paths of `include_str!` are then one `../` deeper.

## Releases

- Pushing a version tag (e.g. `v0.1.0`) that matches the version of `Cargo.toml` publishes a GitHub release with the executables for Windows, macOS and Linux (`.github/workflows/release.yml`).
- The template is made for command-line and terminal applications, which this workflow suits: an archive per system with the executable.
