# /psa tasks, /psa add-context — the task pipeline

The two-stage pipeline that turns task-manager entries into work AI agents can execute:

1. **`/psa tasks [project]`** — mirror a project's tasks into **skeleton** task files (markdown, one file per task) in the project's Second Brain folder.
2. **`/psa add-context [task]`** — **arm** one task: expand it into a self-contained brief an agent can pick up cold.

Skeletons are cheap and batch; arming is deliberate and per-task. A task file is the durable, agent-readable form; the task manager stays the human doing-view. If the task-manager tier is `markdown`, task files are the only form — `/psa tasks` then just scaffolds skeletons from a conversation with the user ("list the tasks for this project").

## The task file contract

Path: `<sb>/1 Projects/<Project>/tasks/NNN-slug.md` — `NNN` = next sequential 3-digit number in that folder; slug = kebab-case title, max 50 chars. (Systems use the same contract for their recurring maintenance: `<sb>/2 Systems/<system>/tasks/NNN-slug.md`.)

```yaml
---
title: "Task name"
task_id: ""            # id in your task manager; "PENDING" if file-born
status: "backlog"      # backlog | ready | in-progress | done | blocked | cancelled
priority: "medium"     # high | medium | low
dependencies: []       # NNN prefixes of task files this waits on
created: "YYYY-MM-DD"
updated: "YYYY-MM-DD"
due_date: null         # "YYYY-MM-DD" or null
tags: []
---
```

Rules:

- **`task_id` is the primary key** — when there is one. Re-runs match on it: update changed files in place, skip unchanged ones, never duplicate. `"PENDING"` marks a file-born task not yet created in the task manager (create it and backfill the id when the tier allows). **Never match on `"PENDING"` or empty** — on the markdown tier (and for any file-born task), the file's `NNN-slug` filename is the identity, and re-runs match on normalized title instead.
- **Status flow**: `/psa tasks` writes `backlog`; `/psa add-context` promotes to `ready`; agents/humans move `ready → in-progress → done` (or `blocked`/`cancelled`).
- **Priority mapping** when mirroring: flagged/starred → `high`; has a due date → `medium`; else `low`.
- **Tags**: slugify to valid markdown-frontmatter tags (lowercase; runs of non-alphanumerics → single hyphen).
- This is deliberately a minimal contract. If you build your own agent runner on top, add fields — the skill ignores fields it doesn't know.

## `/psa tasks [project]`

### 1. Resolve the project

No argument → picker: list active projects (index + task manager per tier), ask which. With an argument: resolve the Second Brain folder by the Brief's `task_manager_id` join key first, folder-name match second.

### 2. Pull the tasks (per tier)

- `automated`: pull the project's incomplete tasks via the adapter — name, id, notes, flagged/priority, due date, tags. Skip parent/group tasks (mirror leaves only).
- `guided`: ask the user to read their task list aloud or paste it; capture names + any dates/priorities they mention.
- `markdown`: ask the user what the tasks are (or accept a pasted list).

### 3. Plan, confirm, then write skeletons

Ensure `tasks/` exists. Inventory existing files (`task_id` lookup map + highest NNN; PENDING/empty ids match by filename/title per the contract). Classify every task as create / update / skip, and **show the user that table before writing anything** — per the confirm-before-writing convention. On approval, write: updates in place; new files from `templates/task.md`, with:

- Frontmatter per the contract above.
- **Description**: 2–4 sentences grounded in the Brief (read it first) — what this task involves and why it matters to the project.
- **Acceptance criteria**: 3–5 specific, testable conditions.
- **Subtasks**: 2–4 concrete steps.
- Preserve any original task-manager note verbatim under a quote block.

### 4. Summarize

Confirm what was written (any deltas from the approved plan are called out, not buried). Suggest: "Arm the next one you'll work: `/psa add-context`."

## `/psa add-context [task]`

### 1. Resolve the task

No argument → picker: list all task files in `status: "backlog"` across projects (grep frontmatter), `priority: high` first, then by due date. With an argument: match by task_id, filename, or fuzzy title.

### 2. Gather context

1. Read the project's `Brief.md` — goal and success criteria feed the brief. **If it's missing**, don't just warn: offer to create it from the Brief template right now, capturing the Goal in two quick questions (what's the outcome? which system does it build or maintain?) — that unblocks everything downstream. If the user declines, mark `Brief:`, `## Why this matters`, and `## References` as `[NEEDS USER INPUT]` and leave the task at `status: backlog` — a brief with an empty why isn't armed.
2. If the Brief's frontmatter has a `repo_path`, list the repo's top-level files (and one level into `src`/`lib`/`docs` if present) — an index only, don't read contents. If `repo_path` is empty, ask once; offer to backfill the Brief.
3. Check the task manager (per tier) for the task's current state and any notes.

### 3. Compose the armed brief

Rewrite the task file. Title rules: imperative mood, ≤60 chars, specific noun. Body template:

```markdown
# <Refined title>

## Context
- **Project:** <name>
- **Brief:** <relative link to Brief.md>
- **Repo:** <repo_path, or "[NEEDS VERIFICATION]">
- **Due:** <date or "none">

## Why this matters
<1–2 sentences pulled from the Brief's Goal — the agent shouldn't need to open the Brief to understand the why.>

## Acceptance Criteria
- [ ] <testable, concrete — WHAT done means>

## Test Protocol
<How to PROVE each criterion — commands, checks, expected output. "What done means" lives above; "how to verify it" lives here.>
1. <check> → verify: <expected observable result>

## Subtasks
- [ ] <carried forward from the skeleton, refined — never silently dropped>

## References
- `<real path from the repo index>` — <why it matters>

## Out of Scope
- <what NOT to do; related work that should become its own task>

## Working Constraints (for the agent picking this up)
- File paths in this brief were verified as of <date>. If a path doesn't resolve, stop and report — don't assume.
- If a fact is not in this brief or the linked Brief.md, mark it [NEEDS VERIFICATION] rather than asserting it.
- If you discover related work, capture it as a follow-up task; do not expand scope.
- Log decisions in the Work Log as you make them.

## Original notes
<verbatim user content, if any>

## Work Log
<!-- the agent documents actions and decisions here -->
```

Compose rules: pull Goal/criteria **verbatim** from the Brief, never paraphrase into vagueness; reference only **real** file paths from the step-2 index; criteria must be testable ("returns 401 when token missing", not "handles errors"); mark anything ambiguous `[NEEDS USER INPUT]` rather than guessing. The Working Constraints block travels with the brief so any consumer — this session, another agent, another model — sees the guardrails inline.

The armed body is an **expansion** of the skeleton, not a replacement document: carry the skeleton's Subtasks forward (refined), keep the Description's substance inside "Why this matters," and preserve the Work Log — arming never deletes what the skeleton captured.

### 4. Approve, then write

Show the user: original vs refined title, the full brief body, and what was added. On approval: update the task file (status → `ready`, updated date); then per tier, update the task manager entry with a short pointer note — `automated`: write it; `guided`: give the user the one line to paste; `markdown`: nothing to update, the file is the task. (Never paste the whole brief into a task manager — it goes stale; link/name the task file instead.)
