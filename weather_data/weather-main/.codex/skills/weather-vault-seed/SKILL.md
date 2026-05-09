---
name: weather-vault-seed
description: "Load Weather Vault project memory before non-trivial weather prediction-market planning, research, coding, or review."
---

# Weather vault seed

Use this skill before non-trivial project work in this repo.

Canonical source: `.claude/skills/vault-seed/SKILL.md`.

## Required context

Read, in order:

1. `CLAUDE.md`
2. `vault/Weather Vault/Project Scope.md`
3. `vault/Weather Vault/wiki/index.md`
4. The top 3-5 relevant vault pages from `vault/Weather Vault/wiki/entities/`, `concepts/`, or `syntheses/`.

If the index does not surface the topic, grep the vault for task keywords such as `Polymarket`, `Kalshi`, station code, endpoint, strategy name, model family, or data source.

## Output contract

Before acting, summarize the loaded context briefly:

```text
(from vault)
- <fact> — <vault page or source path>
- <fact> — <vault page or source path>
```

Then proceed with the actual task.

## Boundaries

- Do not fabricate vault knowledge. If no relevant page exists, say the vault has no prior context on the topic.
- Use `weather-vault-capture` after producing durable decisions, gotchas, schema findings, strategy results, or operational lessons.
