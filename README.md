# skill-pack

A curated bundle of Claude Code skills and tools for people getting started with AI-assisted development.

## Installation

```bash
# Install all skills
claude skills install --from github:samzoloth/skill-pack

# Or install individual skills
claude skills install --from github:samzoloth/skill-pack/skills/<skill-name>
```

## Skills

### Working with AI

| Skill | Description |
|-------|-------------|
| [prompt-engineer](skills/prompt-engineer) | Expert prompt engineering using the "Genius Intern Framework" |
| [think-first](skills/think-first) | "Think First, AI Second" — stay sharp while working with AI |
| [skill-creator](skills/skill-creator) | Create skills that extend Claude's capabilities |

### Dev workflow

| Skill | Description |
|-------|-------------|
| [tdd](skills/tdd) | Test-driven development with vertical slices and tracer bullets (by [mattpocock](https://github.com/mattpocock/skills)) |
| [qa](skills/qa) | Full QA on all session changes using Codex as a second pair of eyes |
| [agentic-review](skills/agentic-review) | Multi-agent code review — security, perf, architecture in parallel |
| [iterate](skills/iterate) | Spin up N agents on isolated worktrees, compare approaches, pick winner |
| [visual-explainer](skills/visual-explainer) | Generate self-contained HTML pages that visually explain systems and code |
| [napkin](skills/napkin) | Per-repo napkin file as a continuously curated runbook (by [blader](https://github.com/blader/napkin)) |

### Product & strategy

| Skill | Description |
|-------|-------------|
| [b2b-expert-advisor](skills/b2b-expert-advisor) | B2B startup strategy advisor grounded in Lenny Rachitsky's 7-part series and April Dunford's positioning framework |
| [plan-ceo-review](skills/plan-ceo-review) | Founder/CEO plan review — rethink the problem, find the 10-star product (from [Garry Tan's gstack](https://github.com/garrytan/gstack)) |

### Design

| Skill | Description |
|-------|-------------|
| [design-swarm](skills/design-swarm) | Orchestrate a team of 10 design agents for holistic UI audit, ideation, and implementation |

## Recommended tools

Not skills, but essential tools for building with AI.

| Tool | What it does | Install |
|------|-------------|---------|
| [Agentation MCP](https://github.com/benjitaylor/agentation) | Visual feedback toolbar + MCP server for AI-assisted design critique | `npx add-mcp "npx -y agentation-mcp server"` |
| [DialKit](https://github.com/joshpuckett/dialkit) | Floating control panel for React — sliders, toggles, spring editors for live-tuning UI | `npm install dialkit motion` |
| [Tirith](https://github.com/sheeki03/tirith) | Terminal security — intercepts homograph attacks, ANSI injection, and pipe-to-shell attacks | See [repo](https://github.com/sheeki03/tirith) |
| [ACIP](https://github.com/Dicklesworthstone/acip) | Advanced Cognitive Inoculation Prompt — hardened system prompt security | See [repo](https://github.com/Dicklesworthstone/acip) |

## Contributing

PRs welcome. Each skill should be a self-contained directory under `skills/` with a `SKILL.md` file.

## License

MIT
