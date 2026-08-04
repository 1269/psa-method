# Shared PSA — PSA at company scale

**Status: design.** This is the agreed shape of the shared layer. Nothing in `skills/psa/` implements it yet — the skill learns about scopes when `/psa audit` is built. Until then, this doc is the reference for how multiple people, each with their own AI agents, run PSA together.

## The problem

PSA gets one person and their agents onto the same page: plain folders, plain markdown, one config file, so nothing has to be guessed. A company is several people — each with their *own* agents, their own machines, their own layouts. The shared layer exists to extend the same guarantee across all of them: every human and every agent in the company navigates shared work without guessing where anything lives.

## Scopes

The method repeats. A **scope** is one complete PSA structure plus the people who share it. Your personal PSA is a scope of one. A company is a scope of many. The method is identical at every scope — three lifecycle stages, indexes, briefs, capacity discipline, review cadences. What changes is who can see it.

- **Personal scope** — your machine: local File Share, local Second Brain, your task manager, your journal.
- **Company scope** — shared tools: the File Share is a shared drive (e.g. Google Drive), and both code and thinking live in the company git host (e.g. a GitHub org).

A person can belong to several scopes at once (personal, company, side-business). Scopes stay structurally independent — each has its own indexes and its own archives — and connect only through [pointer entries](#pointer-entries-and-capacity).

## Where a shared scope's layers live

| Layer | Personal scope | Company scope |
|---|---|---|
| **Working** | Local folders | Shared drive (collab docs, videos, campaign assets) **+** git host (repos) |
| **Thinking** | Local vault | The **brain repo** on the git host |
| **Doing** | Your task manager | Open question — see below |
| **Capture** | Your journal | Stays personal; processing routes shared items into the scope |

Three doctrine lines fall out of this table:

1. **Repos never live on the file share.** Already method doctrine ([method.md](method.md#where-finished-things-live)); at company scale it's load-bearing. Code goes to the git host, where it's cloneable, diffable, and directly workable by agents.
2. **The shared drive is the working layer only.** It holds the things that are genuinely collaborative *documents and assets* — Google Docs being co-edited, spreadsheets, presentations, video files, campaign material — organized under the same `1_Projects / 2_Systems / 3_Archives` shape.
3. **The shared thinking layer is a git repo, not the drive.** See [ADR-0004](adr/0004-shared-brain-is-a-git-repo.md). Someone looking for *what a project is* goes to the brain repo; someone looking for *the assets* goes to the drive or the project's repo.

## The shared Second Brain (the brain repo)

The company scope's Second Brain is a repo in the org — `company-brain` or similar — cloned by every member. It has exactly the same shape as a personal vault: the three indexes (`projects.md`, `systems.md`, `archives.md`), a folder per project with its `Brief.md` and `tasks/`, a descriptor file per system.

This satisfies [ADR-0003](adr/0003-files-first-second-brain.md) at company scale: the shared thinking layer is still plain markdown files every member owns a full copy of. Agents read it exactly like a personal vault. Concurrent edits are git merges — a solved problem, and the reason the files-first doctrine exists.

**Projects that are repos:** deep technical docs belong in the project's own repo, next to the code. But the Brief — or at minimum a pointer to it — lives in the brain repo, so the scope's indexes stay complete. The indexes must never have blind spots; they are the answer to "what is this company working on?"

**Cloned systems:** company systems that are repos get cloned to members' machines and worked directly — Claude Code, Claude co-work, any agent that reads files. This is where the shared layer pays off hardest: giving someone access to a system *is* giving them clone access, and their agents inherit the whole structure the moment the clone lands.

## The Convention — and agents that load it

A scope publishes a **Convention**: its expectations for member machines. Where the local layout goes, which brain repos and systems should be cloned and where, the drive/git split, naming rules, how tasks get armed, which tool connections a member is expected to have.

The Convention is not a wiki page. **It ships as agent instructions at the brain repo root** — `CLAUDE.md`, and `AGENTS.md` for other agents (matching the multi-agent roadmap item). That single decision is what gets everyone's AIs onto the same page: every member's agent, regardless of vendor, loads the same ground truth the moment it touches the shared brain. Joining the scope means your agents learn its rules automatically — versioned, reviewable, and mergeable like everything else in the repo.

## Audit

A future verb, `/psa audit`, checks a machine against the Conventions of the scopes it belongs to. It is deliberately distinct from `/psa review`:

| | Asks | Scope of concern |
|---|---|---|
| `/psa review` | Is the **work** healthy? | Capacity, stale briefs, orphans, overdue system reviews |
| `/psa audit` | Is this **machine** set up the way the scope expects? | Layout, clones, placement, connections |

Audit checks, per scope: the local layout matches the Convention; the brain repo is cloned and current; cloned systems are where the Convention says they go; nothing is sitting on the drive that belongs on the git host; the expected tool connections are present. It's the onboarding check when someone joins a scope, and the recurring drift check afterward.

## Local layout

The method is layout-agnostic, and stays that way. `~/Documents/1_Projects` and a user-level `~/Projects` / `~/Systems` / `~/Archives` are both first-class — the config at `~/.psa/config.json` abstracts the path, so the skill never cares. A scope's Convention *may* prescribe a layout for its members (e.g. "cloned company systems live at `~/Systems/`"), and that's precisely the kind of rule audit exists to check. The method itself blesses no single layout.

## Access

There is no PSA auth layer, and none is planned. **Joining a scope = being added to the scope's platforms**: membership in the git org, access to the drive share. Identity and permissions are those platforms' jobs.

Agents need no identity of their own: an agent acts *as its member*, through that member's own authenticated tools — their `gh` login for repos, their own MCP connections for the drive and other tools. PSA's role is only to *record* in config which connections a scope's Convention expects — which makes a missing connection auditable ("your Drive connection isn't set up") — never to mint or manage credentials.

## Pointer entries and capacity

Capacity is a statement about a **person**, not a scope — you are one human across all your scopes, and a cap the shared work can bypass is fiction.

So: when you take on active work in a shared-scope project, your personal `projects.md` gets a **pointer entry** — one line marking the project as living in that scope, linking to its canonical Brief in the brain repo. It counts against your personal capacity. Your personal Second Brain can hold your private notes about the work; the canonical Brief stays shared. When the shared project closes, your pointer archives with it — the shared scope owns the real archive.

This also keeps `/psa` status and `/psa schedule` honest: they see your *real* workload, which is exactly what an agent time-blocking your calendar needs.

## Open questions

Logged deliberately, not accidentally:

- **Headless and team agents.** An agent running as a scheduled job or shared team runner isn't inside any member's session, so it can't borrow a member's credentials. It likely needs its own identity (machine account, fine-grained token). Undesigned.
- **The shared doing layer.** Where does a shared scope's task state live? The candidate answer is task files in the brain repo (the markdown tier, shared), but multi-person task claiming/assignment mechanics are undesigned.
- **Multi-scope status.** What `/psa` shows when you belong to three scopes — one merged view, per-scope views, or both — is a UX question for when audit is built.
