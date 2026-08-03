# /psa setup — Interview, Config, Scaffold, Tour

Set up a complete PSA system from scratch — or adopt an existing folder structure without breaking it. This command must be safe to run on a blank hard drive **and** on a Documents folder full of years of existing work.

## Phase 1: Interview

Ask one question at a time. Offer the default; accept any answer. Expand `~` in every path.

1. **File Share** — "Where should your working files live — code, assets, production files? This is the root that will hold `1_Projects/`, `2_Systems/`, `3_Archives/`."
   Default: `~/Documents` (or the drive/folder they're setting up, e.g. `/Volumes/Work`).

2. **Second Brain** — "Where should your thinking layer live — briefs, notes, learnings? This must be a local folder of markdown files."
   Default: `<file_share>/Second Brain`.
   Recommend **Obsidian** as the viewer: it's accessible, used everywhere, and this folder becomes your local memory and knowledge base — point Obsidian at the folder as a vault. Record their chosen viewer as `second_brain.tool`. If the user prefers another markdown setup (Logseq, VS Code, plain folder), work with them to find a folder that works, and note they're customizing slightly outside the standard path. If they push for an app database (Notion, Craft): the Second Brain must be files — explain briefly (agents read files natively; you own them outright) and help them pick a folder alongside whatever app they keep using.

3. **Task Manager** — "What do you use for tasks day to day?"
   Any tool is valid: OmniFocus, Things, Todoist, Asana, Notion, Apple Reminders… or none.
   - If none / "just files": tier is `markdown` — task files in the Second Brain are the task manager. Skip to Q4.
   - Otherwise, determine the **access tier** (see "Determining tiers" below).

4. **Journal** — "Where do you capture raw thoughts — daily entries, morning pages, voice notes?"
   Any tool: Evernote, Day One, Apple Notes, a markdown folder… or none (offer `<second_brain>/Journal/` as a markdown-tier journal; if they decline journaling entirely, record `"journal": { "tool": "none" }` with no `access` key and skip its scaffold). Determine tier as above.

5. **Calendar** — "What calendar do you live in? PSA uses it to schedule work into your available time blocks — agents can help manage it."
   Google Calendar, Apple Calendar, Outlook… or "skip" — a skipped calendar records `"calendar": { "tool": "none" }` with no `access` key (tier determination doesn't apply; `/psa schedule` will propose blocks as text only). Otherwise determine tier.

6. **Capacity** — "How many projects do you want to maintain at once? PSA recommends 8–15."
   Accept their number. If they answer above 15: accept it, but tell them plainly that it's highly recommended to keep active projects at 15 or fewer for the duration of a quarter or a defined block of work — they can lower the cap later and keep moving. Below 8 is fine — 8 is a recommendation, not a floor to enforce. Record whatever they choose.

### Determining tiers

For each tool (task manager, journal, calendar), check what's actually available in this session before asking the user to do anything manually:

1. **Automated** if you can drive it: an MCP server is connected for it, a CLI exists (`which <tool-cli>`), or it's a macOS app with a real scripting dictionary — use the `.sdef`/`NSAppleScriptEnabled` check in [adapters.md](adapters.md), NOT `id of app` (which passes for any installed app, scriptable or not).
2. **Guided** otherwise: the tool exists but you can't drive it — you'll instruct, they'll click.
3. **Markdown** if there is no tool: the structure lives as files.

Tell the user which tier each tool landed on and why.

## Phase 2: Write the config

Write `~/.psa/config.json` (create `~/.psa/` if needed) following [templates/config.example.json](../templates/config.example.json), populated from the interview. Show the final JSON to the user. If a config already exists, offer each current value as the default during the interview ("keep `~/Documents`?"), diff the result against the old config, and confirm before replacing. If `second_brain.path` or `file_share.path` changed, warn that existing content is NOT moved — pointing the config elsewhere strands it unless the user moves the folders themselves.

## Phase 3: Present the scaffold plan

Before creating anything, show the complete plan — every folder, file, and tool container that will be created, per space, marking anything that **already exists** as `(exists — will keep)`. Wait for explicit confirmation.

## Phase 4: Scaffold

Everything is idempotent: existing items are reported and kept, only gaps are filled. Never overwrite a file that has content.

### File Share

```
<file_share>/1_Projects/
<file_share>/2_Systems/
<file_share>/3_Archives/completed-projects/
```

### Second Brain

```
<second_brain>/1 Projects/projects.md          ← from templates/projects.md
<second_brain>/1 Projects/_Templates/Brief.md  ← from templates/Brief.md
<second_brain>/1 Projects/_Templates/task.md   ← from templates/task.md
<second_brain>/2 Systems/systems.md            ← from templates/systems.md
<second_brain>/2 Systems/_Templates/system.md  ← from templates/system.md
<second_brain>/3 Archives/archives.md          ← from templates/archives.md
<second_brain>/3 Archives/completed-projects/
<second_brain>/3 Archives/learnings/
<second_brain>/3 Archives/reference/
```

If the journal is markdown-tier at `<second_brain>/Journal/`, also scaffold `Journal/1 Projects/`, `Journal/2 Systems/`, `Journal/3 Archives/` here (this replaces the Journal block below).

Optionally offer: a `goals.md` at the Second Brain root (from `templates/goals.md`) — "a one-page list of your current goals; `/psa new` will read it when checking whether a project deserves a slot." Skip freely if declined.

### Task Manager (per tier)

Create **two** top-level containers using the tool's native concept — folder, area, project group, whatever it has: `1 Projects` and `2 Systems`. No `3 Archives` here: archived items leave the task manager entirely (that's the method), so there is nothing for an archive container to hold.

- `automated`: create them via the adapter; report what was created.
- `guided`: instruct one container at a time ("In Todoist, create a project called `1 Projects` at the top level") and wait for confirmation on each.
- `markdown`: nothing to create — task files in the Second Brain are the task manager. Say so.

### Journal (per tier)

Three containers in the journal tool (notebooks in Evernote, folders in Apple Notes, subfolders in a markdown journal): `1 Projects`, `2 Systems`, `3 Archives`. Daily entries keep living wherever the user already writes them — the containers are where processed entries get filed, and the journal-routing verb on the roadmap will use them directly, so creating them now saves a step later. Per tier, as above.

### Calendar

Nothing to scaffold — the calendar holds no PSA structure. Confirm the tool is reachable if `automated` (e.g. list today's events as a smoke test).

## Phase 5: Tour

Close with a short tour, concretely referencing *their* paths and tools:

1. What was created, per space (one line each).
2. The daily loop: capture in the journal → process to the spaces → work from the task manager → `/psa review` weekly.
3. "Create your first project: `/psa new my-first-project`."
4. "See where you stand anytime: `/psa`."

## Adoption mode (existing structure)

If the interview reveals the user already has folders that resemble PSA (or any organized structure), do **not** propose moving or renaming their content during setup. Scaffold only the missing standard pieces, map their existing folders in the config, and tell them `/psa review` will surface anything that doesn't line up — adoption is gradual, setup is not a migration.
