# Contributing

Thanks for helping. Keep the format small: these skills follow the [Agent Skills specification](https://agentskills.io/specification) and must stay portable across agents.

## Propose a change

1. Open an issue first for a new skill or a change in behavior. For a version bump after a new vendor release, go straight to a PR.
2. Keep each PR to one skill.

## Rules for skill text

- `name` matches the folder name. Lowercase letters, digits and hyphens only.
- `description` says what the skill does and when to use it, with the phrases a user would type. Max 1024 characters.
- Keep the procedure agent-neutral. Do not name one agent's tools or features (sessions, connected repos, review bots). Say "your agent" and "subagents, if supported".
- Keep the approval gate, the no-guessed-versions rule and the before-and-after tests. Those are the point of these skills.
- Cite the vendor advisory for every version number you add.
- Plain words, short sentences, active voice.

## Version bumps

When a vendor releases a newer fixed version:

1. Update the fixed releases in the skill body and `description`.
2. Bump `metadata.version` in the frontmatter.
3. Add a line to `CHANGELOG.md`.
