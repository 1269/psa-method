# The PSA Method

PSA (Projects, Systems, Archives) is a lifecycle-based approach to organizing work, knowledge, and assets. It enforces focus through a strict project cap, connects every project to a goal, and leaves behind durable systems instead of piles of finished work.

It is also built to be **agent-ready**: every part of the structure is legible to AI agents — plain folders, plain markdown, one config file — so an agent can navigate your whole working world, create projects, arm tasks, and schedule work without guessing where anything lives.

## The three stages

Everything you own is in exactly one of three lifecycle stages.

### Projects

Finite, outcome-driven work. Every project has a clear goal, a plan to get there, and a system to track progress. Projects die — either **archived** (outcome achieved or abandoned) or **converted** into a System.

**The core principle:** a project exists either to *build a new system* or to *maintain an existing one*. If a project doesn't connect to a system, question why it's on the list. The point of finite work is to leave behind durable, sustainable systems.

- **Capacity**: 8–15 active projects, hard cap (you choose your number during setup). At the cap, something must finish before something new starts.
- **Structure**: each project has a Brief (GPS framework) in the Second Brain, a working folder in the File Share, and a project in the Task Manager.
- **Lifecycle**: Idea → Active Project → Completed (Archive) or Converted (System)

### Systems

Ongoing areas of responsibility with no end date — maintained, reviewed, and improved continuously. They are the infrastructure that makes projects possible.

- **Examples**: your productivity routines, content production, health tracking, finances, your home lab, this very PSA setup
- **Review cadence**: each system declares its own pace — weekly, monthly, quarterly, or "as needed"
- **Key distinction**: systems don't finish; they evolve. A project might create or improve a system, but the system outlives the project.
- **Systems don't own transient work**: a system holds recurring infrastructure (runbooks, strategy, reusable assets). One-off deliverables live in Projects, even when produced under a system's umbrella.

### Archives

Completed, retired, or reference material — worth keeping for recall, learning, or reuse, but out of your active surfaces.

- **Categories**: completed projects, retired assets, learnings (courses, books, videos), reference material
- **Rule**: archived items leave the Task Manager. The Second Brain keeps summaries and insights; the File Share keeps raw assets.
- **Future**: your archive is a ready-made corpus for search and RAG pipelines — one more reason to keep it structured.

## The four spaces

A **space** is any tool that mirrors the Projects / Systems / Archives structure. That's the defining property: you can create a Projects area, a Systems area, and an Archives area inside it. PSA ships with four standard spaces:

| Space | Role | Holds |
|---|---|---|
| **File Share** | Working layer | Code, assets, production files, configs |
| **Second Brain** | Thinking layer | Briefs, descriptions, learnings, task files |
| **Task Manager** | Doing layer | Next actions, recurring maintenance |
| **Journal** | Capture layer | Raw thoughts and daily entries, before processing |

And one PSA-aware component that is *not* a space:

| Component | Role |
|---|---|
| **Calendar** | Time layer — schedules work into available blocks; agents may manage it |

The calendar holds no Projects/Systems/Archives structure (see [ADR-0002](adr/0002-journal-is-a-space-calendar-is-not.md)); it's where the other spaces' work meets your actual hours.

### How the spaces connect

- A **project** has a working folder in the File Share, a `Brief.md` in the Second Brain, and a project in the Task Manager.
- A **system** has working files in the File Share, a descriptive `{name}.md` in the Second Brain, and recurring maintenance in the Task Manager.
- An **archive** has raw files in the File Share, summaries in the Second Brain, and no presence in the Task Manager.
- The **journal** captures raw input; processing routes each entry's contents to the space where it belongs.

### The standard folder shape

```
File Share root/                    Second Brain root/
├── 1_Projects/                     ├── 1 Projects/
│   └── my-project/                 │   ├── projects.md          ← index
├── 2_Systems/                      │   ├── _Templates/
│   └── my-system/                  │   └── My-Project/
└── 3_Archives/                     │       ├── Brief.md
    └── completed-projects/         │       └── tasks/
                                    │           └── 001-first-task.md
                                    ├── 2 Systems/
                                    │   ├── systems.md           ← index
                                    │   └── my-system/
                                    │       └── my-system.md     ← descriptor
                                    └── 3 Archives/
                                        ├── archives.md          ← index
                                        └── completed-projects/
```

