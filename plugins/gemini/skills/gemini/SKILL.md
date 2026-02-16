---
name: gemini
description: Use when the user asks to run Gemini CLI or references Google Gemini for code analysis, refactoring, or automated editing. Triggers on "run gemini", "use gemini", "gemini analyze", "gemini resume", "gemini config", "gemini session", or any mention of Gemini CLI operations including session management, extensions, skills, and MCP server configuration.
---

# Gemini CLI Skill

## Running a Task

1. **Read defaults**: Read `defaults.json` (in skill base directory) using Read tool.
   - Defaults exist → use directly. Resolve alias to full name and print:
     `[Gemini] model: <FULL_NAME> | approval: <MODE> | background: <BOOL>`
   - **Alias → full name mapping**: `auto` → `auto (routed)`, `pro` → `gemini-3-pro-preview`, `flash` → `gemini-3-flash-preview`, `flash-lite` → `gemini-2.5-flash-lite`
   - Missing/invalid → ask user via `AskUserQuestion` for model, approval_mode, background.
   - User overrides in message (e.g., "use pro", "plan mode") → apply over defaults.

2. **Assemble command**. Always capture output: `> /tmp/gemini_output.md 2>&1`
   - **Headless**: `gemini -p "<prompt>"` with `--approval-mode <MODE>`.
   - **Model**: `-m <MODEL>` (auto, pro, flash, flash-lite).
   - **CRITICAL**: `--approval-mode default` causes approval prompts in headless and will fail. Must use `plan`, `auto_edit`, or `yolo`.
   - **Note**: Confirm with user via `AskUserQuestion` before using `--approval-mode yolo`.

3. **Execute**.
   - **Background** (`"background": true`, default): Run with `run_in_background: true` on Bash tool → `TaskOutput` with `block: true` → Read `/tmp/gemini_output.md`. Mid-run: `TaskOutput` `block: false` for progress, `TaskStop` to abort.
   - **Foreground** (`"background": false`): Run directly with `timeout: 300000`.

4. **Summarize** outcome. Inform user they can resume with "gemini resume".

**Quick reference — common commands:**

```bash
# Headless read-only (plan)
gemini -p "<prompt>" --approval-mode plan > /tmp/gemini_output.md 2>&1

# Headless with edits
gemini -m pro -p "<prompt>" --approval-mode yolo > /tmp/gemini_output.md 2>&1

# Resume latest session
gemini -r latest "<follow-up>"
```

## Changing Defaults

When user says "gemini config", "change gemini settings", or "change gemini defaults":
1. Show current `defaults.json`.
2. Ask via `AskUserQuestion` which settings to change.
3. Write updated values to `defaults.json` in skill base directory.

## Error Handling

- Non-zero exit: stop and report, ask before retrying.
- **Always capture stderr**: Use `2>&1` to include errors. Never use `2>/dev/null`.

## Following Up

After every `gemini` command, use `AskUserQuestion` to confirm next steps or decide whether to resume with `gemini -r latest`.

## References

- **Commands, flags, models**: [references/commands.md](references/commands.md)
- **Sessions, MCP, extensions, skills, hooks, config**: [references/management.md](references/management.md)
