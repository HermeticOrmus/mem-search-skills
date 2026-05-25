# Using this repo with Cursor

This project includes a Cursor project rule so the mem-search workflow is available when you work here.

The rule requires the claude-mem MCP and its `search`, `timeline`, and `get_observations` tools. Install claude-mem first; the rule only tells the agent how to use those tools well.

## In this repository

1. Open the folder in Cursor.
2. The rule [`.cursor/rules/mem-search.mdc`](.cursor/rules/mem-search.mdc) is committed with `alwaysApply: false`, so it applies situationally, when the agent is recalling past work rather than on every turn.
3. In Cursor, confirm under Settings, then Rules, where `mem-search` should appear.

## Use the same rule in another project

**Cursor (recommended)**: Copy `.cursor/rules/mem-search.mdc` into that project's `.cursor/rules/` directory (create the folders if needed). Merge with existing rules as you like.

**Other AI coding tools**: If a stack only supports a root instruction file, copy [`CLAUDE.md`](CLAUDE.md) into that project instead (or merge its contents into your existing instructions). Most modern AI coding tools (Claude Code, Continue, Cline, Windsurf, Aider) read a root-level instruction file.

## Optional: personal Agent Skills

If you want the same content as a reusable skill under `~/.cursor/skills`, use [`skills/mem-search/SKILL.md`](skills/mem-search/SKILL.md). Copy or symlink it into your personal skills directory.
