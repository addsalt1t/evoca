---
name: codex
description: Use when the user asks to run Codex CLI or references OpenAI Codex for code analysis, refactoring, or automated editing. Triggers on "codex", "run codex", "use codex", "codex analyze", "codex resume", "codex config", "codex debug", or any mention of Codex CLI operations including session management, MCP server configuration, cloud tasks, and settings changes.
commands:
  codex-review:
    description: "Run native Codex code review via `codex exec review`. Args: commit SHA, --base <branch>, --uncommitted, or empty for current branch."
    user_invocable: true
---

# Codex CLI Skill

## Models

| Model | ID | Use When |
|-------|----|----------|
| **Default** | `gpt-5.3-codex` | Accuracy & depth: complex refactoring, architecture, multi-file changes, debugging |
| **Sub (Spark)** | `gpt-5.3-codex-spark` | Speed & iteration: quick fixes, simple reviews, formatting, single-file edits, rapid prototyping |

## Running a Task

1. **Read defaults**: Read `defaults.json` (in skill base directory) using Read tool.
   - Defaults exist → proceed. Print: `[Codex] model: <MODEL> | reasoning: <LEVEL> | sandbox: <MODE> | background: <BOOL>`
   - Missing/invalid → ask user via `AskUserQuestion`.

2. **Select model** (in priority order):
   - **Explicit model name** in user message → use exactly as given.
   - **Spark keyword** ("spark", "fast", "quick") → use `sub_model`.
   - **Auto-select**: `model` for complex tasks, `sub_model` for simple tasks.
   - **No signal** → use `model`.

3. **Assemble command**:
   ```
   codex exec [PROMPT] -m <MODEL> -c model_reasoning_effort="<LEVEL>" -s <SANDBOX> -o /tmp/codex_output.md 2>/tmp/codex_stderr.log
   ```
   - **Sandbox**: analysis → `-s read-only`. editing → `-s workspace-write`. `danger-full-access` requires user confirmation.
   - **CRITICAL**: `-c` requires full `key=value` syntax. Never `-c xhigh`.
   - `-i <FILE>` and `-o <FILE>` must come **after** `[PROMPT]`.

4. **Execute**:
   - **Background** (default): `run_in_background: true` → `TaskOutput` `block: false` (startup check) → `TaskOutput` `block: true`.
   - **Foreground**: `timeout: 300000`.
   - **On error**: Read `/tmp/codex_stderr.log` for diagnosis.

5. **Read results**: Read `/tmp/codex_output.md` (final message only). If more detail is needed → read `/tmp/codex_stderr.log`.

6. **Summarize** outcome. Inform user they can continue with "codex resume".

## Changing Defaults

When user says "codex config", "change codex settings", or "change codex defaults":
1. Show current `defaults.json`.
2. Ask via `AskUserQuestion` which settings to change.
3. Write updated values to `defaults.json` in skill base directory.

## Error Handling

- Non-zero exit: stop and report, ask before retrying.
- Before `-s danger-full-access`: confirm with user via `AskUserQuestion`.

## `/codex-review` Command

Uses native `codex exec review`. Only triggered when user explicitly types `/codex-review`. Does **not** support `-o` or `-s`.

### Usage

```
/codex-review                     → current branch vs base
/codex-review <SHA>               → specific commit
/codex-review --base main         → against main branch
/codex-review --uncommitted       → all local changes
```

### Flow

1. **Read defaults**. Print: `[Codex Review] model: <MODEL> | reasoning: <LEVEL>`
2. **Select model**: `sub_model` (spark) by default. `model` only if user requests deep review.
3. **Assemble**: `codex exec review [OPTIONS] -m <MODEL> -c model_reasoning_effort="<LEVEL>" 2>/tmp/codex_stderr.log`
   - `--commit <SHA>` cannot be combined with `[PROMPT]`.
4. **Execute**: Same as main skill.
5. **Read results** via Bash: `tail -n 50 /tmp/codex_stderr.log`. If cut off, increase to `-n 100` or more.
6. **Summarize** findings. Inform user they can continue with "codex resume".

## References

- **Commands, flags, models**: [references/commands.md](references/commands.md)
- **Sessions, MCP, Cloud, config**: [references/management.md](references/management.md)
