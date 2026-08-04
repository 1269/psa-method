# The shared Second Brain is a git repo, not the file share

A shared scope needs a thinking layer, and the default instinct is to put it where everything else shared already lives — the drive. We decided the opposite: a shared scope's Second Brain is a **git repo on the scope's git host**, cloned by every member, and the drive holds only working files (collab docs, videos, assets). This is [ADR-0003](0003-files-first-second-brain.md) applied at company scale: briefs, indexes, and task files must be plain markdown that agents can grep, diff, and merge — drive-native documents offer none of that, and concurrent edits to shared thinking are exactly the merge problem git already solves. The repo also gives the Convention a home as agent instructions (`CLAUDE.md`/`AGENTS.md` at the root), so every member's agent loads the same ground truth on clone.

Consequence worth naming: a shared scope's thinking layer and working layer live on **different platforms**, and joining a scope means cloning a repo, not just accepting a share.

## Considered options

- **Google Docs on the drive** — rejected: no frontmatter contract, no grep, no diffs, agents locked out; kills ADR-0003 at the scope where it matters most.
- **Briefs scattered per project repo** — rejected: the scope loses its central indexes; no single place answers "what is this company working on?"
