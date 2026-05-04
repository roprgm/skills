# skills

A small collection of personal Claude Code skills, MIT-licensed.

## Available skills

- [`design`](skills/design/) — an opinionated, dense, neutral design language for React + Tailwind UIs. Encodes tokens, type scale, surface model, table and form conventions, and a starter `globals.css` (OKLCH, dark-first with light variant).

## Using a skill

Pick the skill you want and copy its directory into one of:

- `~/.claude/skills/<name>/` — available globally across all your projects
- `.claude/skills/<name>/` inside a project — scoped to that project

For example, to use `design` globally:

```sh
mkdir -p ~/.claude/skills/design
cp skills/design/SKILL.md ~/.claude/skills/design/
```

Claude Code surfaces the skill automatically when the description matches the work at hand.

## License

[MIT](LICENSE)
