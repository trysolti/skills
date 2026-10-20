# Solti Agent Skills

Official agent skills for the [Solti Platform](https://www.solti.run) —
infrastructure-free batch execution and scheduling. These are plain-Markdown
skill definitions your AI agent (Claude Code, pi, or any agent supporting the
agent-skills format) can load and execute.

## Available Skills

| Skill | Description |
| --- | --- |
| [`solti-quickstart`](./solti-quickstart/SKILL.md) | From "no local setup" to first workload submitted in one prompt: CLI install, OAuth login, workspace bootstrap, cloud connections + VM pools, starter job. |

## Install

Clone the repo and copy the skill directory into your agent's skills folder:

```bash
git clone https://github.com/trysolti/skills.git
```

- **Claude Code**: copy `solti-quickstart/` to `~/.claude/skills/solti-quickstart/`
- **pi**: copy `solti-quickstart/` to `~/.pi/agent/skills/solti-quickstart/`
- **Other agents**: copy `solti-quickstart/` into the agent's equivalent
  skills directory (or point the agent at `solti-quickstart/SKILL.md`).

Then just ask: *"Use the solti-quickstart skill to get me started on Solti."*

The Solti CLI itself is installed by the skill (or ahead of time):

```bash
brew install trysolti/tap/solti
```

## Documentation

- Getting Started: https://docs.solti.run/docs/getting-started/quickstart
- Docs portal: https://docs.solti.run
- API reference: https://api.docs.solti.run
