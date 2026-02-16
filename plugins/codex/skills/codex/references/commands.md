# Codex CLI Command Reference

## Commands

| Command | Alias | Purpose |
|---------|-------|---------|
| `codex` | | Interactive terminal UI |
| `codex exec` | `codex e` | Non-interactive execution |
| `codex review` | | Code review (non-interactive) |
| `codex resume` | | Resume previous session |
| `codex fork` | | Fork previous session |
| `codex cloud` | | Cloud task management |
| `codex apply` | `codex a` | Apply cloud diffs locally |
| `codex mcp` | | MCP server management |
| `codex mcp-server` | | Run as MCP server (stdio, experimental) |
| `codex app-server` | | App server with websocket transport (experimental) |
| `codex login` | | Authentication |
| `codex logout` | | Remove credentials |
| `codex app <path>` | | Launch macOS desktop app |
| `codex sandbox` | | Run commands in sandbox (`macos`/`linux`/`windows`) |
| `codex completion` | | Shell completion scripts |
| `codex features` | | Feature flags (`list`, `enable`, `disable`) |
| `codex debug` | | Debugging tools |

## `codex exec` Flags

| Flag | Description |
|------|-------------|
| `-m, --model <MODEL>` | Override model |
| `-s, --sandbox <MODE>` | `read-only` \| `workspace-write` \| `danger-full-access` |
| `-c, --config <key=value>` | Config override (repeatable, parsed as TOML) |
| `-p, --profile <PROFILE>` | Config profile from config.toml |
| `-C, --cd <DIR>` | Working directory |
| `-i, --image <FILE>...` | Attach images |
| `-o, --output-last-message <FILE>` | Write final message to file |
| `--full-auto` | = `-a on-request -s workspace-write` |
| `--dangerously-bypass-approvals-and-sandbox` | Remove all safety (alias: `--yolo`) |
| `--json` | JSONL output to stdout |
| `--ephemeral` | Don't persist session |
| `--output-schema <FILE>` | JSON Schema for response shape |
| `--skip-git-repo-check` | Allow outside Git repo |
| `--add-dir <DIR>` | Additional writable directories |
| `--oss` | Use local model provider |
| `--local-provider <lmstudio\|ollama>` | Specify local provider |
| `--color <always\|never\|auto>` | Color output |
| `--enable/--disable <FEATURE>` | Toggle feature flags |

**Subcommands**: `exec resume` and `exec review` accept `-m`, `--full-auto`, `--json`, `--ephemeral`, `--skip-git-repo-check`.

**stderr**: Progress goes to stderr. Capture with `> /tmp/codex_output.md 2>&1`.

## `codex review` Flags

**No `-m` flag.** Use `-c model="<MODEL>"` to set model.

| Flag | Description |
|------|-------------|
| `-c, --config <key=value>` | Config override (including model) |
| `--uncommitted` | Review staged + unstaged + untracked |
| `--base <BRANCH>` | Review against base branch |
| `--commit <SHA>` | Review specific commit (cannot combine with PROMPT) |
| `--title <TITLE>` | Commit title for summary |
| `--enable/--disable <FEATURE>` | Feature flags |

```bash
codex review                               # Current branch vs base
codex review --uncommitted                 # All local changes
codex review --base main                   # Against specific branch
codex review --commit <SHA>                # Specific commit
codex review "Focus on security"           # Custom instructions
```

Also available via `codex exec review` (which accepts `-m`).

## Interactive-Only Flags

Available on `codex`, `resume`, `fork` — **not** on `exec`:

| Flag | Description |
|------|-------------|
| `-a, --ask-for-approval <POLICY>` | `untrusted` \| `on-failure` \| `on-request` \| `never` |
| `--search` | Enable live web search |
| `--no-alt-screen` | Inline TUI mode (for terminal multiplexers) |

## Models

| Model | Description |
|-------|-------------|
| `gpt-5.3-codex` | Latest frontier agentic coding (Recommended) |
| `gpt-5.3-codex-spark` | Ultra-fast real-time iteration (Pro only) |
| `gpt-5.2-codex` | Frontier agentic coding |
| `gpt-5.2` | General frontier model |
| `gpt-5.1-codex-max` | Deep reasoning, long-horizon |
| `gpt-5.1-codex-mini` | Cost-effective (reasoning: `medium`/`high` only) |
| `gpt-5.1` | General coding/agentic |
| `gpt-5.1-codex` | Agentic coding (superseded) |
| `gpt-5-codex` | Agentic coding (superseded) |
| `gpt-5-codex-mini` | Cost-effective (superseded) |
| `gpt-5` | Reasoning model (superseded) |

Omitting `-m` uses account default. Local models: `--oss` with `--local-provider`.

## Reasoning Effort

Set via `-c model_reasoning_effort="<level>"`. **Always use full key=value syntax.**

| Level | Notes |
|-------|-------|
| `minimal` | Fastest. Incompatible with web_search tool |
| `low` | Fast with light reasoning |
| `medium` | Balanced (default) |
| `high` | Greater depth |
| `xhigh` | Maximum depth |

gpt-5.1-codex-mini: `medium` and `high` only.

## Sandbox Modes

| Mode | Behavior |
|------|----------|
| `read-only` | No writes (default) |
| `workspace-write` | Writes to workspace/tmp only |
| `danger-full-access` | Full disk/network (**confirm with user**) |

## `-c` Config Override Examples

```bash
-c model_reasoning_effort="xhigh"
-c model="gpt-5.3-codex"
-c 'sandbox_permissions=["disk-full-read-access"]'
-c shell_environment_policy.inherit=all
```

Multiple `-c` flags can be combined in a single command.
