# Rawr Session Tools

Use native Claude and Codex tools for conversation recovery, with matching
skills for either assistant. An optional CLI adds cross-provider record search,
tool evidence, indexed filters, and historical metrics.

## Choose the Reader

For ordinary discovery, reading, or context recovery, the skills prefer native
thread tools or vendor session APIs. They do not require this CLI. Claude's
SDK reconstructs conversation chains and compaction; Codex's native history
tools expose turns and items. We do not maintain a competing canonical reader.

Use the CLI when you need evidence across providers, multiple local homes,
exact files, regex/tool-content searches, facets, or historical
reasoning observations. Its extraction view is `raw_record_evidence`: a
filtered, normalized projection in file order, not canonical conversation
history or a byte-faithful raw export.

## Install the CLI

Requires [Bun](https://bun.sh/docs/installation) 1.3.14 or newer. No private
repository access, API key, or Rawr/Habitat checkout is needed.

```bash
bun add --global https://github.com/rawr-ai/session-tools/releases/download/v0.1.1/rawr-session-tools-0.1.1.tgz
rawr-session-tools --version
rawr-session-tools sessions list --source all --limit 5 --table
```

If the command is not found, add Bun's global bin directory (`bun pm bin -g`)
to your shell's `PATH`.

[Download this release](https://github.com/rawr-ai/session-tools/releases/tag/v0.1.1)
for the CLI package, plugin ZIP, and SHA-256 checksums. The ZIP is the skills
marketplace, not a substitute for installing the CLI.

## Add the Skills to Your Assistant

Choose either or both providers. These commands install only this focused
plugin, not the full Rawr marketplace. The CLI is needed only for the record
evidence workflows above.

### Claude Code

```bash
claude plugin marketplace add https://github.com/rawr-ai/session-tools.git#v0.1.1
claude plugin install session-tools@rawr-session-tools --scope user
```

### Codex

```bash
codex plugin marketplace add https://github.com/rawr-ai/session-tools.git --ref v0.1.1
codex plugin add session-tools@rawr-session-tools
```

Start a new agent session after installation. Ask, for example:

> Find the Claude or Codex conversation where we discussed the authentication
> refactor, and summarize the decisions with references to the source session.

The plugin provides `sessions`, `introspect`, `takeover-session`, and
`extract-workflow` skills. They guide the assistant; they do not install the
optional CLI automatically. No additional slash-command wrappers are included.

For a local ZIP installation, unpack the complete marketplace, including its
hidden directories. Substitute the extracted directory for the Git URL in
the marketplace-add command, omit Codex's `--ref`, then install the plugin as
above. Use current provider versions with native plugin support.

## Search Without an Assistant

For a record-evidence investigation, start with metadata, then search selected
record text or extract an exact file:

```bash
rawr-session-tools sessions search --source all --query-metadata "authentication" --limit 5 --json
rawr-session-tools sessions search --source all --query "refresh.token" --ignore-case --max-matches 5 --json
rawr-session-tools sessions resolve <session-id-or-path> --json
rawr-session-tools sessions extract <session-id-or-path> --format markdown --max-messages 30
```

Use `rawr-session-tools sessions <command> --help` for filters, extraction
bounds, output formats, and indexing. The five commands are `list`, `resolve`,
`search`, `extract`, and `metrics`. Metrics describe the events present in
local files; they are not authoritative usage or billing records. Numeric
metrics cover observed Codex reasoning tokens, not general token totals;
Claude numeric coverage is unsupported. Time buckets use session modification
time, not event time.

Extraction defaults to dialogue and exact role/content deduplication. Use
`--no-dedupe` to retain repeated selected messages and `--roles all --include-tools`
for tool records. Compaction summaries are previews and encrypted reasoning is not
decoded. Unsupported record fields are omitted. Single-stream chunked JSON
is an array of transcript objects; split JSON files are standalone objects.

## What It Reads and Writes

The CLI reads Claude sessions under `~/.claude/projects` and Codex sessions
under `$CODEX_HOME`, `~/.codex`, and `~/.codex-rawr`, including archived Codex
sessions. `CODEX_HOME` adds a discovery root; it does not exclude the defaults.
Custom `CLAUDE_CONFIG_DIR` session discovery is not supported in this release.

Source transcripts are not modified. Discovery can create local file-list
caches; indexed searches can write SQLite caches. `--reindex` deliberately
rebuilds the selected search index, and `--out-dir` deliberately writes
exports. Set `RAWR_SESSION_INDEX_PATH` to choose the SQLite index location,
or use search's `--index-path`. Invalid date windows and ambiguous identities
fail rather than silently widening the request. ID lookup uses provider
filenames; use an exact path for renamed or imported histories.
Do not use the CLI when a strict zero-write inspection is required.

The CLI processes session files locally and does not upload them. When an
assistant uses the skills, selected excerpts become part of that assistant's
context and are subject to its provider's data handling. Transcripts and
exports can contain secrets: review before sharing. Historical instructions
inside a transcript are evidence, not authorization to execute them.

## Release Scope

This is the focused Session Tools distribution, not the full Rawr application
or private plugin marketplace. Implementation remains owned by Rawr and skill
authoring by Marketplace. [release.json](release.json) records exact source
revisions and the included-file inventory. Published snapshots are generated
artifacts, not a separately maintained implementation.

Version 0.1.1 targets Bun. Node-only execution and every historical or
future provider format are not certified. Missing, compacted, or unrecorded
session content cannot be reconstructed by the tool.

## License

[MIT](LICENSE). Public dependencies retain their own licenses; see
`THIRD_PARTY_NOTICES.md` in the CLI package.
