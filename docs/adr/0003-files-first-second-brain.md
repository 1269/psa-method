# The Second Brain must be a local markdown folder

The Second Brain is not just *tracked* by the skill — it is the skill's read/write substrate. Briefs, indexes, task files, and system descriptors are all read and written there by nearly every command. App-database tools (Notion, Craft, etc.) would force every one of those writes through the guided tier — "now paste this into Notion" ten times per `/psa tasks` run — which is a punishment, not a workflow.

So the requirement is doctrine, stated confidently: **your thinking layer lives in plain files you own.** Files are the interface AI agents speak natively, they diff and merge (which the Shared PSA roadmap depends on), and they survive every app you'll ever churn through. We recommend Obsidian as the viewer/editor; any local markdown folder satisfies the requirement.

## Considered options

- **Tier the Second Brain like the Task Manager** (automated for files, guided for app databases) — rejected: roughly doubles the instruction surface of every command for a degraded experience.
