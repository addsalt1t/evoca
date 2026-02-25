---
description: Update CLAUDE.md with learnings from this session
allowed-tools: Read, Edit, Glob
---

Review this session for learnings about working with Claude Code in this codebase. Update CLAUDE.md with context that would help future Claude sessions be more effective.

## Step 1: Reflect

What context was missing that would have helped Claude work more effectively?
- Bash commands that were used or discovered
- Code style patterns followed
- Testing approaches that worked
- Environment/configuration quirks
- Warnings or gotchas encountered
- Rules that should be created for recurring topic-specific conventions

**For each learning, ask: "Is this team-relevant or personal?"**
- Team-relevant → CLAUDE.md or `.claude/rules/`
- Personal preference → CLAUDE.local.md or auto memory (`/memory`)

## Step 2: Find CLAUDE.md and Rules Files

```bash
find . -name "CLAUDE.md" -o -name "CLAUDE.local.md" -o -path "*/.claude/rules/*.md" 2>/dev/null | head -20
```

Decide where each addition belongs:

| Content Type | Location | Shared? |
|-------------|----------|---------|
| Team project context | `CLAUDE.md` | Yes (git) |
| Topic-specific standards | `.claude/rules/<topic>.md` | Yes (git) |
| Personal/local preferences | `CLAUDE.local.md` | No (auto-gitignored) |
| Personal cross-project defaults | `~/.claude/CLAUDE.md` | No (user-level) |
| Stable personal patterns | Auto memory via `/memory` | No (Claude-managed) |

## Step 3: Draft Additions

**Keep it concise** — one line per concept. CLAUDE.md is part of the prompt, so brevity matters.

Format: `<command or pattern>` - `<brief description>`

**Before adding anything, apply the redundancy test:**
1. Can an agent discover this with tools (`ls`, `Glob`, `Grep`, `Read`)? → Don't add
2. Does this duplicate existing docs that could be `@`-imported? → Use `@` import
3. Does this change agent behavior or prevent a specific mistake? → Add it

Avoid:
- Verbose explanations
- Obvious information
- One-off fixes unlikely to recur
- Discoverable content (directory trees, file listings)
- Paraphrased documentation (use `@` imports instead)

## Step 4: Show Proposed Changes

For each addition:

```
### Update: ./CLAUDE.md

**Why:** [one-line reason]

\`\`\`diff
+ [the addition - keep it brief]
\`\`\`
```

```
### New Rule: .claude/rules/testing.md

**Why:** [one-line reason]

\`\`\`markdown
# Testing Conventions

- <convention discovered during session>
\`\`\`
```

**For personal learnings**, recommend auto memory instead:

```
### Suggestion: Save to auto memory

This learning seems personal rather than team-relevant. Consider running
`/memory` to save it to your auto memory instead of CLAUDE.md.

**Learning:** [description]
```

## Step 5: Apply with Approval

Ask if the user wants to apply the changes. Only edit files they approve.
