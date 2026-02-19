---
name: claude-md-improver
description: Audit and improve CLAUDE.md and rules files in repositories. Use when user asks to check, audit, update, improve, or fix CLAUDE.md or rules files. Scans for all CLAUDE.md and .claude/rules/ files, evaluates quality against templates, outputs quality report, then makes targeted updates. Also use when the user mentions "CLAUDE.md maintenance", "project memory optimization", or "rules management".
tools: Read, Glob, Grep, Bash, Edit
---

# CLAUDE.md Improver

Audit, evaluate, and improve CLAUDE.md files across a codebase to ensure Claude Code has optimal project context.

**This skill can write to CLAUDE.md files.** After presenting a quality report and getting user approval, it updates CLAUDE.md files with targeted improvements.

## Workflow

### Phase 1: Discovery

Find all CLAUDE.md files in the repository:

```bash
find . -name "CLAUDE.md" -o -name ".claude.md" -o -name ".claude.local.md" 2>/dev/null | head -50
find . -path "*/.claude/rules/*.md" 2>/dev/null | head -50
```

**File Types & Locations:**

| Type | Location | Purpose |
|------|----------|---------|
| Project root | `./CLAUDE.md` | Primary project context (checked into git, shared with team) |
| Local overrides | `./.claude.local.md` | Personal/local settings (gitignored, not shared) |
| Global defaults | `~/.claude/CLAUDE.md` | User-wide defaults across all projects |
| Package-specific | `./packages/*/CLAUDE.md` | Module-level context in monorepos |
| Subdirectory | Any nested location | Feature/domain-specific context |
| Project rules | `./.claude/rules/*.md` | Modular, topic-specific project instructions (auto-loaded, shared with team) |
| User rules | `~/.claude/rules/*.md` | Personal rules across all projects (auto-loaded) |

**Note:** Claude auto-discovers CLAUDE.md files in parent directories, making monorepo setups work automatically.

### Phase 2: Quality Assessment

For each CLAUDE.md file, evaluate against quality criteria. See [references/quality-criteria.md](references/quality-criteria.md) for detailed rubrics.

**Quick Assessment Checklist:**

| Criterion | Weight | Check |
|-----------|--------|-------|
| Commands/workflows documented | High | Are build/test/deploy commands present? |
| Architecture clarity | High | Can Claude understand the codebase structure? |
| Non-obvious patterns | Medium | Are gotchas and quirks documented? |
| Conciseness | Medium | No verbose explanations or obvious info? |
| Currency | High | Does it reflect current codebase state? |
| Actionability | High | Are instructions executable, not vague? |
| Rules organization | Medium | Are rules modular and well-scoped? |

**Quality Scores:**
- **A (90-100)**: Comprehensive, current, actionable
- **B (70-89)**: Good coverage, minor gaps
- **C (50-69)**: Basic info, missing key sections
- **D (30-49)**: Sparse or outdated
- **F (0-29)**: Missing or severely outdated

### Phase 3: Quality Report Output

**ALWAYS output the quality report BEFORE making any updates.**

Format:

```
## CLAUDE.md Quality Report

### Summary
- Files found: X
- Average score: X/100
- Files needing update: X

### File-by-File Assessment

#### 1. ./CLAUDE.md (Project Root)
**Score: XX/100 (Grade: X)**

| Criterion | Score | Notes |
|-----------|-------|-------|
| Commands/workflows | X/20 | ... |
| Architecture clarity | X/20 | ... |
| Non-obvious patterns | X/15 | ... |
| Conciseness | X/15 | ... |
| Currency | X/15 | ... |
| Actionability | X/15 | ... |

**Issues:**
- [List specific problems]

**Recommended additions:**
- [List what should be added]

#### 2. ./packages/api/CLAUDE.md (Package-specific)
...

#### Rules Assessment (.claude/rules/)

| File | Scope | Has path filter | Notes |
|------|-------|-----------------|-------|
| code-style.md | Global | No | ... |
| api-design.md | src/api/**/*.ts | Yes | ... |

**Issues:**
- [List specific problems with rules organization]

**Recommendations:**
- [Suggest rules to create, split, or merge]
```

### Phase 4: Targeted Updates

After outputting the quality report, ask user for confirmation before updating.

**Update Guidelines (Critical):**

1. **Propose targeted additions only** - Focus on genuinely useful info:
   - Commands or workflows discovered during analysis
   - Gotchas or non-obvious patterns found in code
   - Package relationships that weren't clear
   - Testing approaches that work
   - Configuration quirks

