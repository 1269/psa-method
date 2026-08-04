# PSA Method

The shared language of the PSA (Projects, Systems, Archives) method — a lifecycle-based way to organize work, knowledge, and assets so that both you and your AI agents can navigate everything you own.

This file is a glossary: for each concept, the term to use and the near-synonyms to avoid (`_Avoid_`), so you, your docs, and your agents all mean the same thing by the same word.

## Language

### The lifecycle

**Project**:
A finite piece of outcome-driven work. A project exists either to build a new system or to maintain an existing one, and it ends — completed into Archives or converted into a System.
_Avoid_: initiative, workstream, epic

**System**:
An ongoing area of responsibility with no end date. Systems are maintained on a review cadence and outlive the projects that create them.
_Avoid_: area, domain, department

**Archive**:
Completed, retired, or reference material — kept for recall, learning, and reuse, and removed from active surfaces.
_Avoid_: backup, trash, cold storage

**Convert**:
The lifecycle move that closes a project and turns its output into a System with a review cadence. The method's signature move.
_Avoid_: migrate, promote

**Capacity**:
The hard limit on simultaneously active projects (recommended 8–15). New projects above capacity require finishing or archiving something first.
_Avoid_: WIP limit, bandwidth

### The spaces

**Space**:
Any tool that mirrors the Projects / Systems / Archives structure. The defining property of a space is that you can create a Projects area, a Systems area, and an Archives area inside it.
_Avoid_: layer, silo, location

**File Share**:
The space holding working files — code, assets, production files. The "working layer."
_Avoid_: documents folder, drive, storage

**Second Brain**:
The space holding thinking — briefs, descriptions, learnings, task files. Always a local folder of markdown files you own; your local memory and knowledge base. The "thinking layer."
_Avoid_: notes app, wiki

**Task Manager**:
The space holding next actions and recurring maintenance. Any task tool works (or none — the markdown tier). The "doing layer."
_Avoid_: todo app, project management tool

**Journal**:
The space where raw thoughts and daily entries are captured before being processed into the other spaces. The "capture layer."
_Avoid_: diary, log

**Calendar**:
A PSA-aware component — not a space (it holds no Projects/Systems/Archives structure). It schedules work into your available time blocks and can be driven by agents.
_Avoid_: fifth space, scheduler

### The scopes

**Scope**:
One complete PSA structure plus the people who share it. Every scope has its own spaces, indexes, archives, and review cadences; a person can belong to several (personal, company, side-business). Spaces exist per scope — your personal Second Brain and a company's shared one are two instances of the same space.
_Avoid_: workspace, tenant, org

**Convention**:
A scope's published expectations for its members' machines — local layout, required clones, expected tool connections. Ships as agent instructions in the scope's Second Brain, so every member's agent loads the same rules.
_Avoid_: policy, standard, guidelines

**Audit**:
The check of a machine against the Conventions of the scopes it belongs to — health of the structure, where Review is health of the work.
_Avoid_: conformance check, lint

**Pointer Entry**:
A one-line entry in a personal index marking active work that lives in another scope. It counts against personal capacity and links to the canonical Brief; the owning scope keeps the real record.
_Avoid_: mirror, copy, shortcut

### The files

**Brief**:
A project's descriptive file (`Brief.md`) in the Second Brain, written with the GPS framework. One per project.
_Avoid_: spec, PRD, readme

**GPS**:
The three sections every Brief answers — Goal (what and why), Plan (how and when), System (how progress is tracked).

**Descriptive File**:
The markdown file named after its folder (`{folder-name}.md`) that explains what the folder is. System folders follow this convention (`my-system/my-system.md`); project folders use `Brief.md` instead, and the three top-level indexes are their own convention.
_Avoid_: about.md, index.html

**Index**:
One of the three top-level lists in the Second Brain — `projects.md`, `systems.md`, `archives.md` — naming everything active or archived in that stage.

**Task File**:
One markdown file per task (`tasks/NNN-slug.md`) inside a project's Second Brain folder, carrying the task's frontmatter contract and body.
_Avoid_: ticket, card, issue

**Skeleton**:
A task file as first created by `/psa tasks` — frontmatter, description, and criteria stubs, not yet ready for an agent.

**Armed**:
The state of a task file after `/psa add-context` expands it into a self-contained brief an agent can execute cold. Arming moves status from `backlog` to `ready`.
_Avoid_: groomed, refined, enriched

### The wiring

**Config**:
The single file at `~/.psa/config.json` recording where each space lives and how each tool is accessed. Written by `/psa setup`, read by every command.

**Access Tier**:
How the skill drives a tool: `automated` (an integration the agent operates directly), `guided` (the agent gives step-by-step instructions, the human executes), or `markdown` (the structure lives as files, no external tool).
_Avoid_: mode, integration level

**Adapter**:
The tool-specific instructions that make a tier work for a given tool (e.g. the OmniFocus JXA adapter for the automated tier).

**Join Key**:
The durable id stored in a Brief's frontmatter (`task_manager_id`) linking the Second Brain folder to the task manager project. Names drift; the join key doesn't.
_Avoid_: foreign key, reference
