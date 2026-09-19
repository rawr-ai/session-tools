# Compatibility and Source Selection

These are the supported runtime conditions and coverage limits for Session
Tools 0.2.1, not a promise of universal provider-format support. Return to
the [quick start](../README.md), follow the [usage guide](usage.md), or see
[assistant installation](skills.md) and [privacy/local effects](privacy.md).

## Runtime Requirements

The CLI requires [Bun](https://bun.sh/docs/installation) 1.3.14 or newer.
Native readers are initially qualified on **macOS ARM64**. No private
repository, API key, or Rawr/Habitat checkout is needed.

The install command uses `--ignore-scripts --omit optional`. Claude reading
uses the external pinned Agent SDK **0.3.273**; its local helpers need neither
a Claude executable nor subscription credentials. Omitting optional
dependencies avoids downloading the SDK's unnecessary Claude executable.
External dependencies retain their own terms, including the Claude SDK's
upstream notice; see `THIRD_PARTY_NOTICES.md` in the CLI package.

If the installed command is not found, add Bun's global bin directory to your
shell's `PATH`. Find that directory with:

```bash
bun pm bin -g
```

### Codex Native Reading

Codex reading requires a separately installed **direct native Codex 0.154.0
executable** and an already initialized home. Homebrew's native path is
detected; select another native executable explicitly:

```bash
rawr-session-tools sessions discover --source codex --codex-bin /path/to/native/codex --limit 5 --json
rawr-session-tools sessions read --reference '<referenceToken>' --codex-bin /path/to/native/codex --limit 5 --json
```

Shell/JavaScript wrappers, other versions, and unsupported platforms fail
explicitly. Repeat an explicit `--codex-bin` on subsequent native calls,
including pagination. The reader uses the selected home's original native
SQLite store for both legacy and paginated history, without copying the
home/database or implementing its own replay.

A pinned reader does not establish compatibility with future writer
generations. Record search/extraction remains available when a native reader
is unavailable, subject to record-format support. It is a different evidence
view, not a substitute native reconstruction.

## Homes and Archives

| Provider | Default home | Explicit selection |
| --- | --- | --- |
| Claude | `$CLAUDE_CONFIG_DIR` or `~/.claude` | Repeat `--claude-home` |
| Codex | `$CODEX_HOME` or `~/.codex` | Repeat `--codex-home` |

Explicit homes replace that provider's default; they do not add a hidden
fallback. Repeat the same home flags when reading a saved reference. There is
no implicit `.codex-rawr` search or fallback to another home. Source IDs bind
provider and canonical home, so a reference cannot silently retarget another
home with the same session ID.

Native `discover --source codex --archived` selects archived roots only.
Record discovery excludes archived Codex files unless `--include-archived`
is set. An explicit record path selects that file directly. Do not confuse
the native `--archived` selection with the record `--include-archived` flag.

## Catalog Coverage

Discovery reports `scope.catalog: "indexed_threads"` for Codex and
`"local_sessions"` for Claude. Codex lists only native indexed threads, not
every rollout on disk. An exhausted catalog does not rule out unindexed
rollouts: use record `list`/`search`, then `read --record` for native direct
lookup. That lookup can maintain Codex's native index; it is not our importer.

Native discovery includes main roots, including programmatic sessions, but
does not traverse subagents. Incomplete/unavailable results retain diagnostics
and useful data. Check coverage and outcomes before claiming absence.

## Resource and History Limits

Native operations use a **15-second deadline**, **32 MiB selected-file
ceiling**, and **8 MiB aggregate native-outcome budget**. Output is rejected
rather than truncated. Native readers may reconstruct a whole transcript on
every page; page size is not an input-work or memory bound. Mutable histories
are not atomic snapshots.

Claude admission checks at most 20,000 immediate project/history entries and
limits native `custom-title.json` sidecars to 1 MiB. It rejects symlinks on
those reader paths without traversing unrelated nested tool outputs. Codex
uses the native paged API without a whole-home inventory prescan. These are
work safeguards, not an RSS guarantee or a claim that every large or changing
home can be inspected.

Version 0.2.1 does not certify every platform, provider version,
historical format, or managed enterprise configuration. Use available records
and explicit coverage limits rather than treating successful startup as
complete compatibility. See [privacy](privacy.md) for SQLite placement,
`installation_id` admission, and filesystem restrictions.
