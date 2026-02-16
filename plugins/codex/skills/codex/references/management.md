# Codex CLI Management Reference

## Session Management

```bash
codex resume                            # Resume with picker
codex resume --last                     # Resume most recent
codex resume --last "continue..."       # Resume with prompt
codex resume <UUID> "prompt"            # Resume by ID
codex resume --last --all               # Resume from any directory
codex fork                              # Fork with picker
codex fork --last                       # Fork most recent
codex fork <UUID> "new direction..."    # Fork specific session
codex exec resume --last "prompt"       # Resume non-interactively
```

Sessions stored at `~/.codex/sessions/`. Resumed sessions inherit original settings; override with explicit flags.

## MCP Server Management

```bash
codex mcp add <name> -- <command> [args]                         # Add stdio server
codex mcp add <name> --env KEY=VALUE -- <command>                # With env vars
codex mcp add <name> --url <URL>                                 # Add HTTP server
codex mcp add <name> --url <URL> --bearer-token-env-var <VAR>   # HTTP with auth
codex mcp list [--json]                                          # List servers
codex mcp get <name> [--json]                                    # Show server config
codex mcp remove <name>                                          # Remove server
codex mcp login <name>                                           # OAuth login (HTTP)
codex mcp logout <name>                                          # Remove credentials
```

Inside interactive session: `/mcp` lists active tools.

**config.toml MCP configuration:**

```toml
[mcp_servers.my_server]
command = "npx"
args = ["-y", "@upstash/context7-mcp"]
enabled = true
enabled_tools = ["tool1", "tool2"]      # Whitelist (optional)
disabled_tools = ["tool3"]              # Blacklist (optional)
startup_timeout_sec = 30
tool_timeout_sec = 60

[mcp_servers.http_server]
url = "https://api.example.com/mcp"
bearer_token_env_var = "API_TOKEN"
```

## Cloud Tasks

```bash
codex cloud                                              # Interactive cloud TUI
codex cloud list [--env <ID>] [--limit N] [--json]       # List tasks
codex cloud exec --env <ID> "prompt"                     # Submit task
codex cloud exec --env <ID> --attempts 3 --branch feat "prompt"
codex cloud status <TASK_ID>                             # Show task status
codex cloud diff <TASK_ID> [--attempt N]                 # Show diff
codex cloud apply <TASK_ID> [--attempt N]                # Apply diff locally
codex apply                                              # Apply latest diff (shortcut)
```

GitHub integration: Tag `@codex` in issues/PRs for cloud tasks, `@codex review` for PR reviews.

## Authentication

```bash
codex login                             # OAuth (browser)
codex login --device-auth               # Device code flow
codex login --with-api-key              # Read API key from stdin
codex login status                      # Check auth state
codex logout                            # Remove credentials
```

Also: `CODEX_API_KEY` environment variable.

## Slash Commands (Interactive)

**Model & Config:** `/model`, `/personality`, `/permissions`, `/status`, `/debug-config`

**Conversation:** `/plan`, `/compact`, `/new`, `/resume`, `/fork`

**File & System:** `/diff`, `/mention`, `/review`, `/init`, `/ps`

**Memory:** `/m_update`, `/m_drop`

**Utilities:** `/mcp`, `/apps`, `/statusline`, `/feedback`, `/logout`, `/quit`, `/exit`

## Configuration

Config file: `~/.codex/config.toml` (user), `.codex/config.toml` (project)

Precedence: CLI flags > project config > user config > defaults

```toml
model = "gpt-5.3-codex"
approval_policy = "on-request"
sandbox_mode = "workspace-write"
personality = "friendly"                    # none | friendly | pragmatic
web_search = "cached"                       # disabled | cached | live
model_reasoning_effort = "high"
model_reasoning_summary = "auto"            # auto | concise | detailed | none
model_verbosity = "medium"                  # low | medium | high
file_opener = "vscode"                      # vscode | cursor | windsurf | none

[sandbox_workspace_write]
network_access = false
writable_roots = ["/extra/path"]

[tui]
animations = true
alternate_screen = "auto"                   # auto | always | never

[profiles.production]
model = "gpt-5.3-codex"
approval_policy = "untrusted"
sandbox_mode = "read-only"
```

Profiles: `codex -p production` or `codex exec -p dev`.

## AGENTS.md & Skills

**AGENTS.md**: Place at repo root for auto-loading. Generate with `codex init`. Override with `AGENTS.override.md`.

**Skills**: Personal `~/.agents/skills/`, Project `.agents/skills/`. Each needs `SKILL.md`.
