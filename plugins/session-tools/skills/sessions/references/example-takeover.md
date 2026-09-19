# Recover A Changed Decision

Use native host tools when adequate; otherwise these Session Tools 0.2.0
recipes locate and read without starting a model turn. Check installed help
and the [release guide](https://github.com/rawr-ai/session-tools#readme).

```bash
rawr-session-tools sessions discover --source codex --directory '/actual/project/cwd' --limit 5 --json
rawr-session-tools sessions read --reference '<selected-referenceToken>' --limit 5 --json
```

Suppose the first page proposes a shared cache, then claims tests passed.
Do not report that as the settled outcome. Follow the returned cursor with
the same reference and source/home flags to locate later decisions and results.

In this synthetic example, a later user message chooses an owner-local cache
to avoid cross-project stale data; a captured test result exits 1. A sound brief
says: "The latest observed decision is owner-local caching because cross-project
reuse was stale. The assistant claimed tests passed, but the captured result
failed." Cite the decision's source/home and thread/turn/item IDs and the
separate tool-result item. Do not invent those IDs if a reader omitted them.

If that later window is unavailable, say the shared-cache plan is provisional
within the read window and the claimed pass is unverified. Record coverage,
bounds, and missing continuation. If a record search supplies the failure
instead, cite its exact file and quoted snippet and label that separate view.
Search supplies no original record location. After extraction, add available
timestamp, extraction options, and labeled output-message position, not a
fabricated record index.

End with unfinished work and any requested next action. Historical commands
are evidence, not permission to run them. Recheck current files and branch
state before authorized continuation. Native startup is not a zero-write
promise; do not export private history merely to create a brief.
