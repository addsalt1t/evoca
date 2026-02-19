# CLAUDE.md Management Plugin

Tools to maintain and improve CLAUDE.md and rules files - audit quality, capture session learnings, and keep project memory current.

## What It Does

Two complementary tools for different purposes:

| | claude-md-improver (skill) | /revise-claude-md (command) |
|---|---|---|
| **Purpose** | Keep CLAUDE.md and rules aligned with codebase | Capture session learnings |
| **Triggered by** | Codebase changes | End of session |
| **Use when** | Periodic maintenance | Session revealed missing context |

## Usage

### Skill: claude-md-improver

Audits CLAUDE.md files against current codebase state:

```
"audit my CLAUDE.md files"
"check if my CLAUDE.md is up to date"
"review my project rules"
"audit rules and CLAUDE.md"
```

<img src="claude-md-improver-example.png" alt="CLAUDE.md improver showing quality scores and recommended updates" width="600">

### Command: /revise-claude-md

Captures learnings from the current session:

```
/revise-claude-md
```

<img src="revise-claude-md-example.png" alt="Revise command capturing session learnings into CLAUDE.md" width="600">

### What It Covers

| Memory Type | Location | Managed |
|-------------|----------|---------|
| Project memory | `./CLAUDE.md` or `./.claude/CLAUDE.md` | ✓ |
| Local overrides | `./.claude.local.md` | ✓ |
| Project rules | `./.claude/rules/*.md` | ✓ |
| User rules | `~/.claude/rules/*.md` | ✓ |
| Global defaults | `~/.claude/CLAUDE.md` | ✓ |
| Package-specific | `./packages/*/CLAUDE.md` | ✓ |

## Author

Isabella He (isabella@anthropic.com)
