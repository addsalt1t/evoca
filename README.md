# Evoca

A Claude Code plugin marketplace.

## What's Inside

| Plugin | What it does |
|--------|-------------|
| **codex** | Run OpenAI Codex CLI — code review, analysis, cloud tasks |
| **gemini** | Run Google Gemini CLI — code analysis, multi-model routing |
| **claude-md-management** | Audit and improve CLAUDE.md and `.claude/rules/` files — quality scoring, session learnings capture |

## Installation

### 1. Install the CLIs

```bash
# Codex
npm install -g @openai/codex
codex login                          # or set CODEX_API_KEY

# Gemini
npm install -g @google/gemini-cli
gemini                               # first run triggers OAuth (or set GEMINI_API_KEY)
```

### 2. Install the plugins

```bash
claude /install-marketplace github:addsalt1t/evoca
claude /install codex
claude /install gemini
```

## Examples

Ask Claude Code naturally — the skill activates automatically.

**Code review with Codex:**
```
You: codex review
→ Codex reviews your staged changes and returns a summary
```

**Analyze a project with Gemini:**
```
You: gemini analyze the authentication module
→ Gemini analyzes the code and returns findings
```

**Cross-validate with a second opinion:**
```
You: I can't figure out why this memory leak happens. Ask gemini for advice.
→ Gemini analyzes the issue from a different angle and suggests approaches

You: codex analyze this race condition — I want a second opinion.
→ Codex examines the code independently and returns its diagnosis
```

**Change settings:**
```
You: codex config
→ Interactive prompt to change model, reasoning level, etc.
```

## Usage

### Codex

```
codex review           # Code review
codex analyze          # Analyze code
codex cloud            # Cloud task
codex config           # Change settings
codex resume           # Resume previous session
```

Settings (`codex config`):

| Setting | Default | Options |
|---------|---------|---------|
| model | `gpt-5.3-codex` | Any Codex-supported model |
| reasoning | `xhigh` | low / medium / high / xhigh |
| sandbox | `read-only` | read-only / full-auto |
| background | `true` | true / false |

### Gemini

```
run gemini             # Run a task
gemini analyze         # Analyze code
gemini config          # Change settings
gemini resume          # Resume previous session
```

Settings (`gemini config`):

| Setting | Default | Options |
|---------|---------|---------|
| model | `auto` | auto / pro / flash / flash-lite |
| approval_mode | `plan` | plan / auto_edit / yolo |
| background | `true` | true / false |

## How It Works

Each plugin registers a **skill** in Claude Code. When you mention a trigger keyword (e.g. `codex review`, `run gemini`), the skill:

1. Reads your configured defaults
2. Assembles the CLI command
3. Runs it in the background
4. Captures the output
5. Returns a summary to your conversation

Use `resume` to continue where a previous session left off.
