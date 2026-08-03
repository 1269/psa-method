# Config lives at `~/.psa/config.json`

Every command needs the config before it knows where anything else lives, so the config can't live inside the File Share (chicken-and-egg: you'd need config to find it) and shouldn't live inside the installed skill folder (overwritten on skill update). A fixed dotfile path is the simplest thing that always works, and JSON parses reliably everywhere.

## Considered options

- **File-share-root config + `~/.psa/root` pointer file** — makes the drive self-describing (move the drive, config travels), but adds a second file and an indirection step for a benefit most users never exercise. Revisit if Shared PSA makes portable drives common.
- **YAML** — friendlier to hand-edit, but `/psa setup` is the intended editing surface (re-run it to change answers), so hand-editability matters less than parse reliability.
