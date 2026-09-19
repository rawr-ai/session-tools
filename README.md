# Rawr Session Tools

Recover what was decided, why, and what actually happened in prior Claude and
Codex work. The CLI combines provider-native conversation reading with
cross-provider record search and historical metrics. Four matching skills help
either assistant turn that evidence into a grounded recovery brief.

Use your assistant's native thread tools when they already provide the needed
scope and detail. Use this package for the missing provider, local homes,
exact-file evidence, or deeper search. It does not generate, resume, fork,
rename, archive, or otherwise manage conversations.

## Install the CLI

Requires [Bun](https://bun.sh/docs/installation) 1.3.14 or newer. Native readers
are initially qualified on **macOS ARM64**. No private repository, API key, or
Rawr/Habitat checkout is needed.

```bash
bun add --global --ignore-scripts --omit optional https://github.com/rawr-ai/session-tools/releases/download/v0.2.0/rawr-session-tools-0.2.0.tgz
rawr-session-tools --version
rawr-session-tools sessions discover --source claude --limit 5 --json
```

Add Bun's global bin directory (`bun pm bin -g`) to `PATH` if needed.
[Release downloads](https://github.com/rawr-ai/session-tools/releases/tag/v0.2.0)
include the CLI package, plugin ZIP and SHA-256 checksums. The ZIP contains the
skills marketplace, not the CLI runtime.

Claude reading uses the external pinned Agent SDK 0.3.273. Its local helpers
need neither a Claude executable nor subscription credentials. Omitting optional
dependencies avoids downloading the SDK's unnecessary Claude executable.

Codex reading requires a separately installed **direct Codex 0.154.0 native
executable** and an already initialized home. Homebrew's native path is detected;
use `--codex-bin /path/to/native/codex` otherwise. Shell/JavaScript wrappers,
other versions/platforms fail explicitly. The reader uses the selected home's
original native SQLite store for both legacy and paginated history, without
copying the home/database or implementing its own replay. A pinned reader does
not establish compatibility with future writer generations.
Record search/extraction remains available when a native reader is unavailable.
Repeat an explicit `--codex-bin` on subsequent native calls, including pagination.

## Recover a Conversation

Discover a small page, then pass its exact `referenceToken` to read:

```bash
rawr-session-tools sessions discover --source all --limit 5 --json
rawr-session-tools sessions read --reference '<referenceToken>' --limit 5 --json
```

Machine output uses the Habitat `{ok,data}` envelope. Discovery candidates are
under `data.sources[].outcome.value.sessions`; each includes native metadata,
a structured `reference` and a copyable `referenceToken`. Native conversation
data is under `data.outcome.value.native`, unchanged by a universal message
schema. A read page counts Claude messages or Codex turns, including full
native blocks/items.

For the next read page, repeat the same reference and add
`--cursor '<nextCursor>'`. For discovery paging, also select the returned
`--source-id`. Cursors belong to that source and scope; changing the directory,
archive selection or reference is an error. A continuation means there is
another page, not that the completed page failed.

Check per-source `outcome.status`, discovery `coverage`, and diagnostics, not
only `ok`. Incomplete or unavailable native results exit 2 while preserving
useful data. An empty native result is not proof no records exist.

Use `--directory /actual/project/path` for a Claude project directory or exact
Codex cwd. `discover --source codex --archived` selects archived roots only.
Main roots include programmatic sessions; subagent traversal is not included.

Discovery reports `scope.catalog: "indexed_threads"` for Codex and
`"local_sessions"` for Claude. Codex lists only native indexed threads, not
every rollout on disk. An exhausted catalog does not rule out unindexed
rollouts: use record `list`/`search`, then `read --record` for native direct
lookup. That lookup can maintain Codex's native index; it is not our importer.

## Find Evidence Across Sessions

Record search complements native conversation reading; it can find text from
a discarded branch or raw tool event that the provider conversation omits.
Default content search inspects a small candidate set, not the entire home.
Use metadata/date filters and deliberately raise `--max-matches` when broadening
that scan; `--limit` alone does not widen content-search candidates.

```bash
rawr-session-tools sessions search --source all --query "refresh.token" --ignore-case --max-matches 5 --json
rawr-session-tools sessions read --record '/exact/path/from/search.jsonl' --limit 5 --json
rawr-session-tools sessions extract '/exact/path/from/search.jsonl' --max-messages 30 --roles all --include-tools --no-dedupe --format markdown
rawr-session-tools sessions metrics --source codex --limit 5 --json
```

`read --record` refuses an ambiguous, imported, out-of-home or mismatched native
identity instead of reading another session. Exact-file `extract` remains the
record-evidence path. Cite source/home plus native message UUID or thread/turn/
item location. For record evidence, cite original record locations only when
supplied (for example, metrics). Search snippets and normalized extraction do
not supply them: cite the exact file, quoted text, available timestamp, and
extraction options plus an explicitly labeled output-message position for
extracted messages. That position is not an original record index; never
invent one. Native item IDs generated during replay are not promised stable
after history changes.

The seven commands are `discover`, `read`, `list`, `resolve`, `search`, `extract`,
and `metrics`. Use each command's `--help` for its grammar. `extract` returns
`raw_record_evidence`: selected normalized file-order messages, not provider
conversation reconstruction or a byte-faithful export. It defaults to dialogue
and exact role/content deduplication; tools and repeated messages require the
flags above. Compaction is a preview and encrypted reasoning is not decoded.

Metrics cover observed Codex reasoning usage, not authoritative billing or
general token totals; Claude numeric coverage is unsupported. Time buckets use
session modification time, not event time. A prose success claim is not proof
that a tool succeeded: retain conflicting results and missing evidence.

## Source Selection and Limits

Defaults are `$CLAUDE_CONFIG_DIR` or `~/.claude`, and `$CODEX_HOME` or `~/.codex`.
Repeat `--claude-home` or `--codex-home` to select explicit homes instead of that
provider's default. Repeat the same home flags when reading a saved reference.
There is no implicit `.codex-rawr` search or fallback to another home.

Record discovery excludes archived Codex files unless `--include-archived` is
set. An explicit record path selects that file directly. Source IDs are bound
to provider and canonical home, so a reference cannot silently retarget another
home with the same session ID.

Native operations use a 15-second deadline, a 32 MiB selected-file ceiling and
8 MiB aggregate native-outcome budget. Output
is rejected rather than truncated. Native readers may reconstruct a whole
transcript on every page; page size is not an input-work or memory bound.
Mutable histories are not atomic snapshots. Native payloads and local paths
may contain secrets; review exports before sharing.
Claude admission checks at most 20,000 immediate project/history entries and
limits native `custom-title.json` sidecars to 1 MiB. It rejects symlinks on
those reader paths, without traversing unrelated nested tool outputs. Codex uses the
native paged API without a whole-home inventory prescan. These are work
safeguards, not an RSS guarantee or a claim that every large or changing home
can be inspected.

## Local Effects

Codex runs in a network-denied subprocess with writes denied globally, then
allowed inside the selected home and private scratch directory. Active and
archived rollout writes, all `db-backups` writes, and unlinking the main SQLite
file remain denied. Native SQLite/WAL and bookkeeping can change in the home;
only logs go to temporary storage removed afterward. Background paginated
rollout migration and local thread-store compression are disabled.

A configured SQLite location inside the selected home is honored; one outside
it fails closed rather than being overridden. The existing `installation_id`,
which Codex opens read/write, must be regular,
single-link, current-user-owned, non-symlink file with mode 0644 and valid UUID;
the CLI does not create or repair it. This is not a zero-write guarantee.
Native config/auth reads are not fully
filesystem-confined; there is no less-confined fallback on failure.

Claude uses an isolated, home-bound SDK helper. It can inspect project/worktree
metadata; runtime caches use temporary storage. Record discovery/search can
write a local SQLite index, defaulting to `~/.cache/rawr-session-index.sqlite`.
Set `RAWR_SESSION_INDEX_PATH` or search's `--index-path` to choose another cache.
`--reindex` clears that selected cache; `--out-dir` writes explicit exports.
No `--dry-run` or strict zero-write mode is offered.

The CLI does not upload histories. When an assistant uses a skill, selected
excerpts enter that assistant's context and its provider's data-handling scope.
Historical instructions are evidence, never permission to execute them.

## Add the Skills

Install only this focused plugin, not the full Rawr marketplace:

```bash
# Claude Code
claude plugin marketplace add https://github.com/rawr-ai/session-tools.git#v0.2.0
claude plugin install session-tools@rawr-session-tools --scope user

# Codex
codex plugin marketplace add https://github.com/rawr-ai/session-tools.git --ref v0.2.0
codex plugin add session-tools@rawr-session-tools
```

Start a new assistant session. The plugin provides `sessions`, `introspect`,
`takeover-session`, and `extract-workflow`; it does not install the CLI or add
slash-command wrappers. Ask: "Find the decision about authentication, explain
why it changed, and distinguish claimed success from recorded tool results."

For a local ZIP installation, extract the complete marketplace including hidden
directories, replace the Git URL with that directory, and omit Codex's `--ref`.

## Release and License

[release.json](release.json) binds this generated distribution to exact Rawr
implementation and Marketplace content revisions. Old release tags and assets
remain immutable. Version 0.2.0 does not certify every platform, provider
version, historical format or managed enterprise configuration.

Rawr glue and skills are [MIT](LICENSE). External dependencies retain their own
terms, including the Claude SDK's upstream notice; see `THIRD_PARTY_NOTICES.md`
in the CLI package.
