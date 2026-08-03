# /psa (status), lists, navigate, review

Read-mostly commands. All paths come from the config; `<sb>` = second_brain path, `<fs>` = file_share path.

## `/psa` — status overview

1. Read the three indexes: `<sb>/1 Projects/projects.md`, `<sb>/2 Systems/systems.md`, `<sb>/3 Archives/archives.md`.
2. Count active projects. If the task manager tier is `automated`, count active projects there too and note any mismatch with the index. If `guided`/`markdown`, the index is the source of truth.
3. Count task files by status across `<sb>/1 Projects/*/tasks/*.md` (grep frontmatter `status:`): how many `backlog` (skeletons, not yet armed), `ready` (armed), `in-progress`.
4. Present:

```
**PSA Status**

| Metric | Value |
|---|---|
| Active projects | X / <capacity.max> |
| Capacity | Green (< max-3) / Yellow (max-3 … max-1) / Red (at max) |
| Systems | N active — M past review cadence |
| Task pipeline | X backlog · Y ready · Z in progress |

**Projects:** <one line each from projects.md>
**Systems needing review:** <any past cadence>
**Suggested next:** <e.g. "2 task skeletons not yet armed — /psa add-context">
```

## `/psa projects` | `/psa systems` | `/psa archives`

Single-stage list views. Read the matching index, enrich lightly:

- **projects** — for each active project: name, goal one-liner (from its Brief), Brief last-modified date, open task-file count. Flag briefs untouched 30+ days.
- **systems** — for each system: name, purpose one-liner (from its descriptor), review cadence, last-reviewed date. Flag any past cadence.
- **archives** — categories and their counts, plus the 5 most recent additions.

## `/psa navigate [name]`

Find everything about one project or system across all spaces. No argument → list active projects + systems, ask which one.

1. **Second Brain**: glob `<sb>/**/*<name>*`; read the Brief or descriptor if found; note the `task_manager_id` join key.
2. **File Share**: glob `<fs>/{1_Projects,2_Systems,3_Archives}/*<name>*`.
3. **Task Manager**: per tier — `automated`: look up by join key (fall back to name match; offer to backfill the join key on success). `guided`: tell the user what to look for. `markdown`: the task files found in step 1 are the answer.
4. **Journal**: per tier — `automated`: search the tool for the name; `markdown`: glob the journal folder; `guided`: note where the user should look themselves.
5. Present a unified view: where it lives in each space, its status, its open tasks, and the one file to read first (Brief or descriptor).

## `/psa review` — 7-point health check

Run every check, then present findings as an actionable list — each finding with a suggested fix the user can approve. Fix only on approval.

1. **Capacity** — active project count vs `capacity.max`.
2. **Stale briefs** — any `Brief.md` not modified in 30+ days (file mtime).
3. **Orphaned folders and index drift** — `<fs>/1_Projects/*` and `<fs>/2_Systems/*` with no matching Second Brain folder, and vice versa (match loosely: kebab/space-insensitive). Also diff `<sb>/1 Projects/*/` and `<sb>/2 Systems/*/` against their indexes — a folder created by hand is invisible to capacity counts and pickers until it's indexed.
4. **Missing descriptors** — `<sb>/2 Systems/*/` folders without their `{name}.md`.
5. **Task-manager drift** — projects in the task manager missing from `projects.md` or vice versa. Per tier: `automated` — diff directly; `guided` — ask the user to paste their project list and diff that (skip gracefully if declined); `markdown` — nothing to drift, skip.
6. **Archive candidates** — projects whose Brief says done, whose task-manager project is completed, or with zero open tasks and no activity in 30+ days.
7. **System review cadence** — systems whose descriptor's cadence implies a review that hasn't happened. Use the descriptor's last-reviewed date; `never` (a freshly created system) means the clock starts at the descriptor's creation, not "overdue on day one." No recorded date at all → fall back to file mtime.

End with one suggested next action, not a lecture. Report metrics plainly.
