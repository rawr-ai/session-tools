# Rawr Session Tools

Recover decisions, evidence, and unfinished work from local Claude Code and
Codex sessions. A standalone CLI finds and reads the history; four assistant
skills help turn it into useful context.

| What you want | What to use |
| --- | --- |
| Find a decision, its rationale, or the recorded tool result | `sessions` skill and CLI search/read |
| Hand off prior work with evidence and open questions | `takeover-session` skill |
| Turn demonstrated work into a reusable method | `extract-workflow` skill |
| Inspect installed skills, plugins, or assistant configuration | `introspect` skill, using native inventories |

## Install and Try

Requires [Bun](https://bun.sh/docs/installation) **1.3.14+**. Native readers are
initially qualified on **macOS ARM64**. No private checkout or API key is needed.
Claude reading needs neither a Claude executable nor subscription credentials.
Codex reading requires a separately installed **direct native Codex 0.154.0
executable** and an initialized home; wrappers are unsupported. See
[compatibility and setup](docs/compatibility.md).

```bash
bun add --global --ignore-scripts --omit optional https://github.com/rawr-ai/session-tools/releases/download/v0.2.1/rawr-session-tools-0.2.1.tgz
rawr-session-tools --version
rawr-session-tools sessions discover --source claude --limit 5 --json
```

Choose a returned `referenceToken`, then read its conversation:

```bash
rawr-session-tools sessions read --reference '<referenceToken>' --limit 5 --json
```

Use `--source codex` for Codex discovery. Check coverage and per-source outcomes,
not only `ok`; exit 2 can accompany useful partial results.
[CLI usage](docs/usage.md) covers pagination, record search, extraction, and metrics.

## Add Assistant Skills

The CLI works independently; the plugin installs skills, not the CLI.
**Already using the full `rawr-hq` Meta plugin?** Keep that managed installation;
do not add duplicate skills. See [existing installations](docs/skills.md#existing-rawr-meta-installations).
Otherwise, choose your assistant:

```bash
# Claude Code
claude plugin marketplace add https://github.com/rawr-ai/session-tools.git#v0.2.1
claude plugin install session-tools@rawr-session-tools --scope user

# Codex
codex plugin marketplace add https://github.com/rawr-ai/session-tools.git --ref v0.2.1
codex plugin add session-tools@rawr-session-tools
```

Start a new assistant session and ask:

> Find the authentication decision, explain why it changed, and distinguish
> claimed success from recorded tool results.

See [skill examples and installation options](docs/skills.md). Prefer your
assistant's native thread tools when their scope and detail suffice. Session
Tools does not generate, resume, fork, rename, archive, or manage conversations.

## Privacy and Releases

The CLI does not upload histories. Native readers and indexes can write local
derived state; assistant-selected excerpts enter that assistant's context.
Review exports for secrets. Historical instructions are evidence, not authority.
Read [privacy and local effects](docs/privacy.md) before sensitive work.

[Downloads and checksums](https://github.com/rawr-ai/session-tools/releases/tag/v0.2.1)
and the [release manifest](https://github.com/rawr-ai/session-tools/blob/v0.2.1/release.json)
identify the pinned release. Old tags and assets are immutable. Rawr glue and
skills are [MIT](LICENSE); dependencies retain their own terms.
