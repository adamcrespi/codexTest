---
name: weather-data-script
description: "Create or review source-first weather/prediction-market data scripts with manifests, idempotency, and uv/Python quality gates."
---

# Weather data script

Use this skill when creating, changing, or reviewing scripts under `scripts/<source>/`.

Canonical source: `.claude/skills/data-script/SKILL.md`. New source scripts should start from `.claude/skills/data-script/template.py`.

## Required layout

Each data source has one folder:

```text
scripts/<source>/
├── download.py
├── transform.py      # optional
├── validate.py       # optional
└── <helper>.py       # optional, source-specific only
```

No top-level `scripts/download/` or `scripts/transform/` folders. No shared `_common.py` unless explicitly justified by reuse pressure.

## Stage contracts

- `download.py`: upstream to `data/raw/<source>/`; output immutable originals.
- `transform.py`: `data/raw/<source>/` to `data/interim/<step>/` or `data/processed/<task>/`.
- `validate.py`: post-run sanity and fidelity checks.

Every raw or processed target writes a `MANIFEST.json` with lifecycle status and source/script metadata.

## Required CLI behavior

Every stage should support:

- `--force`
- `--fresh`
- `--dry-run`
- `--verbose` / `-v`

Use idempotency gates: complete manifests skip by default; in-progress or failed manifests require explicit force/fresh behavior.

## Quality bar

Run relevant checks before completion:

```sh
uv run ruff check .
uv run pyright
uv run pytest
```

For docs/config-only updates, at least run structural checks and `git diff --check`.

## Data safety

Never hand-edit `data/raw/`. Never commit downloaded data. The only CSV carveout is `weather-market-slugs/polymarket.csv`.
