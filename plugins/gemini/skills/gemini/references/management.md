# Gemini CLI Management Reference

## Session Management

**CLI:**
```bash
gemini --list-sessions                 # List all sessions
gemini -r latest "continue..."        # Resume most recent
gemini -r <index|UUID> "..."          # Resume by index or UUID
gemini --delete-session <index|UUID>  # Delete session
gemini --session-summary <path>          # Write JSON session summary to file
```

**In-session:**
- `/resume` — Interactive session browser (search/filter/sort)
- `/chat save <tag>` — Save checkpoint
- `/chat resume <tag>` — Restore checkpoint
- `/chat list` — Show checkpoints
- `/chat delete <tag>` — Remove checkpoint
- `/chat share [filename]` — Export to Markdown/JSON
- `/compress` — Replace context with summary (saves tokens)
- `/rewind` — Navigate backward through conversation

Session retention: configure `sessionRetention.maxAge`, `sessionRetention.maxCount` in `settings.json`.
Sessions stored at `~/.gemini/tmp/<project_hash>/chats/`.

## MCP Server Management

**CLI:**
```bash
gemini mcp list                                                # List configured servers
gemini mcp add <name> <command> [args...]                      # Add stdio server
gemini mcp add <name> <url> --transport http                   # Add HTTP server
gemini mcp add <name> <url> --transport sse                    # Add SSE server
gemini mcp add <name> <command> --env KEY=value                # Add with env vars
gemini mcp add <name> <cmd> --include-tools t1,t2              # Include specific tools
gemini mcp add <name> <cmd> --exclude-tools t1,t2              # Exclude specific tools
gemini mcp add <name> <cmd> --scope user                       # Add at user scope
gemini mcp add <name> <cmd> --trust                            # Trust (bypass confirmation)
gemini mcp add <name> <cmd> --timeout 30000                    # Connection timeout (ms)
gemini mcp remove <name>                                       # Remove a server
gemini mcp enable <name>                                       # Enable server
gemini mcp disable <name>                                      # Disable server
```

**In-session:**
- `/mcp list` (or `/mcp ls`) — Show servers, status, tools
- `/mcp desc` — List with descriptions
- `/mcp schema` — List with schemas
- `/mcp auth [server]` — OAuth flow
- `/mcp refresh` — Restart all servers

## Extensions Management

**CLI:**
```bash
gemini extensions list                                         # List installed
gemini extensions install <git-url-or-path>                    # Install
gemini extensions install <source> --auto-update               # Install with auto-update
gemini extensions install <source> --pre-release               # Install pre-release
gemini extensions uninstall <name>                             # Uninstall
gemini extensions update <name>                                # Update specific
gemini extensions update --all                                 # Update all
gemini extensions enable <name>                                # Enable
gemini extensions disable <name>                               # Disable
gemini extensions link <local-path>                            # Link for development
gemini extensions new <path> [template]                        # Create from template
gemini extensions validate <path>                              # Validate structure
gemini extensions config [name] [setting]                      # Configure settings
```

**In-session:**
- `/extensions` — List active extensions

Runtime flag: `-e <ext1> -e <ext2>` to load specific extensions.

## Skills Management

**CLI:**
```bash
gemini skills list                                             # List discovered skills
gemini skills list --all                                       # List all (including disabled)
gemini skills install <git-url-or-path>                        # Install
gemini skills install <source> --scope user                    # Install at user scope
gemini skills uninstall <name>                                 # Uninstall
gemini skills enable <name>                                    # Enable
gemini skills disable <name>                                   # Disable
```

**In-session:**
- `/skills list` — Show all skills and status
- `/skills enable <name>` / `disable <name>` — Toggle
- `/skills reload` — Refresh discovery

## Hooks Management

**CLI:**
```bash
gemini hooks migrate                                           # Migrate hooks from Claude Code
```

**In-session:**
- `/hooks list` — Show all hooks with status
- `/hooks enable <name>` / `disable <name>` — Toggle
- `/hooks enable-all` / `disable-all` — Bulk control

## Interactive Slash Commands (Quick Reference)

**Core:** `/model`, `/plan`, `/copy`, `/compress`, `/stats`, `/vim`, `/theme`, `/clear`
**Session:** `/resume`, `/chat`, `/rewind`, `/restore`
**Files & Tools:** `/tools`, `/mcp`, `/extensions`, `/skills`, `/hooks`
**Project:** `/init` (GEMINI.md), `/memory` (add/list/show/refresh), `/directory` (add/show)
**System:** `/settings`, `/shortcuts`, `/about`, `/help`, `/quit`
**Special:** `/bug` (file issue), `/auth` (change auth), `/editor`, `/ide`, `/docs`
**Shell:** `!<command>` (execute shell), `@<path>` (inject file content)

## Configuration

**Config hierarchy** (lowest → highest priority):
1. Hardcoded defaults
2. System defaults (`/etc/gemini-cli/system-defaults.json`)
3. User settings (`~/.gemini/settings.json`)
4. Project settings (`.gemini/settings.json`)
5. System overrides (`/etc/gemini-cli/settings.json`)
6. Environment variables
7. CLI arguments

**GEMINI.md context files** — hierarchical loading:
1. Global: `~/.gemini/GEMINI.md`
2. Project-level: current dir → project root (.git)
3. Component-level: subdirectories (respects .gitignore)

Use `/init` to auto-generate GEMINI.md after project analysis. Use `/memory show` to view loaded context.

**Edit settings:** `/settings` command or edit `~/.gemini/settings.json` directly.
