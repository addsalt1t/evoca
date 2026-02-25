# Memory Lint Plugin

Audit and improve CLAUDE.md files — eliminate redundancy, capture session learnings, and keep project memory lean.

## What It Does

Two complementary tools for different purposes:

| | memory-lint (skill) | /revise (command) |
|---|---|---|
| **Purpose** | Keep CLAUDE.md and rules lean and high-signal | Capture session learnings |
| **Triggered by** | Codebase changes, periodic maintenance | End of session |
| **Use when** | Audit quality, find redundancy | Session revealed missing context |

## Usage

### Skill: memory-lint

Audits CLAUDE.md files against quality criteria:

```
"audit my CLAUDE.md files"
"check if my CLAUDE.md is up to date"
"review my project rules"
"memory lint"
"check for redundant content in CLAUDE.md"
```

### Command: /revise

Captures learnings from the current session:

```
/revise
```

### What It Covers

| Memory Type | Location | Managed |
|-------------|----------|---------|
| Project memory | `./CLAUDE.md` or `./.claude/CLAUDE.md` | ✓ |
| Local overrides | `./CLAUDE.local.md` | ✓ |
| Project rules | `./.claude/rules/*.md` | ✓ |
| User rules | `~/.claude/rules/*.md` | ✓ |
| Global defaults | `~/.claude/CLAUDE.md` | ✓ |
| Package-specific | `./packages/*/CLAUDE.md` | ✓ |
| Auto memory health | `~/.claude/projects/*/MEMORY.md` | Read-only audit |
