# Gemini

Run Gemini CLI from Claude Code.

## Prerequisites

```bash
npm install -g @google/gemini-cli
gemini                               # first run triggers OAuth (or set GEMINI_API_KEY)
```

## Installation

```bash
claude /install-marketplace github:addsalt1t/evoca
claude /install gemini
```

## Examples

```
You: gemini analyze the authentication module
→ Gemini analyzes the code and returns findings

You: I can't figure out why this memory leak happens. Ask gemini for advice.
→ Gemini analyzes the issue from a different angle and suggests approaches
```

## Usage

```
run gemini             # Run a task
gemini analyze         # Analyze code
gemini config          # Change settings
gemini resume          # Resume previous session
```

## Settings

Use `gemini config` to customize:

| Setting | Default | Options |
|---------|---------|---------|
| model | `auto` | auto / pro / flash / flash-lite |
| approval_mode | `plan` | plan / auto_edit / yolo |
| background | `true` | true / false |

## How It Works

This plugin registers a **skill** in Claude Code. When you mention a trigger keyword, the skill:

1. Reads your configured defaults
2. Assembles the CLI command (`gemini -p`)
3. Runs it in the background
4. Captures the output
5. Returns a summary to your conversation

Use `gemini resume` to continue where a previous session left off.
