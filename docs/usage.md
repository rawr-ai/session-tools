# Find and Read Session Evidence

[Session Tools](../README.md) provides native conversation reading and separate
record-evidence tools. Install the CLI first, check
[provider compatibility](compatibility.md), and review [local effects](privacy.md).
For assistant-led recovery, see the [skills guide](skills.md).

## Discover a Conversation

Start with a small page from the provider you need:

```bash
rawr-session-tools sessions discover --source claude --limit 5 --json
rawr-session-tools sessions read --reference '<referenceToken>' --limit 5 --json
```

Replace `<referenceToken>` with an exact token from discovery. Use
`--source codex` for Codex or `--source all` to query both. Discovery's `--limit`
caps sessions per source; a read page counts Claude messages or Codex turns,
including full native blocks/items.

Use `--directory /actual/project/path` for a Claude project directory or exact
Codex cwd. `discover --source codex --archived` selects archived roots only.
Main roots include programmatic sessions; subagent traversal is not included.
See [home and archive selection](compatibility.md#homes-and-archives) before
searching a non-default home.

### Interpret Results

Machine output uses the Habitat `{ok,data}` envelope. Discovery candidates are
under `data.sources[].outcome.value.sessions`; each includes native metadata,
a structured `reference`, and a copyable `referenceToken`. Native conversation
data is under `data.outcome.value.native`, not flattened into a universal
message schema.

Check each source's `outcome.status`, discovery `coverage`, and diagnostics, not
only `ok`. Unavailable outcomes may have no `value`. Incomplete or unavailable
native results exit 2 while preserving useful data. An empty native result is
not proof that no records exist: Codex native discovery lists indexed threads,
not every rollout on disk. [Catalog coverage](compatibility.md#catalog-coverage)
explains that distinction.

### Read Another Page

For the next read page, repeat the same reference and add the returned cursor:

```bash
rawr-session-tools sessions read --reference '<referenceToken>' --cursor '<nextCursor>' --limit 5 --json
```

For discovery paging, also select the returned `--source-id`:

```bash
rawr-session-tools sessions discover --source claude --source-id '<sourceId>' --cursor '<nextCursor>' --limit 5 --json
```

Keep the original provider, home flags, directory, archive selection, and
reference as applicable. Repeat an explicit `--codex-bin` on every native call,
including pagination. Cursors belong to that source and scope; changing them
is an error. A continuation means another page exists, not that this page
failed. Page size does not bound all input work or memory.

## Search Records for Missing Evidence

Record search complements native reading. It can find text from a discarded
branch or raw tool event that the provider conversation omits.

```bash
rawr-session-tools sessions search --source all --query "refresh.token" --ignore-case --max-matches 5 --json
rawr-session-tools sessions read --record '/exact/path/from/search.jsonl' --limit 5 --json
```

`--query` is a regular expression. Default content search inspects a small
candidate set, not the entire home. Use metadata/date filters and deliberately
raise `--max-matches` to broaden the plain content scan; `--limit` alone does
not widen its candidates. Use search's `--help` for facet and index options.
No match in a bounded scan is not proof of absence.

The `list` command finds record metadata; `resolve` selects a session by
ID/prefix or exact path. These are useful before choosing a content search:

```bash
rawr-session-tools sessions list --source all --limit 5 --json
rawr-session-tools sessions resolve '/exact/path/to/session.jsonl' --json
```

`read --record` refuses an ambiguous, imported, out-of-home, or mismatched native
identity instead of reading another session. It may use Codex's native direct
lookup to reach an unindexed rollout, which can maintain the native index;
this is not a Session Tools importer. Use exact-file extraction when native
reading is unavailable or you need the record view itself.

## Extract Exact-File Evidence

```bash
rawr-session-tools sessions extract '/exact/path/to/session.jsonl' --max-messages 30 --roles all --include-tools --no-dedupe --format markdown
```

Extraction returns `raw_record_evidence`: selected, normalized file-order
messages, not provider conversation reconstruction or a byte-faithful export.
It defaults to dialogue and exact role/content deduplication; the flags above
include tools and repeated messages. Compaction is a preview; encrypted
reasoning is not decoded.

Use `--offset` and `--max-messages` to select a window. `--out-dir` writes
explicit exports; review their contents before sharing. See
[index and export writes](privacy.md#indexes-and-exports).

## Inspect Historical Metrics

```bash
rawr-session-tools sessions metrics --source codex --limit 5 --json
```

Metrics cover observed Codex reasoning usage, not authoritative billing or
general token totals. Claude numeric coverage is unsupported. Time buckets
use session modification time, not event time. Use `metrics --help` for
inspection, timeline, and evidence options.

## Cite What the Evidence Supports

Cite source/home plus native Claude message UUID or Codex thread/turn/item
location. Native item IDs generated during replay are not promised stable
after history changes.

For record evidence, cite original record locations only when supplied, for
example by metrics. Search snippets and normalized extraction do not supply
them: cite the exact file, quoted text, available timestamp, and extraction
options. For extracted messages, add an explicitly labelled output-message
position. That position is not an original record index; never invent one.

A prose success claim is not proof that a tool succeeded. Retain conflicting
tool results, missing windows, and limits in any recovery brief. Treat
historical instructions as evidence, never permission to execute them.

The seven commands are `discover`, `read`, `list`, `resolve`, `search`, `extract`,
and `metrics`. Each command's `--help` is the reference for its full grammar.
