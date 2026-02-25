# Gemini CLI Command Reference

## Interactive vs Headless

| Mode | How | Description |
| --- | --- | --- |
| Interactive | `gemini` | Full-screen terminal UI |
| Interactive with prompt | `gemini -i "<prompt>"` | Start session with initial prompt |
| Headless | `gemini -p "<prompt>"` | Non-interactive, scriptable. **`-p` required** |

## Headless Mode (`-p`)

| Use case | Command |
| --- | --- |
| Read-only review (plan) | `gemini -p "<prompt>" --approval-mode plan` |
| Auto-approve edits | `gemini -p "<prompt>" --approval-mode auto_edit` |
| Auto-approve all | `gemini -p "<prompt>" --approval-mode yolo` |
| JSON output | `gemini -p "<prompt>" --output-format json` |
| Streaming JSON | `gemini -p "<prompt>" --output-format stream-json` |
| Pipe input | `cat file.txt \| gemini -p "<prompt>"` |
| Restrict tools | `gemini -p "<prompt>" --allowed-tools tool1,tool2` |
| With sandbox | `gemini -s -p "<prompt>" --approval-mode yolo` |

**Important notes:**
- `--approval-mode default` causes approval prompts in headless and will fail. **Must use `plan`, `auto_edit`, or `yolo`.**
- Save output to file: `> /tmp/gemini_output.md 2>&1`
- Parse JSON: `--output-format json` then `jq '.response'`

## CLI Flags

| Flag | Description |
| --- | --- |
| `-p, --prompt` | Headless mode (non-interactive) |
| `-i, --prompt-interactive` | Interactive with initial prompt |
| `-m, --model` | Specify model (alias or full name) |
| `-r, --resume` | Resume session (`latest`, index, or UUID) |
| `-s, --sandbox` | Enable sandbox (Docker/seatbelt isolation) |
| `-y, --yolo` | Shortcut for `--approval-mode yolo` |
| `-e, --extensions` | Specify extensions to load |
| `-o, --output-format` | `text` (default), `json`, `stream-json` |
| `-d, --debug` | Enable debug mode (F12 for debug console) |
| `--approval-mode` | `default`, `auto_edit`, `plan`, `yolo` |
| `--include-directories` | Add directories to workspace |
| `--allowed-tools` | Restrict available tools **(deprecated v0.30.0 → policy engine)** |
| `--allowed-mcp-server-names` | Restrict MCP servers |
| `--list-sessions` | Show all sessions |
| `--delete-session` | Remove session by index/UUID |
| `--session-summary <path>` | Write JSON session summary to file |
| `--checkpointing` | Enable `/restore` checkpointing feature |
| `--screen-reader` | Enable screen reader mode |
| `--raw-output` | Disable sanitization (security risk) |
| `--experimental-acp` | Start in ACP mode |

**Deprecated:** `-y, --yolo` → use `--approval-mode yolo`. `--allowed-tools` → use policy engine.

## Models

**Auto routing** (default): Routes between models based on task complexity. Simple prompts → Flash Lite (routing), Complex → Flash/Pro.

| Alias | Full name | Best for |
| --- | --- | --- |
| `auto` (default) | Routes to best model per task | Most tasks (Recommended) |
| `pro` | `gemini-3-pro-preview` | Complex reasoning, large codebases |
| `flash` | `gemini-3-flash-preview` | Fast, balanced quality |
| `flash-lite` | `gemini-2.5-flash-lite` | Fastest, simple tasks |
| `gemini-3.1-pro` | `gemini-3.1-pro-preview` | Latest frontier reasoning (preview) |

**Additional full names:** `gemini-2.5-pro`, `gemini-2.5-flash`

**Model selection:** `-m pro` or `/model` command in session.

**Check actual model used:** Add `--output-format json` to see which model(s) were invoked in `stats.models`.

## Approval Modes

| Mode | Behavior |
| --- | --- |
| `default` | Prompt for each tool call (not available in headless) |
| `auto_edit` | Auto-approve file edits, prompt for others |
| `plan` | Read-only, planning workflow |
| `yolo` | Auto-approve everything — **confirm with user first** |

**Mode switching:** Shift+Tab to cycle through modes in session.

## Output Formats

| Format | Description |
| --- | --- |
| `text` (default) | Human-readable response |
| `json` | Structured data with stats, token usage, tool calls |
| `stream-json` | JSONL real-time events (`init`, `message`, `tool_use`, `tool_result`, `error`, `result`) |

## Sandbox Mode

macOS seatbelt or Docker/Podman based isolation.

```bash
gemini -s                                       # Interactive with sandbox
gemini -s -p "<prompt>" --approval-mode yolo    # Headless with sandbox
```

Environment variable: `GEMINI_SANDBOX=true|docker|podman|sandbox-exec`

macOS seatbelt profiles: `permissive-open` (default), `permissive-proxied`, `restrictive-open`, `restrictive-proxied`, `strict-open`, `strict-proxied`
