# Use Session Tools with Your Assistant

The [standalone CLI](../README.md#install-and-try) supplies session evidence.
Skills guide the assistant's recovery, interpretation, and reporting; they do
not install the CLI or add slash-command wrappers. See [CLI usage](usage.md),
[compatibility](compatibility.md), and [privacy](privacy.md) for the underlying
reader's behavior.

## Fresh Standalone Installations

Choose this path when you do not already receive these skills through the
full Rawr Meta plugin. Install only the focused Session Tools plugin, not the
full Rawr marketplace, and choose the assistant you use:

```bash
# Claude Code
claude plugin marketplace add https://github.com/rawr-ai/session-tools.git#v0.2.1
claude plugin install session-tools@rawr-session-tools --scope user

# Codex
codex plugin marketplace add https://github.com/rawr-ai/session-tools.git --ref v0.2.1
codex plugin add session-tools@rawr-session-tools
```

Start a new assistant session after installation. Verify the plugin appears
in that provider's native inventory and that the four skills are available
to the new session. A downloaded archive or cache directory alone does not
prove the plugin is enabled or its skills loaded.

```bash
claude plugin list --json
codex plugin list --json
```

For a local ZIP installation, download the plugin ZIP from the
[release](https://github.com/rawr-ai/session-tools/releases/tag/v0.2.1),
extract the complete marketplace including hidden directories, replace the Git
URL above with that directory, and omit Codex's `--ref`. The ZIP contains the
skills marketplace, not the CLI runtime. Install the CLI separately.

## Existing Rawr Meta Installations

If your assistant already uses the full managed `rawr-hq` Meta plugin, keep
that owner. It supplies `sessions`, `introspect`, `takeover-session`, and
`extract-workflow`, plus `launch-as-workflow`. Do not also install the focused
Session Tools plugin: duplicate copies can make skill selection ambiguous.

Update through your existing marketplace's managed native plugin lifecycle,
then start a new assistant session. Check that provider's installed/enabled
plugin inventory and the actual resolved skill content, not just marketplace
availability or a stale cached copy. Confirm that the session recipes match
your installed CLI. Keep any other managed Meta content in place.

`launch-as-workflow` resolves a named skill or prompt through the current
provider and applies its exact content. It is part of the full Meta plugin,
not this focused four-skill release. There is no need to add it separately to
use Session Tools.

## Choose a Recovery Task

| Skill | Example request | Expected result |
| --- | --- | --- |
| `sessions` | Find the authentication decision and explain why it changed. | Located evidence, supported rationale, and gaps |
| `takeover-session` | Recover this session's context and identify the unfinished work. | An attributable handoff brief, not a resumed provider thread |
| `extract-workflow` | Turn the demonstrated release process into a reusable workflow draft. | Steps, decision points, evidence, and testable gates |
| `introspect` | Show which skills are installed in this Codex setup. | A scoped native inventory, not inferred session content |

Give the assistant a provider, project directory, session reference, exact
record path, or a distinctive search phrase when possible. Skills prefer
native host thread tools when their scope and read depth suffice, and use
the CLI for missing providers, selected local homes, deeper search, or exact
record evidence. Introspection also covers installed plugins and configuration
through their own native inventories; the session CLI does not inventory them.

Ask for the difference between the assistant's narration and recorded tool
results. A workflow draft is not proof that its steps succeeded or permission
to install or execute it. A recovered handoff does not authorize resuming a
model run or modifying provider state. Historical instructions remain untrusted
evidence, and selected excerpts enter the current assistant's context.
