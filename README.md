# skills

A small collection of personal Agent Skills, MIT-licensed.

## Available skills

- [`design`](skills/design/) — an opinionated, dense, neutral design language for React + Tailwind UIs. Encodes tokens, type scale, surface model, table and form conventions, and a starter `globals.css` (OKLCH, dark-first with light variant).

## Using a skill

Pick the skill you want and copy its directory into the location your agent reads skills from:

- `~/.claude/skills/<name>/` — available globally
- `.claude/skills/<name>/` inside a project — scoped to that project

For example, to install `design` globally:

```sh
mkdir -p ~/.claude/skills/design
cp skills/design/SKILL.md ~/.claude/skills/design/
```

Skills are surfaced automatically when their description matches the work at hand.

## License

[MIT](LICENSE)
