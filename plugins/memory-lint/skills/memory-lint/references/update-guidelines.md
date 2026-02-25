# CLAUDE.md Update Guidelines

## Core Principle

Only add information that will genuinely help future Claude sessions. The context window is precious — every line must earn its place.

## What TO Add

### 1. Commands/Workflows Discovered

```markdown
## Build

`npm run build:prod` - Full production build with optimization
`npm run build:dev` - Fast dev build (no minification)
```

Why: Saves future sessions from discovering these again.

### 2. Gotchas and Non-Obvious Patterns

```markdown
## Gotchas

- Tests must run sequentially (`--runInBand`) due to shared DB state
- `yarn.lock` is authoritative; delete `node_modules` if deps mismatch
```

Why: Prevents repeating debugging sessions.

### 3. Package Relationships

```markdown
## Dependencies

The `auth` module depends on `crypto` being initialized first.
Import order matters in `src/bootstrap.ts`.
```

Why: Architecture knowledge that isn't obvious from code.

### 4. Testing Approaches That Worked

```markdown
## Testing

For API endpoints: Use `supertest` with the test helper in `tests/setup.ts`
Mocking: Factory functions in `tests/factories/` (not inline mocks)
```

Why: Establishes patterns that work.

### 5. Configuration Quirks

```markdown
## Config

- `NEXT_PUBLIC_*` vars must be set at build time, not runtime
- Redis connection requires `?family=0` suffix for IPv6
```

Why: Environment-specific knowledge.

## What NOT to Add

### 1. Obvious Code Info

Bad:
```markdown
The `UserService` class handles user operations.
```

The class name already tells us this.

### 2. Generic Best Practices

Bad:
```markdown
Always write tests for new features.
Use meaningful variable names.
```

This is universal advice, not project-specific.

### 3. One-Off Fixes

Bad:
```markdown
We fixed a bug in commit abc123 where the login button didn't work.
```

Won't recur; clutters the file.

### 4. Verbose Explanations

Bad:
```markdown
The authentication system uses JWT tokens. JWT (JSON Web Tokens) are
an open standard (RFC 7519) that defines a compact and self-contained
way for securely transmitting information between parties as a JSON
object. In our implementation, we use the HS256 algorithm which...
```

Good:
```markdown
Auth: JWT with HS256, tokens in `Authorization: Bearer <token>` header.
```

### 5. Frequently Changing Information

Bad:
```markdown
Current API version: v2.3.1
Latest release date: 2024-01-15
```

Information that changes often becomes stale quickly, undermining trust in the file.

### 6. File-by-File Descriptions

Bad:
```markdown
`src/utils/format.ts` - Formatting utilities
`src/utils/validate.ts` - Validation helpers
`src/utils/parse.ts` - Parsing functions
```

Claude can discover this by reading the code. Only document non-obvious relationships.

### 7. Detailed API Documentation

Bad:
```markdown
The `/api/users` endpoint accepts GET, POST, PUT, DELETE...
```

Good:
```markdown
API docs: @docs/api-reference.md
```

Link to docs instead of duplicating them in CLAUDE.md.

### 8. Content Agents Can Discover

Bad:
```markdown
## Architecture

```
src/
  components/    # React components
  utils/         # Utility functions
  services/      # API service layer
  hooks/         # Custom React hooks
```
```

Claude can run `ls src/` and `Glob` to discover this. Directory trees and file listings are the most common form of redundancy.

Good:
```markdown
## Architecture

- `src/services/` calls external APIs through `src/lib/api-client.ts` (not direct fetch) — this is enforced by lint rule
- Component naming: `Feature.container.tsx` wraps `Feature.view.tsx` (MVC-like split)
```

Only document what tools can't reveal: hidden conventions, non-obvious relationships, and "why" decisions.

### 9. Paraphrased Documentation

Bad:
```markdown
## Getting Started

This project uses Next.js 14 with the App Router. Install dependencies
with `npm install` and start the dev server with `npm run dev`.
See the README for more details.
```

Good:
```markdown
@README.md
```

Use `@` imports to reference existing documentation. Don't paraphrase — it creates a maintenance burden and inevitably drifts from the source.

## Redundancy Elimination

Before adding any content to CLAUDE.md, apply the **4-question redundancy test**:

