# Skills

Skills for the SalesQL plugin live here, one directory per skill with a `SKILL.md` inside:

```
skills/
└── <skill-name>/
    └── SKILL.md
```

Each skill is exposed as a slash command (`/salesql:<skill-name>`) and can also be invoked automatically by Claude when the context matches its description.

## Available skills

| Skill | Command | What it does |
| --- | --- | --- |
| [find-leads](find-leads/SKILL.md) | `/salesql:find-leads` | Research a business's ICP, search for matching people, and reveal verified emails and phones |

More skills (campaign launching, email verification) will ship in upcoming releases as the underlying MCP tools become available.
