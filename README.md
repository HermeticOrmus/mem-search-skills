<p align="center">
  <img src="https://ormus.solutions/mascot/pixellab_liquid_to_spiral.gif" alt="Mem Search Skills" width="128" style="image-rendering: pixelated;" />
</p>

<h1 align="center">Mem Search Skills</h1>

<p align="center">
  <em>A Claude Code skill for searching claude-mem's cross-session memory — search, filter, then fetch, for 10x token savings when recalling past work.</em>
</p>

<p align="center">
  <a href="https://github.com/HermeticOrmus/mem-search-skills/stargazers"><img src="https://img.shields.io/github/stars/HermeticOrmus/mem-search-skills?style=flat-square&color=aa8142" alt="Stars" /></a>
  <a href="https://github.com/HermeticOrmus/mem-search-skills/blob/main/LICENSE"><img src="https://img.shields.io/github/license/HermeticOrmus/mem-search-skills?style=flat-square&color=aa8142" alt="License" /></a>
  <a href="https://github.com/HermeticOrmus/mem-search-skills/commits"><img src="https://img.shields.io/github/last-commit/HermeticOrmus/mem-search-skills?style=flat-square&color=aa8142" alt="Last Commit" /></a>
  <img src="https://img.shields.io/badge/Claude_Code-aa8142?style=flat-square&logo=anthropic&logoColor=white" alt="Claude Code" />
</p>

---

A single instruction file that teaches Claude how to recall past work from claude-mem cheaply: search for an index, filter to the few results that matter, then fetch full details only for those.

## The problem

Memory across sessions is only useful if recalling it is cheap. The naive approach, fetch every matching observation and read it all, spends the expensive call on results you were going to discard anyway. On a project with hundreds of stored observations, that is the difference between a few hundred tokens and tens of thousands.

The fix is a three-layer workflow: a cheap search returns an index, you filter by reading titles, and you spend the expensive fetch only on the IDs you kept. That is roughly 10x fewer tokens for the same answer.

## The 3-layer workflow

| Layer | Tool | Cost | Purpose |
|---|---|---|---|
| Search | `search` | ~50-100 tokens per result | Get an index of IDs, timestamps, types, titles |
| Timeline | `timeline` | small window | Get context around one interesting result |
| Fetch | `get_observations` | ~500-1000 tokens each | Full details, only for the IDs you kept |

Always batch two or more IDs into one `get_observations` call: one request instead of N.

Full content: [`CLAUDE.md`](CLAUDE.md). Worked walkthroughs: [`EXAMPLES.md`](EXAMPLES.md).

## Install

This skill requires the [claude-mem](https://github.com/thedotmack/claude-mem) MCP. It assumes the `search`, `timeline`, and `get_observations` tools are available. Install claude-mem first; these files then tell Claude how to use it well.

### As a project CLAUDE.md

Drop [`CLAUDE.md`](CLAUDE.md) at the root of your repository. Claude Code picks it up automatically. Merge with existing project instructions if any.

```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/HermeticOrmus/mem-search-skills/main/CLAUDE.md
```

### As a Claude Code skill

The same content is packaged as a skill under [`skills/mem-search/`](skills/mem-search/) for `~/.claude/skills/`. See the `SKILL.md` inside for installation.

### In Cursor

See [`CURSOR.md`](CURSOR.md) for the Cursor-rule equivalent at [`.cursor/rules/mem-search.mdc`](.cursor/rules/mem-search.mdc).

### In other AI coding tools

If your tool reads a single instruction file at the project root, copy `CLAUDE.md` to whatever name your tool expects (`AGENTS.md`, `INSTRUCTIONS.md`, etc.). The workflow only depends on the claude-mem MCP tools being present.

## Why this exists

Cross-session memory is most valuable on long-running projects, which is exactly where the index is largest and a naive recall is most expensive. Filtering before fetching keeps recall affordable as the memory grows, so "did we already solve this?" stays a cheap question instead of becoming a context-blowing one.

The workflow generalizes a common pattern: when a store has a cheap index and an expensive payload, query the index first and pay for the payload only after you have narrowed the set.

## See also

- [claude-mem](https://github.com/thedotmack/claude-mem): the memory system this skill searches; provides the `search`, `timeline`, and `get_observations` MCP tools
- [`vibe-engineer-skills`](https://github.com/HermeticOrmus/vibe-engineer-skills): behavioral guidelines for the human directing AI codegen
- [`andrej-karpathy-skills`](https://github.com/HermeticOrmus/andrej-karpathy-skills): coding-discipline principles for how Claude should behave

## Contributing

PRs welcome, especially for additional worked examples in [`EXAMPLES.md`](EXAMPLES.md), translations of the README, and adaptations of `CURSOR.md` for other AI coding tools (Windsurf, Cline, Aider, Continue, etc.).

## License

MIT, use it, fork it, merge it into your own CLAUDE.md.
