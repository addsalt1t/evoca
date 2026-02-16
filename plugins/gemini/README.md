# Gemini

Google Gemini CLI integration for Claude Code.

## Prerequisites

```bash
npm install -g @google/gemini-cli
```

Google authentication required:

```bash
gemini                         # First run triggers OAuth login
```

Or set the `GEMINI_API_KEY` environment variable.

## Installation

```bash
claude /install gemini
```

## How It Works

This plugin registers Gemini CLI as a skill inside Claude Code. Once installed, Claude Code can invoke it directly from your conversation — no need to switch terminals or manage separate sessions.

```
User prompt (e.g. "run gemini")
  → Skill activated
    → Read defaults.json (model, approval_mode, background)
      → Assemble CLI command (gemini -p "<prompt>" --approval-mode <mode> -m <model>)
        → Execute in background via Bash tool
          → Capture output to /tmp/gemini_output.md
            → Summarize result to user
```

- Tasks run via `gemini -p` in headless (non-interactive) mode
- Supports model routing: auto, pro, flash, flash-lite
- Output is captured and summarized — use `gemini resume` to continue a session
- Runs in background by default so Claude Code remains responsive

## Usage

The gemini skill activates automatically when you use these keywords in Claude Code:

- `run gemini` / `use gemini` — Run a task with Gemini
- `gemini analyze` — Analyze code with Gemini
- `gemini config` — Change default settings (model, approval mode, etc.)
- `gemini resume` — Resume previous session

## Configuration

Use `gemini config` to customize defaults:

| Setting | Default | Description |
|---------|---------|-------------|
| model | `auto` | Model to use (auto/pro/flash/flash-lite) |
| approval_mode | `plan` | Approval mode (plan/auto_edit/yolo) |
| background | `true` | Run in background |
