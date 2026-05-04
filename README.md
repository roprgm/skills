# skills

A small collection of personal Agent Skills, MIT-licensed.

Skills follow the open [Agent Skills](https://agentskills.io) format (a `SKILL.md` file with `name` + `description` frontmatter), so they work with any agent runtime that reads that format.

## Available skills

- [`design`](skills/design/) — an opinionated, dense, neutral design language for React + Tailwind UIs. Encodes tokens, type scale, surface model, table and form conventions, and a starter `globals.css` (OKLCH, dark-first with light variant).

## Install

### Claude Code

```sh
/plugin marketplace add roprgm/skills
/plugin install design@roprgm-skills
```

### Codex CLI

Codex reads `.agents/skills/` natively, so a manual copy is the simplest path (see *Manual* below). The repo also ships a `.codex-plugin/plugin.json` so the marketplace flow works:

```sh
codex plugin marketplace add roprgm/skills
codex plugin install roprgm-skills
```

(Check `codex plugin --help` for the exact subcommands in your version.)

### Manual (any agent that reads `.agents/skills/`)

Project-scoped:

```sh
mkdir -p .agents/skills/design
cp skills/design/SKILL.md .agents/skills/design/
```

User-global:

```sh
mkdir -p ~/.agents/skills/design
cp skills/design/SKILL.md ~/.agents/skills/design/
```

The skill is surfaced automatically when its description matches the work at hand.

## License

[MIT](LICENSE)
