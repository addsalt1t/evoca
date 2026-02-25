# CLAUDE.md Templates

## Key Principles

- **Concise**: Dense, human-readable content; one line per concept when possible
- **Actionable**: Commands should be copy-paste ready
- **Project-specific**: Document patterns unique to this project, not generic advice
- **Current**: All info should reflect actual codebase state
- **Non-redundant**: Nothing an agent can discover via `ls`, `Glob`, `Grep`, or `Read`

---

## Recommended Sections

Use only the sections relevant to the project. Not all sections are needed.

### Commands

Document the essential commands for working with the project.

```markdown
## Commands

| Command | Description |
|---------|-------------|
| `<install command>` | Install dependencies |
| `<dev command>` | Start development server |
| `<build command>` | Production build |
| `<test command>` | Run tests |
| `<lint command>` | Lint/format code |
```

### Critical Architecture

Document only what tools cannot discover. Do NOT include directory trees or file listings.

```markdown
## Critical Architecture

- <hidden dependency or initialization order>
- <magic convention (naming patterns that trigger behavior)>
- <non-obvious module relationship>
- <build system quirk>
```

**Anti-pattern** (do NOT include):
```markdown
## Architecture

```
src/
  components/    # React components
  utils/         # Utility functions
```
```
This is discoverable via `ls` and adds no value.

### Key Files

List only non-obvious files that an agent wouldn't naturally find or whose purpose isn't clear from the name.

```markdown
## Key Files

- `<non-obvious path>` - <why it matters>
- `<config with hidden behavior>` - <what's non-obvious>
```

Do NOT list files whose purpose is clear from their name (e.g., `src/utils/format.ts - Formatting utilities`).

### Code Style

Document project-specific coding conventions.

```markdown
## Code Style

- <convention>
- <convention>
- <preference over alternative>
```

### Environment

Document required environment variables and setup.

```markdown
## Environment

Required:
- `<VAR_NAME>` - <purpose>
- `<VAR_NAME>` - <purpose>

Setup:
- <setup step>
```

### Testing

Document testing approach and commands.

```markdown
## Testing

- `<test command>` - <what it tests>
- <testing convention or pattern>
```

### Gotchas

Document non-obvious patterns, quirks, and warnings.

```markdown
## Gotchas

- <non-obvious thing that causes issues>
- <ordering dependency or prerequisite>
- <common mistake to avoid>
```

### Workflow

Document development workflow patterns.

```markdown
## Workflow

- <when to do X>
- <preferred approach for Y>
```

---

## Template: Project Root (Minimal)

The leanest effective CLAUDE.md — commands and gotchas only.

```markdown
# <Project Name>

<One-line description>

## Commands

| Command | Description |
|---------|-------------|
| `<command>` | <description> |

## Gotchas

- <gotcha>
```

---

## Template: Project Root (Comprehensive)

For projects with non-trivial architecture and conventions.

```markdown
# <Project Name>

<One-line description>

## Commands

| Command | Description |
|---------|-------------|
| `<command>` | <description> |

## Critical Architecture

- <hidden dependency or init order>
- <magic convention>
- <non-obvious module relationship>

## Key Files

- `<non-obvious path>` - <why it matters>

## Code Style

- <convention>

## Environment

- `<VAR>` - <purpose>

## Testing

- `<command>` - <scope>

## Gotchas

- <gotcha>
```

---

## Template: Package/Module

For packages within a monorepo or distinct modules.

```markdown
# <Package Name>

<Purpose of this package>

## Usage

```
<import/usage example>
```

## Key Exports

- `<export>` - <purpose>

## Dependencies

- `<dependency>` - <why needed>

## Notes

- <important note>
```

---

## Template: Monorepo Root

```markdown
# <Monorepo Name>

<Description>

## Packages

| Package | Description | Path |
|---------|-------------|------|
| `<name>` | <purpose> | `<path>` |

## Commands

| Command | Description |
|---------|-------------|
| `<command>` | <description> |

## Cross-Package Patterns

- <shared pattern>
- <generation/sync pattern>
```

---

## Anti-Patterns

Common patterns to avoid in CLAUDE.md files:

| Anti-Pattern | Why It's Bad | Fix |
|-------------|-------------|-----|
| Directory trees (`src/\n  components/\n  utils/`) | Discoverable via `ls`/`Glob` — adds noise, goes stale | Remove; document only non-discoverable relationships |
| File descriptions (`format.ts - Formatting utilities`) | Name already says this; discoverable via `Read` | Only list files whose purpose is non-obvious |
| README duplication (paraphrasing getting started guide) | Creates maintenance burden, drifts from source | Use `@README.md` import |
| Generic advice ("Write tests", "Use meaningful names") | Claude already knows this; wastes context window | Remove entirely |
| Verbose prose (paragraph-style explanations) | Low signal density; one-liners are more effective | Compress to `<pattern> — <reason>` format |
| LLM-generated boilerplate (hedging, "comprehensive" descriptions) | Verbose content dilutes critical instructions | Replace with human-written, specific content |

---

## MEMORY.md Structure

Reference template for auto memory index files (`~/.claude/projects/*/MEMORY.md`).

```markdown
# Project Memory

## Key Patterns
- <stable pattern confirmed across multiple sessions>
- <architectural decision and rationale>

## Important Paths
- `<non-obvious path>` - <why it matters>

## Debugging Insights
See [memory/debugging.md](memory/debugging.md) for detailed notes.

## User Preferences
See [memory/preferences.md](memory/preferences.md) for workflow preferences.
```

**Rules:**
- Keep under 200 lines (only first 200 are auto-loaded)
- Use as an index linking to topic files in `memory/` subdirectory
- Don't duplicate CLAUDE.md content
- Remove outdated or wrong entries promptly

---

## Rules File Templates

### Template: General Rule (Unconditional)

```markdown
# <Topic Name>

- <convention or standard>
- <convention or standard>
- <convention or standard>
```

### Template: Path-Scoped Rule

```markdown
---
paths:
  - "<glob pattern>"
  - "<glob pattern>"
---

# <Topic Name>

- <convention specific to these file paths>
- <convention specific to these file paths>
```

### Template: Rules Directory Structure

For larger projects, organize rules by domain:

```
.claude/rules/
├── frontend/
│   ├── react.md         # React component patterns
│   └── styles.md        # CSS/styling conventions
├── backend/
│   ├── api.md           # API endpoint standards
│   └── database.md      # Database query patterns
├── code-style.md        # General code style
├── testing.md           # Testing conventions
└── security.md          # Security requirements
```

### When to Create a Rule vs Add to CLAUDE.md

| Content Type | Where | Reason |
|-------------|-------|--------|
| Critical architecture (non-discoverable only) | CLAUDE.md | Broad context, always needed |
| Build/test/deploy commands | CLAUDE.md | Workflow info, always needed |
| Language-specific coding standards | Rules | Focused topic, may be path-scoped |
| Framework conventions (React, API) | Rules | Specific to certain file types |
| Cross-cutting gotchas | CLAUDE.md | Applies everywhere |
| Security requirements | Rules | Focused concern, easy to audit |

---

## Update Principles

When updating any CLAUDE.md:

1. **Be specific**: Use actual file paths, real commands from this project
2. **Be current**: Verify info against the actual codebase
3. **Be brief**: One line per concept when possible
4. **Be useful**: Would this help a new Claude session understand the project?
5. **Be non-redundant**: Don't add what tools can discover
