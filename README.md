# skills

Agent skills, installable with the [skills CLI](https://github.com/vercel-labs/skills).

```bash
npx skills add pvinchon/skills           # pick skills interactively
npx skills add pvinchon/skills --list    # list without installing
```

## Contributing

Each skill is a directory under `skills/` holding a `SKILL.md` with `name` and `description`
frontmatter; see [`skills/hello-world`](skills/hello-world/SKILL.md).

With [mise](https://mise.jdx.dev), `mise install` sets up Node and the CLI, then:

```bash
mise run init my-skill   # create skills/my-skill/SKILL.md
mise run list            # list the skills the CLI finds in your checkout
mise run install         # install your checkout's skills globally
```
