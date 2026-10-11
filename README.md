# dotprompts

A collection of reusable AI agent skills for software development.

## Skills

| Skill | Description |
|---|---|
| [clean-code](skills/clean-code/SKILL.md) | Enforces code readability, maintainability, and clean architecture principles — small functions, immutability, composition over inheritance, behavioral testing, and more. |

## Installation

Copy or symlink any skill into your global config to enable it across all projects:

```sh
cp -r skills/clean-code ~/.gemini/config/skills/
```

Or add it to a single project by placing it under `.agents/skills/` in your repo root.
