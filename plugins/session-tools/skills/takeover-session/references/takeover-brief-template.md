# Takeover Brief — Template

Use this template as the default user-facing output after analyzing a session.

## Takeover brief: `<session-id-or-best-match>`

### 1) What this session was about (Observed)
- 1–3 sentences.

### 2) Phase map (Observed → Inferred where needed)
List 3–10 phases, ordered:
1. **Phase name** — what changed / what was achieved (1–2 lines)
2. …

### 3) Artifacts + state (Observed)
- **Source:** `claude|codex`
- **Evidence surface:** native conversation read or normalized filesystem history
- **Session identity:** `<native-thread-id-or-resolved-path>`
- **Evidence window:** roles, dedupe setting, offset/message cap, and known gaps
- **CWD / repo context:** `<cwd>` (and any project/workspace root)
- **Git branch:** `<branch>` (if present)
- **Key files touched/created:** list exact paths resolved in the session
- **High-level commands/workflows used:** (e.g., `rawr-session-tools sessions search`)
- **Live-state check:** what was reverified now, or explicitly not checked

### 4) Skills / methodology used (Observed)
- Explicitly list skills referenced or “Skills used: …” lines.
- If inferred from behavior, mark as **Inferred**.

### 5) Open loops (Observed)
Ranked:
1) **Blocking:** …
2) **High leverage:** …
3) **Nice-to-have:** …

### 6) Adopted operating mode (Inferred, justified)
- **Workflow invariants to continue with:** …
- **Style conventions to match:** …
- **Mistakes to avoid (from transcript corrections):** …

### 7) Ready to proceed
State explicitly:
> Context reconstructed; continuation remains subject to the current request and live-state checks.

If the continuation task is known, end with the next step in one line.