2. **Keep it minimal** - Avoid:
   - Restating what's obvious from the code
   - Generic best practices already covered
   - One-off fixes unlikely to recur
   - Verbose explanations when a one-liner suffices

3. **Show diffs** - For each change, show:
   - Which CLAUDE.md file to update
   - The specific addition (as a diff or quoted block)
   - Brief explanation of why this helps future sessions

**Diff Format:**

```markdown
### Update: ./CLAUDE.md

**Why:** Build command was missing, causing confusion about how to run the project.

```diff
+ ## Quick Start
+
+ ```bash
+ npm install
+ npm run dev  # Start development server on port 3000
+ ```
```
```

### Phase 5: Apply Updates

After user approval, apply changes using the Edit tool. Preserve existing content structure.

## Rules Management

### When to Use Rules vs CLAUDE.md

| Use Case | CLAUDE.md | Rules |
|----------|-----------|-------|
| Project overview, architecture | ✓ | |
| Build/test/deploy commands | ✓ | |
| Topic-specific coding standards | | ✓ |
| Path-scoped conventions | | ✓ |
| Cross-cutting gotchas | ✓ | |
| Language/framework guidelines | | ✓ |

### Rules Best Practices

- **One topic per file**: Each rule file covers a single concern (e.g., `testing.md`, `api-design.md`)
- **Descriptive filenames**: The filename should indicate what the rules cover
- **Use path filters sparingly**: Only add `paths` frontmatter when rules truly apply to specific file types
- **Organize with subdirectories**: Group related rules (e.g., `frontend/`, `backend/`)
- **Keep rules focused**: Broad guidelines go in CLAUDE.md, specific conventions go in rules

### Path-Specific Rules

Rules can target specific files using YAML frontmatter:

```yaml
---
paths:
  - "src/api/**/*.ts"
---
```

Supported glob patterns:

| Pattern | Matches |
|---------|---------|
| `**/*.ts` | All TypeScript files in any directory |
| `src/**/*` | All files under src/ |
| `*.md` | Markdown files in project root |
| `src/**/*.{ts,tsx}` | TypeScript and TSX files under src/ |

### Rules Organization Patterns

```
.claude/rules/
├── frontend/
│   ├── react.md         # React component conventions
│   └── styles.md        # CSS/styling rules
├── backend/
│   ├── api.md           # API endpoint standards
│   └── database.md      # Database query patterns
├── code-style.md        # General code style
├── testing.md           # Testing conventions
└── security.md          # Security requirements
```

## Templates

See [references/templates.md](references/templates.md) for CLAUDE.md templates by project type.

## Common Issues to Flag

1. **Stale commands**: Build commands that no longer work
2. **Missing dependencies**: Required tools not mentioned
3. **Outdated architecture**: File structure that's changed
4. **Missing environment setup**: Required env vars or config
5. **Broken test commands**: Test scripts that have changed
6. **Undocumented gotchas**: Non-obvious patterns not captured
7. **Rules bloat**: Too many unfocused rules files diluting context
8. **Missing path scoping**: Rules that should be scoped to specific paths but aren't
9. **CLAUDE.md / rules overlap**: Duplicate instructions between CLAUDE.md and rules files

## User Tips to Share

When presenting recommendations, remind users:

- **`#` key shortcut**: During a Claude session, press `#` to have Claude auto-incorporate learnings into CLAUDE.md
- **Keep it concise**: CLAUDE.md should be human-readable; dense is better than verbose
- **Actionable commands**: All documented commands should be copy-paste ready
- **Use `.claude.local.md`**: For personal preferences not shared with team (add to `.gitignore`)
- **Global defaults**: Put user-wide preferences in `~/.claude/CLAUDE.md`
- **Use `.claude/rules/`**: For modular, topic-specific instructions that are easier to maintain than a monolithic CLAUDE.md
- **Path-specific rules**: Scope rules to relevant files with YAML frontmatter `paths` field
- **Symlink shared rules**: Use symlinks to share common rules across projects

## What Makes a Great CLAUDE.md

**Key principles:**
- Concise and human-readable
- Actionable commands that can be copy-pasted
- Project-specific patterns, not generic advice
- Non-obvious gotchas and warnings

**Recommended sections** (use only what's relevant):
- Commands (build, test, dev, lint)
- Architecture (directory structure)
- Key Files (entry points, config)
- Code Style (project conventions)
- Environment (required vars, setup)
- Testing (commands, patterns)
- Gotchas (quirks, common mistakes)
- Workflow (when to do what)
