---
name: memory-lint
description: Audit and improve CLAUDE.md, rules files, and auto memory in repositories. Use when user asks to check, audit, update, improve, or fix CLAUDE.md or rules files. Scans for all CLAUDE.md, .claude/rules/, and auto memory files, evaluates quality, outputs quality report, then makes targeted updates. Also use when the user mentions "CLAUDE.md maintenance", "project memory optimization", "memory lint", or "rules management".
tools: Read, Glob, Grep, Bash, Edit
---

# Memory Lint

Audit, evaluate, and improve CLAUDE.md files and auto memory across a codebase to ensure Claude Code has optimal — and minimal — project context.

**This skill can write to CLAUDE.md files.** After presenting a quality report and getting user approval, it updates CLAUDE.md files with targeted improvements.

**Philosophy: Less is more.** LLM-generated verbose context reduces agent performance and increases costs. Only minimal, human-written, non-redundant content improves outcomes.

## Workflow

### Phase 1: Discovery

Find all CLAUDE.md files and auto memory in the repository:

```bash
find . -name "CLAUDE.md" -o -name "CLAUDE.local.md" 2>/dev/null | head -50
find . -path "*/.claude/rules/*.md" 2>/dev/null | head -50
```

**File Types & Locations:**

| Type | Location | Purpose |
|------|----------|---------|
| Project root | `./CLAUDE.md` or `./.claude/CLAUDE.md` | Primary project context (checked into git, shared with team) |
| Local overrides | `./CLAUDE.local.md` | Personal/local settings (auto-gitignored, not shared) |
| Global defaults | `~/.claude/CLAUDE.md` | User-wide defaults across all projects |
| Managed policy (macOS) | `/Library/Application Support/ClaudeCode/CLAUDE.md` | Organization-wide defaults (IT-managed) |
| Managed policy (Linux) | `/etc/claude-code/CLAUDE.md` | Organization-wide defaults (IT-managed) |
| Managed policy (Windows) | `%PROGRAMDATA%\ClaudeCode\CLAUDE.md` | Organization-wide defaults (IT-managed) |
| Package-specific | `./packages/*/CLAUDE.md` | Module-level context in monorepos |
| Subdirectory | Any nested location | Feature/domain-specific context |
| Project rules | `./.claude/rules/*.md` | Modular, topic-specific project instructions (auto-loaded, shared with team) |
| User rules | `~/.claude/rules/*.md` | Personal rules across all projects (auto-loaded) |
| Auto memory index | `~/.claude/projects/*/MEMORY.md` | Claude's self-maintained project memory (first 200 lines auto-loaded) |
| Auto memory topics | `~/.claude/projects/*/memory/*.md` | Detailed topic files linked from MEMORY.md |

**Note:** Claude auto-discovers CLAUDE.md files in parent directories, making monorepo setups work automatically.

**`@` Import Syntax:** CLAUDE.md files can reference other files with `@path/to/file` syntax (e.g., `@README.md`, `@docs/setup.md`). When auditing, trace all `@` imports to evaluate the full instruction set. Imports resolve relative to the containing file and support up to 5 hops deep. The first time an `@` import is encountered, the user sees a one-time approval dialog — after approval, that import is trusted permanently.

**`/init` Command:** Running `/init` in a new project bootstraps a `CLAUDE.md` with project-specific context. Recommend this for projects that don't have a CLAUDE.md yet.

**`--add-dir` Flag:** Claude Code can be launched with `--add-dir /path/to/other` to include CLAUDE.md files from additional directories. The env var `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD` provides the same functionality.

**Worktree Sharing:** For shared context across projects, use `@~/.claude/shared-context.md` imports to reference a common file.

**Auto Memory (`~/.claude/projects/`):** This is Claude's self-maintained memory, separate from CLAUDE.md. Key characteristics:
- MEMORY.md index: first 200 lines are auto-loaded into every conversation — lines beyond 200 are **truncated and lost**
- Topic files in `memory/` subdirectory for detailed notes linked from MEMORY.md
- Managed by Claude itself — do NOT modify auto memory files during audits
- Can be disabled per-project with env var `CLAUDE_CODE_DISABLE_AUTO_MEMORY=1`
- Health check: verify MEMORY.md is under 200 lines and used as an index (not a dump)

### Phase 2: Quality Assessment

For each CLAUDE.md file, evaluate against quality criteria. See [references/quality-criteria.md](references/quality-criteria.md) for detailed rubrics.

**Quick Assessment Checklist:**

