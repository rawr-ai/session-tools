# Rawr Session Tools

Search local Claude Code and Codex conversations from one CLI, with matching
skills for either assistant. Find a previous discussion, extract a transcript,
recover decisions, or carry useful context into a new session.

## Install the CLI

Requires [Bun](https://bun.sh/docs/installation) 1.3.14 or newer. No private
repository access, API key, or Rawr/Habitat checkout is needed.

```bash
bun add --global https://github.com/rawr-ai/session-tools/releases/download/v0.1.0/rawr-session-tools-0.1.0.tgz
rawr-session-tools --version
rawr-session-tools sessions list --source all --limit 5 --table
```

If the command is not found, add Bun's global bin directory (`bun pm bin -g`)
to your shell's `PATH`.

[Download this release](https://github.com/rawr-ai/session-tools/releases/tag/v0.1.0)
for the CLI package, plugin ZIP, and SHA-256 checksums. The ZIP is the skills
marketplace, not a substitute for installing the CLI.

## Add the Skills to Your Assistant

Install the CLI first, then choose either or both providers. These commands
install only this focused plugin, not the full Rawr marketplace.

### Claude Code

```bash
claude plugin marketplace add https://github.com/rawr-ai/session-tools.git#v0.1.0
claude plugin install session-tools@rawr-session-tools --scope user
```

### Codex

```bash
codex plugin marketplace add https://github.com/rawr-ai/session-tools.git --ref v0.1.0
codex plugin add session-tools@rawr-session-tools
```

Start a new agent session after installation. Ask, for example:

> Find the Claude or Codex conversation where we discussed the authentication
> refactor, and summarize the decisions with references to the source session.

The plugin provides `sessions`, `introspect`, `takeover-session`, and
`extract-workflow` skills. They guide the assistant; they do not install the
CLI automatically. No additional slash-command wrappers are included.

For a local ZIP installation, unpack the complete marketplace, including its
hidden directories. Substitute the extracted directory for the Git URL in
the marketplace-add command, omit Codex's `--ref`, then install the plugin as
above. Use current provider versions with native plugin support.

## Search Without an Assistant

Start with metadata, then search transcript text or extract an exact session:

```bash
rawr-session-tools sessions search --source all --query-metadata "authentication" --limit 5 --json
rawr-session-tools sessions search --source all --query "refresh.token" --ignore-case --max-matches 5 --json
rawr-session-tools sessions resolve <session-id-or-path> --json
rawr-session-tools sessions extract <session-id-or-path> --format markdown --max-messages 30
```

Use `rawr-session-tools sessions <command> --help` for filters, extraction
bounds, output formats, and indexing. The five commands are `list`, `resolve`,
`search`, `extract`, and `metrics`. Metrics describe the events present in
local files; they are not authoritative usage or billing records.

## What It Reads and Writes

The CLI reads Claude sessions under `~/.claude/projects` and Codex sessions
under `$CODEX_HOME`, `~/.codex`, and `~/.codex-rawr`, including archived Codex
sessions. `CODEX_HOME` adds a discovery root; it does not exclude the defaults.
Custom `CLAUDE_CONFIG_DIR` session discovery is not supported in this release.

Source transcripts are not modified. Discovery can create local file-list
caches; indexed searches can write SQLite caches. `--reindex` deliberately
rebuilds the selected search index, and `--out-dir` deliberately writes
exports. Set `RAWR_SESSION_INDEX_PATH` to choose the SQLite index location.
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

Version 0.1.0 targets Bun. Node-only execution and every historical or
future provider format are not certified. Missing, compacted, or unrecorded
session content cannot be reconstructed by the tool.

## License

[MIT](LICENSE). Public dependencies retain their own licenses; see
`THIRD_PARTY_NOTICES.md` in the CLI package.
