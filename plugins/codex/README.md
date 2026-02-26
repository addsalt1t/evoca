# Codex

Exec Codex CLI from Claude Code.

## Prerequisites

```bash
npm install -g @openai/codex
codex login                          # or set CODEX_API_KEY
```

## Installation

```bash
claude /install-marketplace github:addsalt1t/evoca
claude /install codex
```

## Examples

```
You: codex review
→ Codex reviews your staged changes and returns a summary

You: codex analyze this race condition — I want a second opinion.
→ Codex examines the code independently and returns its diagnosis

You: /codex-review --base main
→ Codex reviews current branch against main
```

## Usage

```
codex review           # Code review
codex analyze          # Analyze code
codex cloud            # Cloud task
codex config           # Change settings
codex resume           # Resume previous session
/codex-review          # Native code review command
```

## Settings

Use `codex config` to customize:

| Setting | Default | Options |
|---------|---------|---------|
| model | `gpt-5.3-codex` | Any Codex-supported model |
| sub_model | `gpt-5.3-codex-spark` | Fast model for quick tasks |
| reasoning | `xhigh` | low / medium / high / xhigh |
| sandbox | `read-only` | read-only / full-auto |
| background | `true` | true / false |

## How It Works

This plugin registers a **skill** in Claude Code. When you mention a trigger keyword, the skill:

1. Reads your configured defaults
2. Assembles the CLI command (`codex exec`)
3. Runs it in the background
4. Captures the output
5. Returns a summary to your conversation

Use `codex resume` to continue where a previous session left off.
