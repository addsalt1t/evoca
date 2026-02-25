# CLAUDE.md Quality Criteria

Scoring rubric for evaluating CLAUDE.md quality. This rubric prioritizes signal density over coverage.

## Scoring Rubric

### 1. Commands/Workflows (18 points)

**18 points**: All essential commands documented with context
- Build, test, lint, deploy commands present
- Development workflow clear
- Common operations documented

**13 points**: Most commands present, some missing context

**8 points**: Basic commands only, no workflow

**4 points**: Few commands, many missing

**0 points**: No commands documented

### 2. Critical Architecture (8 points)

Only non-discoverable architectural knowledge. Claude can run `ls`, `Glob`, and `Grep` — don't repeat what tools reveal.

**8 points**: Documents only what tools can't discover
- Hidden dependencies and initialization order
- Magic conventions (naming patterns that trigger behavior)
- Non-obvious module relationships
- Build system quirks

**6 points**: Mostly non-discoverable, minor redundancy

**4 points**: Mix of discoverable and non-discoverable

**2 points**: Mostly discoverable content (directory trees, file listings)

**0 points**: No architecture info, or entirely discoverable content restated

### 3. Non-Obvious Patterns (18 points)

The highest-value content type. Gotchas, quirks, and "why we do it this way" that prevent repeated debugging sessions.

**18 points**: Comprehensive gotchas and quirks captured
- Known issues documented
- Workarounds explained
- Edge cases noted
- "Why we do it this way" for unusual patterns
- Common mistakes to avoid

**13 points**: Good coverage, some patterns missing

**8 points**: Basic patterns documented

**4 points**: Minimal pattern documentation

**0 points**: No patterns or gotchas

### 4. Conciseness (12 points)

**12 points**: Dense, valuable content
- Under 300 lines (recommended max for reliable instruction following)
- No filler or obvious info
- Each line adds value
- No redundancy with code comments

**8 points**: Mostly concise, some padding

**4 points**: Verbose in places

**0 points**: Mostly filler or restates obvious code

### 5. Currency (12 points)

**12 points**: Reflects current codebase
- Commands work as documented
- File references accurate
- Tech stack current

**8 points**: Mostly current, minor staleness

**4 points**: Several outdated references

**0 points**: Severely outdated

### 6. Actionability (12 points)

**12 points**: Instructions are executable
- Commands can be copy-pasted
- Steps are concrete
- Paths are real
- Critical rules use emphasis markers (`IMPORTANT`, `YOU MUST`) for stronger adherence

**8 points**: Mostly actionable

**4 points**: Some vague instructions

**0 points**: Vague or theoretical

### 7. Redundancy (10 points)

Penalizes content that duplicates what an agent can discover through tools or that restates existing documentation.

**10 points**: Zero redundant content
- Nothing discoverable via `ls`, `Glob`, `Grep`, or `Read`
- No paraphrased README or docs content (uses `@` imports instead)
- No duplicate instructions across CLAUDE.md and rules files

**7 points**: Minor redundancy (1-2 discoverable items)

**4 points**: Moderate redundancy (directory trees, file descriptions)

**0 points**: Heavily redundant — restates README, lists file structure, paraphrases docs

### 8. Content Density (10 points)

Measures signal-to-noise ratio. Every line should change agent behavior or prevent a mistake.

**10 points**: Every line is actionable or preventive
- No tutorial-style explanations
- No generic best practices
- No LLM-generated boilerplate
- Passes the "50-line briefing" test: could the essential info fit in 50 lines?

**7 points**: High density, minor noise (a few generic lines)

**4 points**: Mixed — some valuable content buried in noise

**0 points**: Low density — mostly generic advice, verbose prose, or LLM-generated content

### 9. Rules Organization (bonus, 10 points)

**10 points**: Well-organized rules structure
- Topic-specific rule files with clear naming
- Path-scoped rules where appropriate
- No overlap with CLAUDE.md content
- Subdirectories for logical grouping

**7 points**: Good rules, minor organization issues

**4 points**: Rules exist but poorly organized or overlapping

**0 points**: No rules files (not penalized — rules are optional)

## Assessment Process

1. Read the CLAUDE.md file completely
2. Cross-reference with actual codebase:
   - Run documented commands (mentally or actually)
   - Check if referenced files exist
   - Verify architecture descriptions
3. **Redundancy audit**: For each section, ask "Could an agent discover this with tools?"
4. Score each criterion
5. Calculate total and assign grade
6. List specific issues found
7. Propose concrete improvements (often removals, not additions)

## Quality Scores

- **A (90-100)**: Lean, high-signal, non-redundant, current
- **B (70-89)**: Good signal, minor redundancy or gaps
- **C (50-69)**: Useful content mixed with noise
- **D (30-49)**: Sparse, redundant, or outdated
- **F (0-29)**: Missing, severely outdated, or mostly boilerplate

## Red Flags

- Commands that would fail (wrong paths, missing deps)
- References to deleted files/folders
- Outdated tech versions
- Copy-paste from templates without customization
- Generic advice not specific to the project
- "TODO" items never completed
- Duplicate info across multiple CLAUDE.md files
- Duplicate instructions between CLAUDE.md and rules files
- Rules files that are too broad (should be split into focused topics)
- Missing path filters on rules that only apply to specific file types
- **Directory trees or file listings** (discoverable via `ls`/`Glob`)
- **Paraphrased README content** (should use `@README.md` import)
- **Tutorial-style prose** (CLAUDE.md is a briefing, not a tutorial)
- **LLM-generated boilerplate** (verbose, hedging language, "comprehensive" descriptions)

## Diagnostic Signals

When evaluating CLAUDE.md effectiveness (not just content), check for these patterns:

- Claude repeatedly ignores a rule → file is likely too long, critical rules are getting lost in noise
- Claude asks questions already answered in CLAUDE.md → phrasing is ambiguous or buried too deep
- Claude follows rules inconsistently → emphasis markers (`IMPORTANT`, `YOU MUST`) may help
- `@` imports referencing deleted or moved files → broken import chain
- CLAUDE.md has directory trees → check if discoverable via `ls`/`Glob` — if so, remove them
- CLAUDE.md exceeds 300 lines → likely contains redundant or low-density content
