# Codex

OpenAI Codex CLI integration for Claude Code.

## Prerequisites

```bash
npm install -g @openai/codex
```

OpenAI authentication required (one of the following):

```bash
codex login                    # OAuth via browser
codex login --device-auth      # Device code flow
codex login --with-api-key     # API key from stdin
```

Or set the `CODEX_API_KEY` environment variable.

## Installation

```bash
claude /install codex
```

## How It Works

This plugin registers Codex CLI as a skill inside Claude Code. Once installed, Claude Code can invoke it directly from your conversation — no need to switch terminals or manage separate sessions.

```
User prompt (e.g. "codex review")
  → Skill activated
    → Read defaults.json (model, reasoning, sandbox, background)
      → Assemble CLI command (codex exec -m <model> -c model_reasoning_effort="<level>" "prompt")
        → Execute in background via Bash tool
          → Capture output to /tmp/codex_output.md
            → Summarize result to user
```

- Tasks run via `codex exec` in non-interactive mode
- Code reviews use `codex review` or `codex exec review`
- Output is captured and summarized — use `codex resume` to continue a session
- Runs in background by default so Claude Code remains responsive

## Usage

The codex skill activates automatically when you use these keywords in Claude Code:

- `codex review` — Run code review
- `codex analyze` — Analyze code
- `codex config` — Change default settings (model, reasoning level, sandbox mode, etc.)
- `codex resume` — Resume previous session
- `codex cloud` — Run cloud task

## Configuration

Use `codex config` to customize defaults:

| Setting | Default | Description |
|---------|---------|-------------|
| model | `gpt-5.3-codex` | Model to use |
| reasoning | `xhigh` | Reasoning effort (low/medium/high/xhigh) |
| sandbox | `read-only` | Sandbox mode |
| background | `true` | Run in background |
