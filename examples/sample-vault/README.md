# Sample vault

The actual files PSA produces — so you can see the output before pointing anything at your own folders. This is the Second Brain of the [walkthrough](../walkthrough.md)'s user, Sam, frozen right after: setup → first project (`/psa new website-refresh`) → tasks mirrored (`/psa tasks`) → one task armed (`/psa add-context`).

```
Second Brain/
├── 1 Projects/
│   ├── projects.md                          ← index, one active project
│   └── Website-Refresh/
│       ├── Brief.md                         ← filled GPS Brief with join key
│       └── tasks/
│           ├── 001-audit-current-site.md    ← skeleton (backlog)
│           └── 003-migrate-site-to-new-host.md  ← armed (ready)
├── 2 Systems/
│   └── systems.md                           ← index, empty so far
└── 3 Archives/
    └── archives.md                          ← index, empty so far
```

The matching File Share is just three folders (git can't hold empty directories, so it isn't checked in):

```
/Volumes/Work/
├── 1_Projects/website-refresh/
├── 2_Systems/
└── 3_Archives/completed-projects/
```

Compare `001` (skeleton — what `/psa tasks` writes in batch) against `003` (armed — what `/psa add-context` produces for one task deliberately). The difference between them is the whole point of the two-stage pipeline.
