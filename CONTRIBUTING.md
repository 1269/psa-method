# Contributing

The most valuable contribution is an **adapter recipe**: worked automated-tier patterns for a tool beyond OmniFocus (Things, Todoist, Asana, Notion, Reminders, a calendar…).

## Adapter contributions

1. Add a section to `skills/psa/references/adapters.md` following the OmniFocus example: how to detect the tool, how to list/create the three PSA containers, how to pull and create tasks, any hard-won gotchas.
2. Keep it real: only patterns you've actually run. Note the platform (macOS-only vs anywhere) and what the tool exposes as a join key.
3. Open a PR with a one-line summary of what you tested it against (tool version, OS).

## Method and doc contributions

- Doctrine changes (stages, spaces, contracts) need a strong case — open an issue first. The glossary (`CONTEXT.md`) and ADRs (`docs/adr/`) explain why things are the way they are.
- Fixes to clarity, broken instructions, or contradictions between docs are always welcome as direct PRs.

## Forks

Deep customization belongs in your fork, and that's by design — see "Make it yours" in the README. If your fork grows something broadly useful, PR the general part back.
