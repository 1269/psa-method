# PSA — Projects, Systems, Archives

**A systems-first productivity method your AI agents can run with you.**

PSA is **systems-first thinking**: the durable unit of your working life is the **System** — ongoing, no end date, maintained through automated process on a review cadence. **Projects** are how systems get built and improved — outcome-driven work with as many steps and phases as it needs, but it ultimately ends. What's finished becomes an **Archive**, kept for recall and reuse. Everything you own — files, notes, tasks, journal entries — lives in exactly one of those three stages, mirrored across every tool you use in plain folders and plain markdown — which means an AI agent can navigate your whole working world without guessing where anything lives.

PSA ships as a single [Claude Code](https://claude.com/claude-code) skill: `/psa`. One command sets the whole thing up, and the same command runs it day to day.

## Why this exists

- **Systems over output.** The goal isn't to finish more projects; it's to end up with systems that largely run themselves — automated maintenance, agents working on a cadence, less of you required over time. Projects are the interventions that get you there.
- **Focus by design.** A hard cap on active projects (recommended 8–15). New work requires finishing old work.
- **Nothing just fades out.** Every project ends deliberately: archived, or **converted** into a system with a review cadence. Finished work leaves behind infrastructure, not clutter.
- **Agent-ready by construction.** Briefs, indexes, and task files are markdown with a small, stable contract. Your agents read the same files you do — and `/psa add-context` expands any task into a self-contained brief an agent can execute cold.
- **Bring your own tools.** Any task manager (OmniFocus, Things, Todoist, Asana, Notion, none). Any journal (Evernote, Day One, files). Any calendar. The skill drives what it can automate and guides you through the rest.

## Install

Requires [Claude Code](https://claude.com/claude-code). PSA installs as a personal skill, so it's available in every project, from any directory. (Automated control of local apps like OmniFocus or Apple Notes requires macOS; MCP- and CLI-backed tools work on any platform — everything else falls back to guided mode.)

```bash
git clone https://github.com/1269/psa-method.git
mkdir -p ~/.claude/skills
cp -r psa-method/skills/psa ~/.claude/skills/psa
```

Restart Claude Code and type `/psa` — it should tell you PSA isn't set up yet and offer to run setup. Then:

```
/psa setup
```

Setup interviews you (where's your file share, second brain, task manager, journal, calendar, and how many projects you want to cap at), writes one config file to `~/.psa/config.json`, and scaffolds all four spaces. Answered something wrong? Re-run `/psa setup` anytime — it re-interviews and updates the config without touching your content.

> 🎬 **Video walkthrough** — setting up a hard drive with PSA from start to finish: *coming soon*.

**Updating:** `cd psa-method && git pull && cp -r skills/psa ~/.claude/skills/psa`
**Uninstalling:** `rm -rf ~/.claude/skills/psa ~/.psa` — your files, notes, and tasks are untouched.
**Multiple machines:** run `/psa setup` on each; the config holds machine-local paths and doesn't travel. Your spaces sync however you already sync them.

## What it touches

PSA never deletes. Setup only creates missing folders — it never moves or renames anything you already have, so it's safe to run against an existing Documents folder. The only operations that move files are `/psa archive` and `/psa convert`, which relocate a finished project's folder into `3_Archives/` or `2_Systems/` — and every command that writes anything shows you the full plan and waits for a yes first.

## The four spaces

A **space** is any tool that mirrors the Projects / Systems / Archives structure:

| Space | Layer | Holds |
|---|---|---|
| **File Share** | Working | Code, assets, production files |
| **Second Brain** | Thinking | Briefs, notes, learnings, task files — always local markdown ([why](docs/adr/0003-files-first-second-brain.md); we recommend [Obsidian](https://obsidian.md)) |
| **Task Manager** | Doing | Next actions, recurring maintenance |
| **Journal** | Capture | Raw thoughts, daily entries, before processing |

Plus a **Calendar** — not a space, but a PSA-aware component: it schedules ready work into your available time blocks, and agents can manage it ([why it's not a space](docs/adr/0002-journal-is-a-space-calendar-is-not.md)).

## The command

| | |
|---|---|
| `/psa help` | What PSA is, every command, where things live — works before setup too |
| `/psa setup` | Interview → config → scaffold all spaces → tour (re-run anytime to change answers) |
| `/psa` | Status: capacity, projects, systems needing review, task pipeline |
| `/psa projects` / `systems` / `archives` | List one lifecycle stage |
| `/psa navigate [name]` | Find something across every space |
| `/psa review` | 7-point health check (capacity, stale briefs, orphans, drift…) |
| `/psa new [name]` | Create a project across all spaces — capacity-gated, with a Goal/Plan/System brief |
| `/psa archive [name]` | Close a finished project into Archives |
| `/psa convert [name]` | Turn a finished project into an ongoing System |
| `/psa tasks [project]` | Mirror a project's tasks into agent-readable task files |
| `/psa add-context [task]` | Expand one task into a self-contained brief an agent can execute cold |
| `/psa schedule` | Time-block ready work into your calendar |

Every command that writes shows you the plan first. Commands without arguments turn into pickers.

## Learn the method

- **[docs/method.md](docs/method.md)** — the full method: three stages, four spaces, the GPS Brief, capacity doctrine, access tiers
- **[examples/walkthrough.md](examples/walkthrough.md)** — a worked example: blank drive → running system → first project shipped
- **[examples/sample-vault/](examples/sample-vault/)** — the actual files the system produces: a filled Brief, a skeleton task, an armed task, the indexes
- **[CONTEXT.md](CONTEXT.md)** — the glossary; **[docs/adr/](docs/adr/)** — why key decisions went the way they did

## Make it yours

PSA is a baseline, deliberately. **Fork this repo.** Rename spaces, add verbs, wire your own tools, build your own agent runner on top of the task-file contract — the contract ignores fields it doesn't know, so extend freely. The goal of this repo is to get humans and their agents onto a shared page about how to run a linked system; your version of that page is supposed to diverge. Adapter recipes for new tools are the most useful thing to contribute back — see [CONTRIBUTING.md](CONTRIBUTING.md).

## Roadmap

- **Shared PSA** — PSA at company scale, now designed: the method repeats per **scope** (personal, company, …). A company scope keeps collab docs and assets on a shared drive, code *and* the shared Second Brain in the git org — a brain repo every member clones, carrying the scope's Convention as agent instructions so everyone's AIs load the same ground truth. A future `/psa audit` checks each machine against that Convention. Full design: [docs/shared-psa.md](docs/shared-psa.md).
- **Journal routing** — `/psa process-journal`: split a raw entry into items and file each where it belongs, behind a confirmation gate.
- **More agents** — support for other coding agents like Codex and Gemini, coming soon. The method is already agent-agnostic (plain folders, plain markdown, one config file); this brings the skill itself to their formats.
- **More adapters** — community-contributed automated-tier examples beyond OmniFocus.

## License

[MIT](LICENSE). Take it, run with it.
