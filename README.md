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

---

## Part of the Libre Open-Source Stack for Claude Code

This repository is part of a growing family of open-source toolkits for Claude Code.

### Libre suite — comprehensive plugin bundles

- [LibreUIUX-Claude-Code](https://github.com/HermeticOrmus/LibreUIUX-Claude-Code) — UI/UX development (152 agents, 70 plugins, 76 commands, 74 skills)
- [LibreArch-Claude-Code](https://github.com/HermeticOrmus/LibreArch-Claude-Code) — Software architecture and system design
- [LibreCopy-Claude-Code](https://github.com/HermeticOrmus/LibreCopy-Claude-Code) — Technical writing and documentation engineering
- [LibreDevOps-Claude-Code](https://github.com/HermeticOrmus/LibreDevOps-Claude-Code) — DevOps engineering and infrastructure automation
- [LibreEmbed-Claude-Code](https://github.com/HermeticOrmus/LibreEmbed-Claude-Code) — Embedded systems, firmware, and IoT development
- [LibreFinTech-Claude-Code](https://github.com/HermeticOrmus/LibreFinTech-Claude-Code) — Financial technology development
- [LibreGEO-Claude-Code](https://github.com/HermeticOrmus/LibreGEO-Claude-Code) — AI-search optimization (ChatGPT, Perplexity, Gemini, Google AI Overviews)
- [LibreGameDev-Claude-Code](https://github.com/HermeticOrmus/LibreGameDev-Claude-Code) — Game development across Godot, Unity, Unreal
- [LibreMLOps-Claude-Code](https://github.com/HermeticOrmus/LibreMLOps-Claude-Code) — ML engineering and AI operations
- [LibreMobileDev-Claude-Code](https://github.com/HermeticOrmus/LibreMobileDev-Claude-Code) — Mobile app development (Flutter, React Native, native iOS, native Android)
- [LibreSecOps-Claude-Code](https://github.com/HermeticOrmus/LibreSecOps-Claude-Code) — Security operations
- [LibreSessionFlow-Claude-Code](https://github.com/HermeticOrmus/LibreSessionFlow-Claude-Code) — Session lifecycle: handoff, pickup, absorb, explore, close

### Skills mini-repos — single CLAUDE.md drop-ins

- [vibe-engineer-skills](https://github.com/HermeticOrmus/vibe-engineer-skills) — Direct AI codegen well: hypothesis before help, scoped prompts, validate before accepting
- [markdown-discipline-skills](https://github.com/HermeticOrmus/markdown-discipline-skills) — Strip AI-slop from markdown (no em dashes, no marketing fluff)
- [shell-safety-skills](https://github.com/HermeticOrmus/shell-safety-skills) — `set -euo pipefail` discipline plus 15 failure-mode examples
- [commit-standard-skills](https://github.com/HermeticOrmus/commit-standard-skills) — Ormus Commit Standard v1.0 plus commit-msg hook and commitlint
- [unwoke-skills](https://github.com/HermeticOrmus/unwoke-skills) — Strip AI theater (ten sins to eliminate, symmetric engagement)
- [python-conventions-skills](https://github.com/HermeticOrmus/python-conventions-skills) — Modern Python 3.11+ (types, pathlib, async, ruff, mypy, uv)
- [typescript-conventions-skills](https://github.com/HermeticOrmus/typescript-conventions-skills) — TypeScript strict mode, discriminated unions, Result types
- [hermetic-laws-skills](https://github.com/HermeticOrmus/hermetic-laws-skills) — Seven Hermetic Principles applied to engineering
- [riper-workflow-skills](https://github.com/HermeticOrmus/riper-workflow-skills) — Research / Innovate / Plan / Execute / Review systematic dev
- [six-day-cycle-skills](https://github.com/HermeticOrmus/six-day-cycle-skills) — Sustainable shipping cadence with mandatory rest
- [token-optimization-skills](https://github.com/HermeticOrmus/token-optimization-skills) — Claude Code token and context optimization
- [osint-skills](https://github.com/HermeticOrmus/osint-skills) — OSINT research methodology (multi-wave investigative spiral)
- [calcinate-skills](https://github.com/HermeticOrmus/calcinate-skills) — Stage 1 of the Magnum Opus (burn project bloat)
- [claude-md-overhaul-skills](https://github.com/HermeticOrmus/claude-md-overhaul-skills) — Audit CLAUDE.md and MEMORY.md against caps
- [session-handoff-skills](https://github.com/HermeticOrmus/session-handoff-skills) — Session handoff and pickup discipline
- [naming-skills](https://github.com/HermeticOrmus/naming-skills) — Product naming methodology (mine the brand's vocabulary)
- [magnum-opus-skills](https://github.com/HermeticOrmus/magnum-opus-skills) — Seven-stage alchemy applied to project transformation
- [hypothesis-debugging-skills](https://github.com/HermeticOrmus/hypothesis-debugging-skills) — Hypothesis-driven debugging: reproduce, isolate, test, fix
- [vibe-proof-skills](https://github.com/HermeticOrmus/vibe-proof-skills) — Security hardening for vibe-coded full-stack apps
- [tdd-skills](https://github.com/HermeticOrmus/tdd-skills) — Test-driven development (Red-Green-Refactor) for JS/TS and Python
- [mars-skills](https://github.com/HermeticOrmus/mars-skills) — Production-readiness audit: the five mortal sins of vibe-coded MVPs
- [git-workflow-skills](https://github.com/HermeticOrmus/git-workflow-skills) — Clean git workflow: branch, atomic commits, reviewable PRs
- [code-review-skills](https://github.com/HermeticOrmus/code-review-skills) — Domain-aware code review: classify the code, then focus
- [explore-code-skills](https://github.com/HermeticOrmus/explore-code-skills) — Understand an unfamiliar codebase fast
- [dx-audit-skills](https://github.com/HermeticOrmus/dx-audit-skills) — Audit developer experience: docs, onboarding, tooling friction
- [setup-env-skills](https://github.com/HermeticOrmus/setup-env-skills) — Set up a project's development environment
- [automate-skills](https://github.com/HermeticOrmus/automate-skills) — Turn repetitive tasks into reliable automation scripts
- [quick-fix-skills](https://github.com/HermeticOrmus/quick-fix-skills) — Fast troubleshooting for common issues
- [prime-context-skills](https://github.com/HermeticOrmus/prime-context-skills) — Prime project context at the start of a session
- [auto-docs-skills](https://github.com/HermeticOrmus/auto-docs-skills) — Generate and maintain project documentation
- [learning-skills](https://github.com/HermeticOrmus/learning-skills) — Learn any technology: roadmaps, explanations, practice, cheatsheets, comparisons
- [linux-sysadmin-skills](https://github.com/HermeticOrmus/linux-sysadmin-skills) — Linux system administration: security, performance, diagnostics, monitoring, maintenance

### Template source

- [andrej-karpathy-skills](https://github.com/HermeticOrmus/andrej-karpathy-skills) — the canonical single-file CLAUDE.md pattern (fork of jiayuan_jy's original)

Star the family, not just one — that's how the suite stays coherent.
