# pod-valkey

The `valkey` candy of the OpenCharly candy library, as a standalone repo (the
candy de-submodule cutover, kind-prefixed naming). It provides a Redis-compatible
Valkey 9.x key-value server supervised on `0.0.0.0:6379`.

## What it provides

Installs Valkey 9.x from the Remi modular repo (the `valkey` package, module
`valkey:remi-9.0`), which ships `/usr/bin/valkey-server` and
`/usr/bin/valkey-cli`. A supervised `valkey-server` binds `0.0.0.0:6379` with
protected mode off, a `${HOME}/.valkey` data dir, and a 60s/1-key RDB snapshot
policy.

| Property | Value |
|---|---|
| Port | `6379` |
| Volume | `valkey-data` → `~/.valkey` |
| Service | `valkey` (`valkey-server … --save 60 1`, `restart: always`, priority 20) |
| Env | `VALKEY_URL=redis://127.0.0.1:6379` |
| Package | `valkey` (RPM, Remi modular repo) |

Because it speaks the Redis protocol, `REDIS_URL` is injected for cross-container
service discovery (`redis://charly-valkey:6379`), and same-container consumers
receive `redis://localhost:6379`.

Every claim is verifiable: the binaries on disk, the package installed, and a
live PING/PONG + SET/GET round-trip + reachable-port probe against the running
deployment.

## How to use it

```yaml
my-app:
  candy:
    - '@github.com/opencharly/pod-valkey:<tag>'
  ports:
    - "6379:6379"
```

## Verification

The candy's `check:` plan asserts `/usr/bin/valkey-server`, `/usr/bin/valkey-cli`,
and the `valkey` package, and — at deploy scope — a `valkey-cli ping` returning
`PONG`, a `SET`/`GET` round-trip, and a reachable `127.0.0.1:${HOST_PORT:6379}`.

## Layout

- `charly.yml` — the `valkey:` candy entity (description, `require`, `distro`,
  `env`, `port`, `volume`, `service`, `plan`) plus its `skill:` entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `CHANGELOG/` — per-CalVer history.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-infrastructure:valkey` — the candy properties, the
  Remi modular repo, and the service-discovery env injection.
- `/charly-infrastructure:redis` — the Redis alternative on the same port 6379.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
