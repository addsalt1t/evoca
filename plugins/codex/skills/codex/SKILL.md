---
name: codex
description: Use when the user asks to run Codex CLI or references OpenAI Codex for code analysis, refactoring, or automated editing. Triggers on "run codex", "use codex", "codex analyze", "codex review", "codex resume", "codex config", "codex cloud", or any mention of Codex CLI operations including session management, MCP server configuration, cloud tasks, and settings changes.
---

# Codex CLI Skill

## Running a Task

1. **Read defaults**: Read `defaults.json` (in skill base directory) using Read tool.
   - Defaults exist → use directly. Print: `[Codex] model: <MODEL> | reasoning: <LEVEL> | sandbox: <MODE> | background: <BOOL>`
   - Missing/invalid → ask user via `AskUserQuestion` for model, reasoning, sandbox, background.
   - User overrides in message (e.g., "use gpt-5.3-codex") → apply over defaults.

2. **Assemble command**. Always capture output: `> /tmp/codex_output.md 2>&1`
   - **`codex exec`**: Use `-m <MODEL>` and `-c model_reasoning_effort="<LEVEL>"`.
   - **Sandbox**: analysis/review → `-s read-only` (default). editing/writing → `-s workspace-write`. `danger-full-access` requires user confirmation.
   - **`codex review`** (direct): Use `-c model="<MODEL>"` instead. Cannot combine `--commit <SHA>` with `[PROMPT]`.
   - **`codex exec review`**: Accepts `-m <MODEL>` directly (alternative to `codex review`).
   - **CRITICAL**: `-c` requires full `key=value` syntax. Use `-c model_reasoning_effort="xhigh"`. Never `-c xhigh`.
   - **Note**: `minimal` reasoning level is incompatible with web_search tool.
   - **Note**: `-i <FILE>` must come after `[PROMPT]`, not before. Otherwise the prompt is consumed by `-i`.

3. **Execute**.
   - **Background** (`"background": true`, default): Run with `run_in_background: true` on Bash tool → immediately `TaskOutput` with `block: false` (startup check: if already completed with non-zero exit, stop and report error) → then `TaskOutput` with `block: true` → Read `/tmp/codex_output.md`. Mid-run: `TaskOutput` `block: false` for progress, `TaskStop` to abort.
   - **Foreground** (`"background": false`): Run directly with `timeout: 300000`.

4. **Summarize** outcome. Inform user they can continue with "codex resume".

## Changing Defaults

When user says "codex config", "change codex settings", or "change codex defaults":
1. Show current `defaults.json`.
2. Ask via `AskUserQuestion` which settings to change.
3. Write updated values to `defaults.json` in skill base directory.

## Error Handling

- Non-zero exit: stop and report, ask before retrying.
- Before `-s danger-full-access`: confirm with user via `AskUserQuestion`.

## References

- **Commands, flags, models**: [references/commands.md](references/commands.md)
- **Sessions, MCP, Cloud, config**: [references/management.md](references/management.md)
