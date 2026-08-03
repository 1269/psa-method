---
title: "Migrate site to new host with zero downtime"
task_id: "things:///show?id=G7H8I9"
status: "ready"
priority: "high"
dependencies: ["001"]
created: "2026-08-03"
updated: "2026-08-03"
due_date: "2026-08-22"
tags: ["infrastructure"]
---

# Migrate site to new host with zero downtime

## Context
- **Project:** Website Refresh
- **Brief:** [Brief.md](../Brief.md)
- **Repo:** /Volumes/Work/1_Projects/website-refresh
- **Due:** 2026-08-22

## Why this matters

The stale site is costing freelance inquiries, and the old host blocks the static-site toolchain the future personal-brand system needs. This migration is the milestone the other work converges on.

## Acceptance Criteria

- [ ] Site serves from the new host at samreads.dev with valid TLS
- [ ] Every keep-listed URL from the 001 audit returns 200 (no broken inbound links)
- [ ] DNS cutover completed with old host still serving until propagation (zero downtime)
- [ ] Deploy process written down in the repo's README

## Test Protocol

1. `curl -sI https://samreads.dev` → verify: `HTTP/2 200` and the new host's server header
2. For each URL in `audit/keep-list.txt`: `curl -s -o /dev/null -w "%{http_code}" <url>` → verify: `200`
3. `dig samreads.dev +short` → verify: new host's IP
4. Repo README contains a "Deploy" section → verify: a fresh clone can follow it to a successful deploy

## Subtasks

- [ ] Provision the new host and TLS
- [ ] Deploy the current site build to the new host
- [ ] Verify keep-list URLs on the new host before touching DNS
- [ ] Cut DNS over; monitor propagation

## References

- `audit/keep-list.txt` — URLs that must survive (produced by task 001)
- `deploy/` — new host's config, created during this task

## Out of Scope

- Homepage copy changes (task 002 — don't mix content edits into the migration diff)
- Analytics re-setup — follow-up task if the new host breaks the current snippet

## Working Constraints (for the agent picking this up)

- File paths in this brief were verified as of 2026-08-03. If a path doesn't resolve, stop and report — don't assume.
- If a fact is not in this brief or the linked Brief.md, mark it [NEEDS VERIFICATION] rather than asserting it.
- If you discover related work, capture it as a follow-up task; do not expand scope.
- Log decisions in the Work Log as you make them.

## Original notes

> Old host contract renews Sept 1 — finish before then or we pay another year.

## Work Log

<!-- the agent documents actions and decisions here -->
