# AGENTS.md — Weather Prediction Markets Project

This repository is a solo quantitative trading project for prediction-market weather contracts on Kalshi and Polymarket at major US airports. It is not a generic weather forecasting product. Optimize for **edge vs market-implied probability**, calibration, transaction costs, market-resolution rules, and execution timing; do not optimize for RMSE alone.

`CLAUDE.md`, `vault/Weather Vault/Project Scope.md`, and `vault/Weather Vault/wiki/index.md` are canonical project memory. This file adapts those rules for Codex/OMX. If a deeper `AGENTS.md` exists, it overrides this file for its subtree. Direct system, developer, and user instructions override this file.

## First moves for non-trivial work

1. Read or seed from:
   - `CLAUDE.md`
   - `vault/Weather Vault/Project Scope.md`
   - `vault/Weather Vault/wiki/index.md`
   - the top 3-5 relevant vault pages for the task
2. Identify whether the work is data ingestion, research/backtest, strategy implementation, trading operations, or documentation.
3. Use the smallest safe workflow that can finish and verify the task.
4. Capture durable knowledge back into the vault when the work produces a decision, data-source finding, schema gotcha, strategy result, or operational lesson.

Use `.codex/skills/weather-vault-seed/SKILL.md` before planning, designing, researching, coding, or answering substantial project questions. Use `.codex/skills/weather-vault-capture/SKILL.md` when durable knowledge is produced.

## Operating principles

- **Trading, not forecasting.** Prefer calibrated probability estimates, edge calculations, transaction-cost awareness, and decision thresholds over point-forecast accuracy.
- **CONUS-first.** HRRR is CONUS-only. International markets require a different stack and are out of scope unless explicitly planned.
- **Airport-specific.** Models and features should respect each station's ground truth and local microclimate.
- **Causality is load-bearing.** Features must be as-of safe. No observation at or after the prediction target may leak into features.
- **Real-time pipeline matters.** New HRRR / NBS / GFS / market data can matter inside 15-45 minute repricing windows.
- **Thin adapters over duplicated policy.** Keep `CLAUDE.md`, the vault, and `.claude/skills` as source of truth. Codex files should adapt them, not fork them.

## Language and tooling

- Python 3.13 is the project language. Use `uv` for all Python execution.
- Do not introduce TypeScript or Node in this repo.
- Bash is not a top-level product surface. If shelling out is necessary for data tooling, do it from Python subprocesses and document the dependency.
- Run Python commands through `uv run`; do not rely on an activated virtualenv.

Standard commands:

```sh
uv sync
uv run python scripts/some_script.py
uv run pytest
uv run ruff check .
uv run ruff format .
uv run pyright
uv run cfp {setup,recommend,submit,cancel-all,status}
```

## Data conventions

- `data/` is gitignored and contains `raw/`, `interim/`, and `processed/`.
- Never commit GRIB2, Parquet, NetCDF, downloaded CSVs, credentials, or local `.env` files.
- `weather-market-slugs/polymarket.csv` is the committed CSV carveout because it is a small, semi-permanent source-of-truth slug catalog.
- Never hand-edit `data/raw/`. Write transformations under `scripts/<source>/transform.py`.
- Every source lives under `scripts/<source>/` with stage files:
  - `download.py` for upstream to `data/raw/<source>/`
  - optional `transform.py` for raw to `data/interim/` or `data/processed/`
  - optional `validate.py` for sanity and fidelity checks
- Every raw and processed dataset needs a `MANIFEST.json` with lifecycle status. Follow `.codex/skills/weather-data-script/SKILL.md` and the canonical `.claude/skills/data-script/SKILL.md` contract.
- Before calling a data source complete, use `.codex/skills/weather-data-validation/SKILL.md` and climb the audit ladder far enough to compare against upstream when needed.

## Weather and market modeling conventions