| Criterion | Weight | Check |
|-----------|--------|-------|
| Commands/workflows documented | 18 | Are build/test/deploy commands present? |
| Critical architecture (non-discoverable) | 8 | Only hidden deps, init order, magic conventions? |
| Non-obvious patterns | 18 | Are gotchas and quirks documented? |
| Conciseness | 12 | No verbose explanations or obvious info? |
| Currency | 12 | Does it reflect current codebase state? |
| Actionability | 12 | Are instructions executable, not vague? |
| Redundancy | 10 | No discoverable content, README paraphrases, or duplicates? |
| Content density | 10 | Every line changes behavior or prevents mistakes? |
| Rules organization (bonus) | +10 | Are rules modular and well-scoped? |

**Redundancy Audit** (run for each file):
1. Flag any directory trees or file listings — are these discoverable via `ls`/`Glob`?
2. Flag any content that paraphrases the README — should use `@README.md` instead
3. Flag any generic best practices — Claude already knows these
4. Flag any LLM-generated boilerplate — verbose, hedging language, "comprehensive" descriptions

**Quality Scores:**
- **A (90-100)**: Lean, high-signal, non-redundant, current
- **B (70-89)**: Good signal, minor redundancy or gaps
- **C (50-69)**: Useful content mixed with noise
- **D (30-49)**: Sparse, redundant, or outdated
- **F (0-29)**: Missing, severely outdated, or mostly boilerplate

### Phase 3: Quality Report Output

**ALWAYS output the quality report BEFORE making any updates.**

Format:

```
## Memory Lint Report

### Summary
- Files found: X
- Average score: X/100
- Files needing update: X

### File-by-File Assessment

#### 1. ./CLAUDE.md (Project Root)
**Score: XX/100 (Grade: X)**

| Criterion | Score | Notes |
|-----------|-------|-------|
| Commands/workflows | X/18 | ... |
| Critical architecture | X/8 | ... |
| Non-obvious patterns | X/18 | ... |
| Conciseness | X/12 | ... |
| Currency | X/12 | ... |
| Actionability | X/12 | ... |
| Redundancy | X/10 | ... |
| Content density | X/10 | ... |

**Issues:**
- [List specific problems]

**Recommended changes:**
- [Often removals, not just additions]

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

#### Auto Memory Health

| Check | Status | Notes |
|-------|--------|-------|
| MEMORY.md exists | ✓/✗ | Path: ~/.claude/projects/... |
| MEMORY.md under 200 lines | ✓/✗ | Current: X lines |
| Used as index (links to topic files) | ✓/✗ | ... |
| Topic files organized | ✓/✗ | X topic files found |
| CLAUDE_CODE_DISABLE_AUTO_MEMORY | Off/On | ... |

**Note:** Auto memory is read-only during audits. Use `/memory` to make changes.
```

### Phase 4: Targeted Updates

After outputting the quality report, ask user for confirmation before updating.

**Update Guidelines (Critical):**

1. **Keep it minimal** — Every addition must pass:
   - "Can an agent discover this with tools?" → If yes, don't add it
   - "Does this change agent behavior or prevent a mistake?" → If no, don't add it
   - "Is this the shortest way to express this?" → If no, shorten it

2. **Propose targeted additions only** — Focus on genuinely useful info:
   - Commands or workflows discovered during analysis
   - Gotchas or non-obvious patterns found in code
   - Package relationships that weren't clear
   - Testing approaches that work
   - Configuration quirks

3. **Propose removals too** — Flag content that should be removed:
   - Discoverable directory trees or file listings
   - Paraphrased README or docs content (replace with `@` import)
   - Generic best practices
   - Verbose explanations (compress to one-liners)

4. **Show diffs** — For each change, show:
   - Which CLAUDE.md file to update
   - The specific addition or removal (as a diff or quoted block)
   - Brief explanation of why this helps future sessions

5. **Placement guidance** — Where does each piece of context belong?

   | Content Type | Best Location |
   |-------------|---------------|
   | Team-shared project context | `CLAUDE.md` |
   | Critical architecture (non-discoverable) | `CLAUDE.md` |
   | Topic-specific coding standards | `.claude/rules/<topic>.md` |
   | Path-scoped conventions | `.claude/rules/<topic>.md` with `paths` frontmatter |
   | Personal preferences | `CLAUDE.local.md` |
   | Cross-project personal defaults | `~/.claude/CLAUDE.md` |
   | Stable patterns from sessions | Auto memory (`~/.claude/projects/`) |
   | Personal session learnings | Auto memory via `/memory` |
   | Shared context across repos | `@~/.claude/shared-context.md` import |

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
| Critical architecture (non-discoverable only) | ✓ | |
| Build/test/deploy commands | ✓ | |
| Topic-specific coding standards | | ✓ |
| Path-scoped conventions | | ✓ |
| Cross-cutting gotchas | ✓ | |
| Language/framework guidelines | | ✓ |
| Domain knowledge for occasional use | | Skills (`.claude/skills/`) |
| Personal preferences (not shared) | `CLAUDE.local.md` | |
| Stable patterns from sessions | Auto memory | |

