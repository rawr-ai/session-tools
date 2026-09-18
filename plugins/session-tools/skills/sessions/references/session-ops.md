# Session Operations

Recipes target `rawr-session-tools` 0.1.0 on Bun >=1.3.14. Follow the
[release README](https://github.com/rawr-ai/session-tools#readme) for installation,
then check `--version` and each subcommand's `--help`. Run from the caller's
working directory; no private source checkout is required.

## Discover An Exact Target

```bash
rawr-session-tools sessions list --source all --limit 5 --table
rawr-session-tools sessions search --query-metadata "<hint>" --source codex --limit 5
rawr-session-tools sessions resolve "<id-or-path>" --source codex --format text
```

Use `--cwd-contains`, `--branch`, `--model`, `--since`, and `--until` to narrow
listing/search. Time filters use file modification time, not necessarily the
conversation's first or last message time. `--project` is useful for Claude.
Keep the resolved source and path with every finding. If a prefix is ambiguous,
ask for a candidate selection and use its full ID or exact path.

## Search Content Only When Needed

```bash
rawr-session-tools sessions search --source codex --cwd-contains "<repo>" --query "<regex>" --ignore-case --max-matches 5 --snippet 200
rawr-session-tools sessions search --source codex --has-tool apply_patch --candidate-limit 25 --limit 5
```

- `--query-metadata` is a substring query; `--query` is a regular expression.
  They are mutually exclusive. An invalid regex is not an empty result.
  Metadata search still reads provider records to derive metadata; it is not a
  guarantee of no transcript-file access.
- `--has-tag`, `--has-directive`, `--has-tool`, `--has-payload-type`, and
  `--has-top-type` support structured discovery. Facets can stand alone or
  accompany either query mode; finding a tool name does not prove success.
- `--limit` caps metadata/facet results; `--max-matches` caps content results;
  `--candidate-limit` bounds facet candidates. A result cap is not a complete
  corpus search. Non-facet content search selects at most the larger of
  `--max-matches` and `--reindex-limit`, even when not rebuilding the index.
  Narrow filters first; increase these limits deliberately when widening.
- Search defaults to user/assistant content. Use `--roles all --include-tools`
  when tool evidence is required; `--include-tools` alone does not expand the
  default role filter.

## Extract Bounded Evidence

```bash
rawr-session-tools sessions extract "<exact-path>" --format markdown --no-dedupe --max-messages 100
rawr-session-tools sessions extract "<exact-path>" --format markdown --no-dedupe --offset 100 --max-messages 100
```

`--offset` and `--max-messages` select messages, not bytes or elapsed time.
Keep roles and dedupe settings consistent between pages. The default
`--max-messages 0` is unlimited; do not use it for first inspection. Include
`--roles all --include-tools` only when required, and preserve the extraction
options in your evidence notes. Report unknown early context instead of reconstructing
missing text as fact.

```bash
rawr-session-tools sessions extract "<exact-path>" --format markdown --no-dedupe --roles all --include-tools --max-messages 100
```

For two-session comparisons, extract each with comparable settings and compare
decisions, artifacts, or metrics. Similar titles do not establish shared lineage.

## Inspect Metrics With Coverage

```bash
rawr-session-tools sessions metrics --source all --limit 10 --group-by source
rawr-session-tools sessions metrics --timeline --source codex --limit 10 --bucket day
rawr-session-tools sessions metrics --inspect "<exact-path>" --max-events 100 --max-evidence 5
rawr-session-tools sessions metrics --timeline --session "<exact-path>" --bucket record --max-buckets 100
rawr-session-tools sessions metrics --source codex --limit 10 --orchestration
```

Summary is the default. `--timeline` and `--inspect` select distinct modes.
Single-session timeline/inspect reject metadata filters and `--limit`; use
`--max-buckets` or `--max-events` for those output bounds. `record` buckets need
`--session`; `--group-by` belongs to summary mode.

Use repeated `--breakpoint` or `--threshold` for reasoning-token criteria, or
`--no-breakpoints` / `--no-thresholds` to disable those families. Retain reported
coverage and diagnostic information. Unavailable token records are not zero
usage, token records are not a billing receipt, and `no_orchestration_observed`
does not prove standalone execution. These metrics do not establish lineage.

## Choose Output And Control Writes

Use base `--json` for machine-readable command results. `extract --format json`
formats a transcript; `resolve --format json` formats resolution details.
Neither is interchangeable with the command-result envelope. Metrics supports
only `--format text|markdown`; use `--json`, not `--format json`, for automation.

Source transcripts are read, not edited, but cache/export state can change:

| Operation | Write behavior |
| --- | --- |
| Codex discovery, including listing/search | May maintain a local discovery index |
| Content search with `--use-index` | May persist transcript text on cache miss |
| Search with `--reindex` | Clears the index before rebuilding the bounded selection |
| Any `--out-dir` | Creates/writes result files in the selected directory |

The default index is `~/.cache/rawr-session-index.sqlite`, overridable with
`RAWR_SESSION_INDEX_PATH`; search also accepts `--index-path`. Prefer ordinary
bounded search. For explicitly requested cache rebuilding, explain that the
existing index is replaced and select a dedicated cache path before proceeding.
Do not use `--reindex` as an automatic troubleshooting step.

Export only when requested or needed for authorized analysis, to a fresh private
scratch directory. Exported transcripts are not automatically redacted. Review
and redact before sharing, and never include real session files in releases.
List/search exports use `search-results.json`; resolve uses `metadata.json`;
extract uses `metadata.json` plus transcript file(s); metrics uses
`metrics-results.json` containing the service DTO without the command envelope.

## Recover Without Guessing

- **Missing binary or incompatible flags:** consult the release README and
  installed help; do not fall back to a private application or invent an alias.
- **No session found:** check provider and exact path, then relax one metadata
  filter at a time. Discovery roots are described in [Session Structures](session-structures.md).
- **Missing early content:** widen extraction deliberately and label compaction,
  filtering, or missing records. A summary is not the original transcript.
- **Slow search:** narrow provider/repository/time and candidate bounds first.
  Cache creation is a separate write decision, not part of read-only inspection.
- **Strict zero-write request:** the CLI has no documented no-cache mode. Stop
  and explain the limitation; inspect a user-selected file directly only when
  that read is authorized and sufficient.