The numeric prefixes keep the three stages sorted in lifecycle order everywhere. The Task Manager and Journal mirror the same three areas using whatever their tool calls a container (folder, area, notebook, section).

## The files

### Descriptive file convention

System folders in the Second Brain carry a markdown file named after the folder itself (`my-system/my-system.md`) — the system's descriptor. Project folders carry a `Brief.md` instead. These files serve two readers at once: humans browsing, and AI agents navigating. Three top-level **indexes** — `projects.md`, `systems.md`, `archives.md` — list everything in each stage.

### The GPS Brief

Every project's `Brief.md` answers three questions:

- **Goal** — what you're building, why it matters, what success looks like
- **Plan** — key milestones, time commitment, dependencies
- **System** — how you track progress, and which system this project builds or maintains

The Brief's frontmatter carries the **join key** (`task_manager_id`) — the durable id of the matching Task Manager project. Folder names and project names drift; ids don't. Every cross-space lookup resolves by join key first, name second.

### Task files

Tasks can live twice: as entries in your Task Manager (the doing view) and as markdown **task files** in the project's Second Brain folder (the agent-ready view). `/psa tasks` mirrors a project's tasks into skeleton files; `/psa add-context` arms one into a self-contained brief an agent can execute cold. See the [task file contract](../skills/psa/references/tasks.md).

If your Task Manager tier is `markdown`, the task files *are* your task manager — one source of truth, zero apps.

## Access tiers

PSA works with the tools you already use. During setup, each tool gets an **access tier**:

| Tier | Meaning |
|---|---|
| `automated` | The agent drives the tool directly (MCP server, CLI, AppleScript/JXA) |
| `guided` | The agent tells you exactly what to do in the tool, step by step, and continues once you confirm |
| `markdown` | The structure lives as plain files — no external tool at all |

The File Share and Second Brain are always file-based. The Second Brain must be a **local folder of markdown files** — that's doctrine, not a limitation (see [ADR-0003](adr/0003-files-first-second-brain.md)): files are the interface agents speak natively, and your thinking layer should be something you own outright. We recommend [Obsidian](https://obsidian.md) to view and edit it — accessible, ubiquitous, local-first. Any markdown folder works; if you use something else, the skill will work with you to find a workable folder and note that you're customizing slightly outside the standard path.

## Where finished things live

A finished website, app, or deliverable belongs under the **system that will maintain it** — the folder of whichever system it serves. A project *builds* the thing; on completion, the durable home is the system folder. Repos live on your git host (link or nest them under the system folder); large binary assets live in the File Share; the Second Brain holds the thinking.

## Working the method

The daily loop is small:

1. **Capture** into the Journal (or straight into the Task Manager inbox).
2. **Process** captures to where they belong — a task, a Brief note, an idea, reference.
3. **Work** from the Task Manager; agents work from armed task files.
4. **Review** weekly: `/psa review` finds drift (stale briefs, orphans, capacity, overdue system reviews).
5. **Finish**: archive or convert every project that ends. Nothing just fades out.

## Roadmap

- **Shared PSA** — connecting multiple people's PSA systems: shared project views without overwriting each other, while everyone keeps their own private Second Brain. The files-first doctrine is what makes this mergeable.
- **Journal routing** — a `/psa process-journal` verb that reads your latest entry, splits it into items, and proposes a destination for each (task, brief note, idea, archive) behind a confirmation gate.
- **More agents** — support for other coding agents like Codex and Gemini, coming soon. The structure is already agent-agnostic — any agent that can read files can navigate a PSA system today; this ports the skill's instructions to their native formats.
- **More adapters** — worked automated-tier examples beyond OmniFocus, as the community contributes them.

## Make it yours

PSA is a baseline, not a prescription. Fork the repo, rename the spaces, add your own verbs, wire your own tools. The one thing we'd urge you to keep: the lifecycle discipline — a hard project cap, and every project ending in an archive or a system.