### Rules Best Practices

- **One topic per file**: Each rule file covers a single concern (e.g., `testing.md`, `api-design.md`)
- **Descriptive filenames**: The filename should indicate what the rules cover
- **Use path filters sparingly**: Only add `paths` frontmatter when rules truly apply to specific file types
- **Organize with subdirectories**: Group related rules (e.g., `frontend/`, `backend/`)
- **Keep rules focused**: Broad guidelines go in CLAUDE.md, specific conventions go in rules
- **Consider skills for domain knowledge**: CLAUDE.md loads every session — if content is only relevant sometimes, recommend extracting it into a skill (`.claude/skills/<name>/SKILL.md`) instead

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
10. **Missing emphasis markers**: Critical rules without `IMPORTANT` or `YOU MUST` that need stronger adherence
11. **Content better as skills**: Domain knowledge or workflows in CLAUDE.md that are only relevant sometimes — should be extracted into `.claude/skills/`
12. **Untraced `@` imports**: CLAUDE.md references external files via `@` syntax that may be broken or circular
13. **Redundant discoverable content**: Directory trees, file listings, or structure descriptions that `ls`/`Glob` can reveal — remove and let the agent discover
14. **MEMORY.md over 200 lines**: Auto memory index exceeds the auto-load limit — lines beyond 200 are silently truncated
15. **Missing `/init` bootstrap**: Project has no CLAUDE.md — recommend running `/init` to generate one
16. **Duplicated README content**: CLAUDE.md paraphrases README instead of using `@README.md` import

## User Tips to Share

When presenting recommendations, remind users:

- **Less is more**: Minimal, human-written context outperforms verbose LLM-generated files. A 50-line CLAUDE.md beats a 300-line "comprehensive" one
- **Use `@` imports aggressively**: Instead of paraphrasing docs, use `@README.md`, `@docs/setup.md` — keeps CLAUDE.md lean while linking to full context
- **Auto memory 200-line limit**: MEMORY.md only auto-loads the first 200 lines — keep it as a concise index linking to topic files in `memory/`
- **`/init` for new projects**: Run `/init` in any project without a CLAUDE.md to bootstrap one with project-specific context
- **`--add-dir` for shared context**: Launch Claude with `--add-dir /path/to/shared` to include CLAUDE.md from other directories (or use `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD` env var)
- **Worktree sharing**: Use `@~/.claude/shared-context.md` to share context across multiple projects
- **`#` key shortcut**: During a Claude session, press `#` to have Claude auto-incorporate learnings into CLAUDE.md
- **Keep it concise**: CLAUDE.md should stay under 300 lines; dense is better than verbose
- **Actionable commands**: All documented commands should be copy-paste ready
- **Use `CLAUDE.local.md`**: For personal preferences not shared with team (auto-gitignored)
- **Global defaults**: Put user-wide preferences in `~/.claude/CLAUDE.md`
- **Use `/memory`**: Run `/memory` during a session to inspect which memory files are currently loaded
- **Use `.claude/rules/`**: For modular, topic-specific instructions that are easier to maintain than a monolithic CLAUDE.md
- **Path-specific rules**: Scope rules to relevant files with YAML frontmatter `paths` field
- **Symlink shared rules**: Use symlinks to share common rules across projects

## What Makes a Great CLAUDE.md

**Key principles:**
- Concise and human-readable
- Actionable commands that can be copy-pasted
- Project-specific patterns, not generic advice
- Non-obvious gotchas and warnings
- Non-redundant — nothing an agent can discover via tools
- Minimal — every line changes behavior or prevents a mistake

**Recommended sections** (use only what's relevant):
- Commands (build, test, dev, lint)
- Critical Architecture (non-discoverable only — hidden deps, init order, magic conventions)
- Key Files (non-obvious entry points and config only)
- Code Style (project conventions)
- Environment (required vars, setup)
- Testing (commands, patterns)
- Gotchas (quirks, common mistakes)
- Workflow (when to do what)
