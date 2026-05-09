---
name: weather-strategy-review
description: "Review deployable weather prediction-market strategies for structure, causality, edge quality, execution risk, and operational readiness."
---

# Weather strategy review

Use this skill before marking a strategy deployable, after changing `src/<strategy_name>/`, and before live Polymarket/Kalshi execution.

Primary references:

- `CLAUDE.md` strategy conventions
- `src/README.md`
- `src/consensus_fade_plus1/ARCHITECTURE.md`
- Relevant vault pages from `vault/Weather Vault/wiki/index.md`

## Required structure

Each deployable strategy under `src/<strategy_name>/` should have:

- `STRATEGY.md`: thesis, signal, execution rules, IS/OOS backtest, capacity, risk, kill switches, deployment checklist.
- `strategy.py`: pure signal generation; no CLOB calls or order placement.
- `cli.py`: operator commands such as `setup`, `recommend`, `submit`, `cancel-all`, `status`.
- `backtest.py`: reproduces claims in `STRATEGY.md`.

## Review gates

Block approval if any gate fails:

- Future leakage or non-causal feature joins.
- Random time-series splits.
- Missing transaction-cost / spread / fee / capacity analysis.
- No OOS evidence or unclear IS/OOS boundary.
- Live order path lacks dry-run/recommendation guard or explicit user authorization.
- Missing kill switch, max exposure, or per-market cap for live strategies.
- Strategy docs claim more than tests/backtests prove.
- Durable decision or result is not captured with `weather-vault-capture`.

## Verification

Prefer:

```sh
uv run pytest
uv run ruff check .
uv run pyright
```

Add strategy-specific replay/backtest commands when available and inspect the output before approval.
