# Walkthrough — blank drive to running system

A worked example of the full PSA loop, from nothing to a first archived project. Sample user: Sam, who keeps files on an external drive (`/Volumes/Work`), uses **Things** for tasks (guided tier), journals in **Apple Notes**, and lives in **Google Calendar**.

## 1. Install and set up

```bash
git clone https://github.com/1269/psa-method.git
cp -r psa-method/skills/psa ~/.claude/skills/
```

In Claude Code: `/psa setup`. The interview:

> **Where should your working files live?** → `/Volumes/Work`
> **Where should your thinking layer live?** → `/Volumes/Work/Second Brain` — "I'll open it in Obsidian" (the skill recommends pointing Obsidian at this folder as a vault: it becomes your local memory and knowledge base)
> **Task manager?** → Things. No MCP or CLI available → **guided tier**: "I'll tell you exactly what to create in Things and you'll click it."
> **Journal?** → Apple Notes. Scriptable → **automated tier**.
> **Calendar?** → Google Calendar, via a connected MCP → **automated tier**.
> **How many projects at once?** → "20?" → *"Recommended is 8–15 for a quarter's worth of focus — you can always lower it later. Keep 20?"* → Sam settles on 12.

Config written to `~/.psa/config.json`. The scaffold plan is shown, Sam confirms, and:

- `/Volumes/Work/1_Projects/`, `2_Systems/`, `3_Archives/completed-projects/` created
- Second Brain: `1 Projects/projects.md`, `_Templates/`, `2 Systems/systems.md`, `3 Archives/archives.md` created
- Things (guided): "Create an Area named `1 Projects`" → done → "`2 Systems`" → done. (No `3 Archives` in the task manager — archived items leave it entirely.)
- Apple Notes (automated): three folders created directly
- Calendar: smoke-tested by listing today's events

Tour: the daily loop, then *"create your first project: `/psa new`."*

## 2. First project

`/psa new website-refresh`

- **Capacity gate**: 0 of 12 — plenty of room.
- **Goal check**: "What goal does this serve, which system does it build or maintain?" → "Freelance income; it maintains my personal-brand system (which doesn't exist yet — this project will create it)."
- Created on confirm: `Second Brain/1 Projects/Website-Refresh/Brief.md` (GPS sections filled from the conversation), `/Volumes/Work/1_Projects/website-refresh/`, and — guided — a Things project named `Website Refresh` inside the `1 Projects` area. The skill asks Sam for the project's link (Things → right-click → Copy Link) and records it as the Brief's join key.
- Sam adds three first tasks while the context is warm.

## 3. Mirror and arm tasks

`/psa tasks website-refresh` — guided tier, so the skill asks Sam to paste the task list from Things. It writes `tasks/001-audit-current-site.md`, `002-rewrite-homepage-copy.md`, `003-migrate-to-new-host.md` — each a **skeleton**: contract frontmatter, a description grounded in the Brief, draft acceptance criteria.

`/psa add-context` — the picker shows the three backlog tasks; Sam picks `003`. The skill reads the Brief, indexes the repo folder, and composes the armed brief: refined title (`Migrate site to new host with zero downtime`), testable acceptance criteria, a test protocol ("curl the new host → verify 200 and correct title tag…"), references to real paths, out-of-scope lines, and the working-constraints block. Sam approves; status flips to `ready`; the Things task gets a one-line pointer note.

That `ready` task file is now something Sam can hand to any agent — or work through personally.

## 4. Schedule the week

`/psa schedule` — one task `ready`, two still `backlog`. Calendar is automated: the skill pulls the week, finds Tuesday 9–11 free inside working hours, and proposes the migration for Tuesday (deep block) — noting that `001` and `002` are still skeletons: "arm them with `/psa add-context` and I can schedule them too." Sam approves; the event appears in Google Calendar, linking to the task file.

## 5. Review weekly

Sunday: `/psa review` runs the 7 checks. For the drift check, Things is guided tier, so the skill asks: "paste your Things project list and I'll diff it against the index." Findings: `Brief.md` for Website-Refresh untouched for 34 days ("update or confirm it's still accurate?"), and — from Sam's pasted list — a Things project `Holiday Planning` that exists in no index ("adopt it as a project with `/psa new`, or leave it outside PSA?"). Each finding comes with its fix; Sam approves what makes sense.

## 6. Finish deliberately

Two months later the site has shipped. Sam runs `/psa archive website-refresh`… and the skill notices the Brief said this project would *create the personal-brand system*, so it suggests `/psa convert` instead:

`/psa convert website-refresh` → close-out conversation (outcome achieved; the site and its deploy process carry forward; two open tasks become recurring maintenance) → system defined: `personal-brand`, purpose "keep the site and public presence current," monthly cadence → on confirm: descriptor written to `2 Systems/personal-brand/personal-brand.md`, the working folder moves to `2_Systems/personal-brand/`, Things gets a `Personal Brand` list under `2 Systems` with the maintenance tasks on monthly repeat, the Brief is archived with a conversion note, both indexes update.

The project is gone. The system it built remains. That's the method.

## Where to go next

- Cap in the wrong place? Re-run `/psa setup` — it re-interviews and updates the config without touching your content.
- Read [docs/method.md](../docs/method.md) for the doctrine behind each move.
- Fork the repo and make the method yours.
