# AGENTS.md — pod-valkey

Standalone candy repo for the `valkey` candy — a Redis-compatible Valkey 9.x
key-value server supervised on `0.0.0.0:6379`. The candy lives in `charly.yml`
at the repo root.

Canonical files:

- `charly.yml` — the `valkey:` candy entity (description, `require`, `distro`,
  `env`, `port`, `volume`, `service`, `plan`) and its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-infrastructure:valkey` — the owning skill: the candy properties, the
  Remi modular repo, and the service-discovery env injection. Load before
  editing, building, deploying, or troubleshooting this candy.
- `/charly-infrastructure:redis` — the Redis alternative on the same port 6379.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `mkdir:` / `check:`, per-distro sections, service
  declarations).
- `/charly-check:check` — the check/R10 framework (`charly check box`,
  `charly check run <bed>`).
- `/charly-pod:pod` — the `kind: pod` / deploy schema reference (this candy is
  composed into a box; ports, volumes, services).
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifest
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The candy's own `check:` steps assert `/usr/bin/valkey-server`,
  `/usr/bin/valkey-cli`, and the `valkey` package, and — at deploy scope — a
  `PING`/`PONG`, a `SET`/`GET` round-trip, and a reachable published port.

## Modify this repo

- Edit the `valkey:` candy entity in `charly.yml`; the `skill:` entity in the
  same file is the owning skill's source — a candy change and its skill change
  land together.
- The Remi modular repo + `valkey:remi-9.0` module are declared under
  `distro.fedora`; keep the repo, module, and package in step.
- The service `exec` binds `0.0.0.0`, disables protected mode, and sets the
  `${HOME}/.valkey` data dir and `--save 60 1` policy; keep them in step with the
  `valkey-data` volume and the runtime checks.
- The `skill:` entity is the source for `/charly-infrastructure:valkey`; never
  edit the generated `SKILL.md` — regenerate it.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
