# OpenCode Skills

Reusable opencode skills for DDEV-based development workflows.

These skills capture workflow rules, command patterns, and safety checks so opencode can apply the right habits consistently across development sessions.

## Included Skills

- `symfony-ddev`: Symfony, Doctrine, migrations, console commands, assets, and verification guidance.
- `wordpress-ddev`: WordPress, WP-CLI, database, plugin/theme, content, and verification guidance.

## Usage

Clone the repository:

```bash
git clone https://github.com/agaktr/opencode-skills.git
```

Register the skills directory in your opencode config:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "skills": {
    "paths": ["/path/to/opencode-skills/skills"]
  }
}
```

Restart opencode after changing the config. Running sessions keep using the already-loaded configuration.

## Structure

Each skill lives in its own folder with a `SKILL.md` file:

```text
skills/
  symfony-ddev/SKILL.md
  wordpress-ddev/SKILL.md
```

Review and adapt environment-specific details before using these skills in a different setup.