- Use UTC internally. Convert to local time only at the market-resolution boundary.
- Use time-based splits only. Never use random train/test splits for time-series weather or market data.
- Evaluate calibration separately from accuracy with reliability curves, Brier score, log loss, and realized edge.
- Account for transaction costs, fees, spread, capacity, fill probability, and market impact before labeling a strategy deployable.
- Treat Polymarket/Kalshi market definitions, resolution stations, local time windows, and endpoint schemas as first-class data, not assumptions.
- For Polymarket execution work, review `vault/Weather Vault/wiki/index.md` for `[[Polymarket]]`, `[[Polymarket CLOB execution]]`, `[[Polymarket CLOB WebSocket]]`, and related pages.

## Strategy conventions

Deployable trading strategies live under `src/<strategy_name>/`. Each strategy should include:

- `STRATEGY.md` — thesis, signal definition, execution rules, IS/OOS backtest, capacity, risk controls, kill switches, deployment checklist.
- `strategy.py` — pure signal generation; no CLOB calls or order placement.
- `cli.py` — standard subcommands such as `setup`, `recommend`, `submit`, `cancel-all`, and `status`.
- `backtest.py` — reproduces historical stats in `STRATEGY.md`.

Shared infrastructure belongs under `src/lib/`, especially `src/lib/polymarket/`, `src/lib/weather/`, and related reusable packages. Do not pre-create abstractions before more than one strategy needs them.

The active strategy index is `src/README.md`; `src/consensus_fade_plus1/ARCHITECTURE.md` is the current architecture reference for the production Polymarket path.

## Project-specific Codex roles

Use project-local agents when available:

- `weather-planner` — plans weather/Polymarket research, data, and strategy work. It reads the vault first and does not implement.
- `weather-executor` — implements Python/uv/data/strategy tasks with data-script, strategy, and vault-capture rules.
- `weather-reviewer` — reviews for causality, leakage, calibration, execution risk, schema validity, and validation evidence.

Keep domain workflows in `.codex/skills/weather-*`; do not replace planner/executor/reviewer roles with task agents.

## Skills to use

- `weather-vault-seed` — load project memory before non-trivial work.
- `weather-vault-capture` — write durable findings back into the vault.
- `weather-data-script` — create or review source-first data scripts.
- `weather-data-validation` — audit data fidelity before modeling or trading.
- `weather-market-research` — structure Polymarket/Kalshi weather market research.
- `weather-strategy-review` — review deployable strategy artifacts and risk controls.

## Documentation rules

- Keep README files minimal. Durable domain knowledge belongs in `vault/Weather Vault/wiki/` as entity, concept, or synthesis pages.
- Strategy docs and decision records use normal prose, not caveman style.
- The repo's default conversational style is caveman-full by reference to `.claude/skills/caveman/SKILL.md`, but use normal prose for commit messages, PR descriptions, code comments, vault/wiki content, decision docs, planning docs, and any place ambiguity would be costly.

## Safety

- Never commit `.env`, credentials, API keys, private keys, wallet secrets, or service tokens.
- Never run destructive git operations such as `reset --hard`, force push, or branch deletion without explicit approval.
- Never place real orders, alter live trading credentials, or touch external production services unless the user explicitly asks and the command has a dry-run or clear operational guard.
- Prefer `--dry-run`, paper trading, or recommendation-only flows before live `submit` flows.
- Preserve hooks and signing behavior unless explicitly instructed otherwise.

## Verification

For docs/config-only changes, at minimum run structural validation such as TOML parsing, frontmatter checks, reference checks, `git diff --check`, and staged-file review.

For Python/data/strategy changes, prefer this order:

```sh
uv run ruff check .
uv run pyright
uv run pytest
```

Add targeted data validation for changed sources and strategy-specific backtest/replay checks for changed trading logic. Do not claim a data source, model, or strategy is ready to trade without fresh evidence.

## Commit protocol

Commits must use the Lore protocol:

```text
<intent line: why the change was made, not what changed>

<optional concise body: constraints and approach rationale>

Constraint: <external constraint that shaped the decision>
Rejected: <alternative considered> | <reason for rejection>
Confidence: <low|medium|high>
Scope-risk: <narrow|moderate|broad>
Directive: <forward-looking warning for future modifiers>
Tested: <what was verified>
Not-tested: <known gaps in verification>
```

Stage intentionally. Before committing, inspect `git status --short`, `git diff --cached --name-only`, and ensure no data, secrets, `.omx/`, `.venv/`, or unrelated generated files are staged.
