# Specifications

<!--
Describe what the application does, before writing any code (process.md, Specifications).
Write behavior, not implementation: technical choices go in docs/technical-choices.md.
Remove or add sections as needed.
-->

## Purpose

<!-- What the application is for, who uses it (a person, a script, both), and the expected volume of data. -->

## Success criteria

<!-- Two or three facts the owner can check after the release of this version, checked one by one at the retrospective. For example:
- It replaces my shell script for the weekly export.
- It reads a year of logs (about 2 GB) in under 5 seconds.
-->

## Requirements

<!-- What isn't a feature but must hold: the target systems, the smallest terminal supported (e.g. 80 × 24), the speed on a given volume, the size of the executable. Only what matters for this application. -->

## Design principles

<!-- Look and feel, tone of the messages, use of colors. -->

## Command-line conventions

<!-- The answers to process.md, Command-line conventions: stdout and stderr, plain output when piped, --color and NO_COLOR, --help and --version, completions. Only what departs from the usual, or a choice to make. -->

## Commands

<!-- One subsection per command: its usage, its options and arguments, what it prints, the edge cases, and its acceptance examples, each of which becomes a test. For example:

### count

`tool count <file> [--json]`: counts the lines of a file.

```text
$ tool count notes.txt          → prints « 42 lines », exit code 0
$ tool count missing.txt        → prints on stderr « missing.txt: file not found », exit code 2
$ tool count notes.txt --json   → prints {"lines":42}, exit code 0
```
-->

## Local files

<!-- Files read or written by the application, where they live on each system, and whether they are tracked by Git. -->

## Exit codes

| Code | Meaning |
|---|---|
| 0 | Success. |

## Error handling

| Situation | Behavior |
|---|---|
