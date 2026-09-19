# Native Recovery Recipes

Use native host tools when their provider/home scope and depth suffice.
Otherwise use packaged `rawr-session-tools` on Bun >=1.3.14. Initial
native qualification is macOS ARM64. The
[release guide](https://github.com/rawr-ai/session-tools#readme) owns installation,
version selection, and runtime requirements; no source checkout or disposable
SDK reader is needed.

## Select And Read

```bash
rawr-session-tools --version
rawr-session-tools sessions discover --help
rawr-session-tools sessions discover --source all --limit 5 --json
rawr-session-tools sessions read --reference '<returned-referenceToken>' --limit 5 --json
rawr-session-tools sessions read --reference '<same-referenceToken>' --cursor '<exact-nextCursor>' --limit 5 --json
```

Choose one source-qualified candidate. Ask if ambiguous, including duplicate
IDs in different homes. Retain repeatable `--claude-home`/`--codex-home`
choices on every call: explicit selections replace defaults for that provider.
Codex defaults to active roots; native archived discovery requires
`--source codex --archived`. Use actual cwd with `--directory`, not a
repository-name substring. For discovery pages, repeat source/scope and pass
the returned `--source-id` and exact `--cursor`.

Inspect `data.coverage` and source outcomes on discovery; inspect
`data.outcome.status` on read. `ok: true` alone is insufficient.
Noncomplete commands exit 2 with data retained; do not discard useful partial
evidence. A completed page can have a next cursor. Read the native payload at
`data.outcome.value.native` and follow continuations until the needed later
decision and tool result are in view, or explicitly mark that window missing.

## Record Search For A Named Gap

```bash
rawr-session-tools sessions search --source codex --query '<regex>' --max-matches 5 --json
rawr-session-tools sessions read --record '/exact/search-hit.jsonl' --limit 5 --json
rawr-session-tools sessions extract '/exact/search-hit.jsonl' --format markdown --no-dedupe --roles all --include-tools --max-messages 100
```

Carry the same home selection; add `--source-id` to disambiguate record handoff.
Native handoff rejects unregistered/imported or wrong-path files rather than
registering them. Exact-file extraction remains record evidence even outside
homes. Search matches can belong to a branch omitted by native conversation
reading; cite that distinction, not a merged history.

The record view is normalized file order, not native replay. Continue record
windows with `--offset 100 --max-messages 100`, preserving role/dedupe settings.
A prose claim that tests passed is not a captured passing result.

## Bounds, Privacy, And Failure

Native page limits cap returned messages/turns, not lazy work: each page can
reconstruct the full source under separate deadline/byte/inventory limits.
No intended conversation mutation does not mean zero writes. Native startup,
record caches, and exports have distinct effects. Strict no-write requests need
an adequate qualified surface; missing runtimes are diagnostics, not permission
to install, initialize, or migrate provider state.

Export only when needed and authorized to a fresh private directory. Native
`--out-dir` writes `native-discovery.json` or `native-conversation.json`.
Files are not automatically redacted. Prefer redacted notes for sensitive
evidence and follow [Scratchpad Policy](scratchpad-policy.md).

For complete flags, metrics, runtime limits, and record export variants, use
[Session Operations](../../sessions/references/session-ops.md) and
[Session Structures](../../sessions/references/session-structures.md).
