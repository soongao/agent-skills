# Agent Skills

Personal, platform-neutral skills for AI agents.

Each skill is stored under `skills/<skill-name>/SKILL.md`. The skill content should be written as a reusable workflow or reference that any compatible agent can read, without depending on a specific runtime such as Codex, Claude, OpenCode, or soong-agent.

## Layout

```text
skills/
  design-clarifier/
    SKILL.md
```

## Authoring Principles

- Keep `SKILL.md` platform-neutral.
- Put trigger information in the frontmatter `description`.
- Keep the body concise and procedural.
- Prefer local source-of-truth documents over generic assumptions.
- Add extra files only when they directly support the skill.
- Avoid runtime-specific install scripts or metadata in this repository.

## Current Skills

- `design-clarifier`: Clarify intent, terminology, scope, and design contracts before writing or implementing ambiguous work.
