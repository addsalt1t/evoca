# Evoca

A Claude Code plugin marketplace. See @README.md for plugin listing.

## Architecture

```
plugins/<name>/
  .claude-plugin/plugin.json   # Plugin metadata (name, version, author)
  README.md                    # User-facing usage guide
  skills/<skill>/
    SKILL.md                   # Skill logic with YAML frontmatter (name, description, tools)
    defaults.json              # Runtime defaults (optional)
    references/                # Deep reference docs (optional)
  commands/
    <command>.md               # Slash commands (optional)
```

## Gotchas

- Plugin cache (`~/.claude/plugins/cache/`) is separate from source — after editing source files, manually sync to cache or reinstall
- SKILL.md YAML frontmatter `tools` field restricts which tools the skill can use when invoked
- Each plugin is self-contained — no shared dependencies between plugins