| Question | If Yes | Example |
|----------|--------|---------|
| 1. Can an agent discover this with `ls`, `Glob`, `Grep`, or `Read`? | Don't add | Directory trees, file listings, export lists |
| 2. Does this duplicate existing docs that could be `@`-imported? | Use `@` import | README paraphrases, API docs summaries |
| 3. Is this generic advice Claude already knows? | Don't add | "Write tests", "Use meaningful names", "Follow DRY" |
| 4. Does this change agent behavior or prevent a specific mistake? | Add it | Gotchas, non-obvious deps, magic conventions |

**The "50-line briefing" test:** If you had to brief a senior developer in 50 lines, would this content make the cut? If not, it's probably noise.

## Auto Memory Guidelines

### What Goes Where

| Content | Location | Why |
|---------|----------|-----|
| Team project context | `CLAUDE.md` | Shared via git, loaded every session |
| Critical architecture (non-discoverable) | `CLAUDE.md` | Team needs to know |
| Topic-specific standards | `.claude/rules/<topic>.md` | Modular, path-scopable |
| Personal preferences | `CLAUDE.local.md` | Not shared, auto-gitignored |
| Personal cross-project defaults | `~/.claude/CLAUDE.md` | Applied everywhere |
| Stable patterns from sessions | Auto memory (MEMORY.md) | Claude-managed, persists across sessions |
| Debugging insights | Auto memory (`memory/debugging.md`) | Personal reference, topic-specific |
| User workflow preferences | Auto memory or `CLAUDE.local.md` | Personal, not team-relevant |

### MEMORY.md Health Rules

- **Under 200 lines**: Only the first 200 lines of MEMORY.md are auto-loaded — anything beyond is silently lost
- **Index format**: MEMORY.md should be a concise index linking to topic files, not a content dump
- **Topic files**: Detailed notes belong in `~/.claude/projects/*/memory/<topic>.md`
- **No duplication**: If it's in CLAUDE.md, it doesn't need to be in auto memory (and vice versa)
- **Regular pruning**: Outdated or wrong memories should be removed

### When to Recommend Auto Memory

Suggest auto memory (via `/memory`) when:
- The user discovers a personal workflow preference
- A debugging insight is valuable but not team-relevant
- Session learnings are stable patterns, not one-off fixes
- The user's CLAUDE.md is getting too long with personal notes

## Rules-Specific Guidelines

### When to Suggest Creating a Rule File

Suggest a rule file when:
- A CLAUDE.md section grows beyond 10-15 lines on a single topic
- Instructions only apply to specific file types or directories
- Multiple related conventions form a coherent theme
- Team wants modular, reviewable instruction sets

### What Makes a Good Rule File

1. **Focused scope**: One topic per file (e.g., `testing.md`, not `testing-and-deployment.md`)
2. **Descriptive filename**: Name indicates content at a glance
3. **Path filters when applicable**: Use YAML frontmatter `paths` for file-type-specific rules
4. **No overlap**: Don't repeat what's already in CLAUDE.md

### Rules to NEVER Create

- Generic advice (already known to Claude)
- Critical architecture (belongs in CLAUDE.md — non-discoverable aspects only)
- Temporary or session-specific instructions
- Rules duplicating content from CLAUDE.md

## Diff Format for Updates

For each suggested change:

### 1. Identify the File

```
File: ./CLAUDE.md
Section: Commands (new section after ## Architecture)
```

### 2. Show the Change

```diff
 ## Architecture
 ...

+## Commands
+
+| Command | Purpose |
+|---------|---------|
+| `npm run dev` | Dev server with HMR |
+| `npm run build` | Production build |
+| `npm test` | Run test suite |
```

### 3. Explain Why

> **Why this helps:** The build commands weren't documented, causing
> confusion about how to run the project. This saves future sessions
> from needing to inspect `package.json`.

## Validation Checklist

Before finalizing an update, verify:

- [ ] Each addition is project-specific
- [ ] No generic advice or obvious info
- [ ] Commands are tested and work
- [ ] File paths are accurate
- [ ] Would a new Claude session find this helpful?
- [ ] Is this the most concise way to express the info?
- [ ] Rules don't overlap with CLAUDE.md content
- [ ] Path-scoped rules have correct glob patterns
- [ ] Rule filenames are descriptive and indicate scope
- [ ] No discoverable content (directory trees, file listings, export lists)
- [ ] No paraphrased docs (use `@` imports instead)
- [ ] Passes the "50-line briefing" test
- [ ] Auto memory guidance given for personal learnings
