# skill-pack

A curated bundle of Claude Code skills for power users.

## Installation

```bash
# Install all skills
claude skills install --from github:samzoloth/skill-pack

# Or install individual skills
claude skills install --from github:samzoloth/skill-pack/skills/<skill-name>
```

## Skills

| Skill | Description |
|-------|-------------|
| [tdd](skills/tdd) | Test-driven development with vertical slices and tracer bullets (by [mattpocock](https://github.com/mattpocock/skills)) |
| [qa](skills/qa) | Full QA on all session changes using Codex as a second pair of eyes |
| [design-swarm](skills/design-swarm) | Orchestrate a team of 10 design agents for holistic UI audit, ideation, and implementation |

## Contributing

PRs welcome. Each skill should be a self-contained directory under `skills/` with a `skill.md` file.

## License

MIT
