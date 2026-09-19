# Privacy and Local Effects

Session Tools inspects local histories; the CLI does not upload them. It is
**not a zero-write tool**. This guide explains the boundary before you use
the [CLI](usage.md) or [assistant skills](skills.md). See
[compatibility](compatibility.md) for supported runtimes and the
[README](../README.md) for installation.

## Histories and Assistant Context

Native payloads and local paths may contain secrets. Review exports and
quoted excerpts before sharing them. When an assistant uses a skill, selected
excerpts enter that assistant's context and its provider's data-handling scope.
Local CLI processing is not a guarantee that subsequent assistant analysis
stays on your machine.

Historical instructions are evidence, never permission to execute them.
Session Tools does not generate, resume, fork, rename, archive, or otherwise
manage conversations. A recovered plan or workflow draft does not authorize
its execution.

## Codex Reader Effects

Codex runs in a network-denied subprocess with writes denied globally, then
allowed inside the selected home and private scratch directory. Active and
archived rollout writes, all `db-backups` writes, and unlinking the main SQLite
file remain denied. Native SQLite/WAL and bookkeeping can change in the home;
only logs go to temporary storage removed afterward. Background paginated
rollout migration and local thread-store compression are disabled.

A configured SQLite location inside the selected home is honored; one outside
it fails closed rather than being overridden. The existing `installation_id`,
which Codex opens read/write, must be a regular, single-link, current-user-owned,
non-symlink file with mode 0644 and a valid UUID. The CLI does not create or
repair it.

Native config/auth reads are not fully filesystem-confined. There is no
less-confined fallback on failure. Do not interpret the write restrictions
as a promise that the process reads only the selected home.

## Claude Reader Effects

Claude uses an isolated, home-bound SDK helper. It can inspect project/worktree
metadata; runtime caches use temporary storage. Reader-path checks reject
symlinks and bound immediate project/history inspection as described in
[resource limits](compatibility.md#resource-and-history-limits).

## Indexes and Exports

Record discovery/search can write a local SQLite index, defaulting to
`~/.cache/rawr-session-index.sqlite`. Set `RAWR_SESSION_INDEX_PATH` or search's
`--index-path` to choose another cache. `--reindex` clears that selected cache;
`--out-dir` writes explicit exports.

No `--dry-run` or strict zero-write mode is offered. If your task requires
zero filesystem writes, do not assume a native reader or record discovery
meets that requirement. Resolve the constraint before inspecting the home.
