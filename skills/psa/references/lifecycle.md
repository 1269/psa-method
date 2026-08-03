# /psa new, archive, convert — the lifecycle commands

These commands move things between lifecycle stages, touching every space. All are confirmation-gated: show the full plan, then act. `<sb>` = second_brain path, `<fs>` = file_share path.

**Move preconditions (every folder/file move below).** Check both ends before any `mv`:

| Source | Destination | Do |
|---|---|---|
| exists | absent | Move normally |
| absent | exists | Already done (user beat you to it) — report and skip |
| absent | absent | Stop: nothing to move — surface it, ask what happened |
| exists | exists | Stop and ask — **never merge or nest**. A blind `mv` here silently creates `dest/name/name/` |

The same idempotency applies to these moves as to creates: detect done work, fill gaps, never mangle.

## `/psa new [name]` — create a project

### 1. Capacity gate

Count active projects (index + task manager per tier).

- **At `capacity.max`**: STOP. Show the active list and ask which project should finish, archive, or convert first. Creating above cap requires the user to explicitly override — and if they do, remind them once, plainly, that the cap is the method.
- **Within 3 of max**: proceed, but note the remaining headroom.

### 2. Goal check

Ask: **"What goal does this project serve, and which system does it build or maintain?"**

- If `<sb>/goals.md` exists, read it and check the answer against it — name the match, or note the miss.
- If capacity is tight or the idea sounds impulsive, add the two-question gut check: *Will this still matter in 30 days? Does it build or maintain a system?* A project that does neither is a candidate for the idea list, not a project slot. Flag once, respect their decision.

### 3. Create across spaces (on confirmation)

1. **Second Brain**: `<sb>/1 Projects/<Name>/Brief.md` from `templates/Brief.md` — fill the frontmatter per the template-filling convention (`created` = today, `status: active`, real `# <Name>` heading; `task_manager_id` comes from step 3.3, `repo_path`/`goal_link` filled if known, else left empty) and the GPS sections from the conversation (goal from step 2; ask briefly for plan and tracking if not obvious). Add the project to `projects.md`.
2. **File Share**: `<fs>/1_Projects/<name>/` (kebab-case).
3. **Task Manager** (per tier): create the project inside the `1 Projects` container. `automated`: create it, capture its id, write it into the Brief's `task_manager_id`. `guided`: instruct, then ask the user to paste/report the project's id or link if the tool exposes one (else leave the join key empty). `markdown`: nothing — the Brief plus a `tasks/` folder is the project.
4. Offer to capture 2–3 first tasks while the context is warm (into the task manager per tier, or straight to task files).

### 4. Confirm

Show what was created in each space, and end with: "Fill out the rest of the Brief, then `/psa tasks <name>` when you're ready to mirror tasks for agents."

## `/psa archive [name]` — close a completed project

No argument → list active projects, ask which. Then:

### 1. Verify completion

Read the Brief; check for open tasks per tier (`automated`: query the tool; `guided`: ask the user to check; `markdown`: grep the project's task files for non-terminal `status`). If open tasks exist, ask: complete now, move to another project/system, or drop? If the Brief's goal reads unmet, ask whether this is a completion or an abandonment — both are valid archives; record which. And if the Brief says this project was meant to **create or become a system** (goal or System section says so), suggest `/psa convert` instead of archiving — converting is probably what the user actually wants.

### 2. Execute (on confirmation)

1. **Task Manager**: mark the project complete/archived per tier (`markdown`: set each remaining task file's `status` to its terminal value — the folder move in step 2 is the archival).
2. **Second Brain**: move `<sb>/1 Projects/<Name>/` → `<sb>/3 Archives/completed-projects/<Name>/`. Append a short close-out note to the Brief: date, outcome (achieved / abandoned + one line), anything learned worth keeping.
3. **File Share**: move `<fs>/1_Projects/<name>/` → `<fs>/3_Archives/completed-projects/<name>/`.
4. **Indexes**: remove from `projects.md`; add to `archives.md` with the close-out one-liner.

### 3. Confirm

Summary of what moved where, plus the freed capacity count.

## `/psa convert [name]` — project becomes a system

The method's signature move: the project ends, its output lives on with a review cadence. No argument → list active projects, ask which.

### 1. Load context

Read the Brief; list open tasks per tier.

### 2. Close-out conversation

1. **Outcome** — did it achieve its goal, or is it "good enough to maintain"?
2. **What carries forward** — which artifacts, configs, processes become the system?
3. **What drops** — WIP and experiments that don't need to survive.
4. **Open tasks** — each one: move to the system as recurring maintenance, finish now, or drop?

### 3. Define the system

- **Name** (kebab-case for folders) and one-line **purpose**.
- **Review cadence**: watch-and-revisit (a specific date), periodic (weekly/monthly/quarterly), or as-needed.
- **Review triggers** — signals that should prompt attention outside cadence.

### 4. Execute (on confirmation)

1. **Second Brain**: create `<sb>/2 Systems/<system-name>/<system-name>.md` from `templates/system.md` (purpose, origin project + date, components, cadence). Move project files that carry forward into the system folder — but NOT the Brief.
2. **File Share**: move `<fs>/1_Projects/<project>/` → `<fs>/2_Systems/<system-name>/`.
3. **Task Manager** (per tier): mark the project complete; create a single-action list/project named `<system-name>` inside the `2 Systems` container; add the recurring maintenance tasks from step 2.4 with the cadence as their repeat. `markdown` tier: no containers — write the maintenance tasks as task files under `<sb>/2 Systems/<system-name>/tasks/` (same contract as project task files).
4. **Archive the Brief**: move it to `<sb>/3 Archives/completed-projects/<Project-Name>/Brief.md` with a conversion note appended: date, outcome, converted-to system name, why maintenance rather than done.
5. **Indexes**: remove from `projects.md`; add to `systems.md` (purpose + cadence); note the conversion in `archives.md`.

### 5. Confirm

```
✓ Project "<name>" closed out
✓ System "<system-name>" created
  - Second Brain: 2 Systems/<system-name>/<system-name>.md
  - File Share: <fs>/2_Systems/<system-name>/
  - Task Manager: <system-name> under 2 Systems (cadence: <cadence>)
✓ Brief archived with conversion note
✓ Indexes updated
```
