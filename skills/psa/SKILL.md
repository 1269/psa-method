---
name: psa
description: Run your PSA (Projects, Systems, Archives) productivity system — a lifecycle-based structure across four spaces (File Share, Second Brain, Task Manager, Journal) plus a PSA-aware calendar. Use for setting up the system from scratch (/psa setup), status overviews, navigating projects and systems, creating/archiving/converting projects, health reviews, mirroring tasks into agent-ready markdown files, arming task briefs, and scheduling work into available time blocks. Triggers on "/psa", "set up my PSA system", "new PSA project", "archive this project", "PSA status", "psa review", "turn this project into a system", "mirror my tasks into files", "arm this task", "add context to this task", "schedule my ready tasks", "block time for my tasks".
---

# PSA — Projects, Systems, Archives

One method, one command. Navigate and manage the PSA framework across all four spaces, using the tools recorded in the user's config.

## Step zero — every invocation

1. Read `~/.psa/config.json`.
2. **If it doesn't exist** (and the invoked command isn't `setup` itself): the system isn't set up. Say so, and offer to run `/psa setup`. Run nothing else without a config.
3. Resolve every path in the config (expand `~`). Note each tool's **access tier** — it governs how you touch that tool for the rest of the session:
   - `automated` — drive the tool directly via its adapter (see [references/adapters.md](references/adapters.md))
   - `guided` — give the user exact, step-by-step instructions for their tool, then wait for confirmation before continuing
   - `markdown` — the structure lives as files; operate on files only

## Commands

| Command | What it does | Reference |
|---|---|---|
| `/psa setup` | Interview → write config → scaffold all four spaces (idempotent, adoption-safe) → tour | [references/setup.md](references/setup.md) |
| `/psa` | Status overview: capacity, projects, systems needing review | [references/status.md](references/status.md) |
| `/psa projects` \| `systems` \| `archives` | List one stage with health/cadence details | [references/status.md](references/status.md) |
| `/psa navigate [name]` | Find a project or system across all spaces | [references/status.md](references/status.md) |
| `/psa review` | 7-point health check with actionable fixes | [references/status.md](references/status.md) |
| `/psa new [name]` | Create a project across all spaces (capacity-gated, GPS Brief) | [references/lifecycle.md](references/lifecycle.md) |
| `/psa archive [name]` | Close a completed project into Archives | [references/lifecycle.md](references/lifecycle.md) |
| `/psa convert [name]` | Close a project and turn its output into a System | [references/lifecycle.md](references/lifecycle.md) |
| `/psa tasks [project]` | Mirror a project's tasks into skeleton task files | [references/tasks.md](references/tasks.md) |
| `/psa add-context [task]` | Arm one task file into a self-contained agent brief | [references/tasks.md](references/tasks.md) |
| `/psa schedule` | Propose time blocks for ready work; write to calendar per tier | [references/schedule.md](references/schedule.md) |

Read the reference file for the invoked command **before** executing it. For the method itself (stages, spaces, doctrine), fetch [docs/method.md in the psa-method repo](https://github.com/1269/psa-method/blob/main/docs/method.md) — the installed skill folder contains only the references and templates.

## Conventions (apply to every command)

- **Confirm before writing.** Show what you're about to create, move, or change — in any space — and get a yes first. Reads are free; writes are gated.
- **Idempotent and adoption-safe.** If something already exists (folder, file, project), detect it, report "already in place," and fill only the gaps. Never overwrite user content.
- **Resolve by join key, name second.** Cross-space lookups use the Brief's `task_manager_id` frontmatter where present. Names drift; ids don't. When you fall back to name matching and it succeeds, offer to backfill the join key. Tools that expose no id use the sentinel `task_manager_id: "name:<Project Name>"` — a `name:` prefix means "resolve by name" and is **not** a gap to warn about.
- **No-arg commands become pickers.** `/psa tasks`, `/psa add-context`, `/psa navigate`, `/psa archive`, `/psa convert` with no argument list the candidates and ask the user to pick; `/psa new` without a name asks for one after the goal conversation. When an index is empty (as it always is right after setup), pickers fall back to globbing the actual folders.
- **Naming rule.** From a display name like `Website Refresh`: Second Brain folder = Title-Kebab (`Website-Refresh`), File Share folder = lower-kebab (`website-refresh`), task-manager project = the display name verbatim.
- **Template filling.** When creating a file from a template: every `YYYY-MM-DD` becomes today's date unless the surrounding text says otherwise; every placeholder heading (`# Project Name`, `# System Name`, `# Task name`) becomes the real name; required-but-unknown values get `[NEEDS VERIFICATION]`, never a guess; placeholder table rows and `<!-- guidance comments -->` are removed once the section is really filled. Exception: a new system descriptor gets `**Last reviewed:** never` — it genuinely hasn't been.
- **Vault templates win.** If the user's Second Brain has a customized copy under `_Templates/` (scaffolded by setup), use it; fall back to the skill's `templates/` otherwise.
- **Guided tier is precise.** When instructing the user in their own tool, name the exact buttons, containers, and titles ("In Things, create an Area named `1 Projects`"), one step at a time, and wait for confirmation.
- **Surface gaps, don't swallow them.** Missing Brief, empty `task_manager_id`, unreachable path → warn and continue where safe, stop where not. Never silently invent a fact; mark unknowns `[NEEDS VERIFICATION]`.
- **Templates** for every file you create are in [templates/](templates/) — use them verbatim as starting points.

## Config shape

```json
{
  "version": 1,
  "capacity": { "max": 15 },
  "spaces": {
    "file_share":   { "path": "~/Documents" },
    "second_brain": { "path": "~/Documents/Second Brain", "tool": "obsidian" },
    "task_manager": { "tool": "things", "access": "guided", "notes": "" },
    "journal":      { "tool": "evernote", "access": "guided", "notes": "" }
  },
  "calendar": { "tool": "google-calendar", "access": "automated", "notes": "" }
}
```

`notes` holds anything tool-specific the adapter needs (an MCP server name, a CLI binary, a vault name, a database mapping). The two file spaces (`file_share`, `second_brain`) are always file-based and carry no `access` field — `second_brain.tool` just records the viewer. A skipped calendar or journal is `{ "tool": "none" }` with no `access` key. If a config's `version` is older than the skill expects, keep working with what's there and offer to re-run `/psa setup`.

The skill shells out for ordinary file and detection work (`mkdir`, `mv`, `ls`, `grep`, `which`, `osascript` on macOS) — worth knowing if you run restrictive tool permissions.

## Keywords

psa, projects, systems, archives, setup, project-management, second-brain, task-manager, journal, calendar, navigate, review, archive, convert, tasks, add-context, schedule, capacity, brief, gps
