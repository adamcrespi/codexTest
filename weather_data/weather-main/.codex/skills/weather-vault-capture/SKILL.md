---
name: weather-vault-capture
description: "Capture durable weather-market project knowledge into the Obsidian vault wiki and update index/log."
---

# Weather vault capture

Use this skill whenever work produces durable project knowledge.

Canonical source: `.claude/skills/vault-capture/SKILL.md`.

## Capture triggers

Capture when you produce or discover:

- A new data source, endpoint, schema gotcha, provider limitation, or station fact.
- A Polymarket/Kalshi market-structure, fee, resolution, CLOB, or websocket finding.
- A strategy result, retraction, risk control, capacity estimate, or deployment lesson.
- An architectural decision that future agents should not re-derive.
- A bug diagnosis or validation failure with reusable lessons.

## Where to write

Use `vault/Weather Vault/wiki/`:

- `entities/` for named things: providers, stations, venues, APIs, models.
- `concepts/` for ideas and methods: calibration, Kelly sizing, HRRRx, data validation.
- `syntheses/` for dated decisions, cross-source analyses, strategy results, and lessons.

Every new or materially updated page must also update:

- `vault/Weather Vault/wiki/index.md`
- `vault/Weather Vault/wiki/log.md`

## Content rules

- Facts over prose.
- Cite file paths, commit SHAs, vault pages, or external URLs.
- Use UTC dates.
- Wikilink aggressively.
- Keep code documentation in code/docstrings; keep durable domain knowledge in the vault.

## Safety

Do not capture secrets, wallet keys, API tokens, or private credentials.
