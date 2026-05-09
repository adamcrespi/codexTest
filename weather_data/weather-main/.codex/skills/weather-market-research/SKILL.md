---
name: weather-market-research
description: "Structure weather prediction-market research for Polymarket/Kalshi with calibration, leakage, transaction-cost, and vault-capture gates."
---

# Weather market research

Use this skill for research notebooks, market analysis, backtests, and edge discovery involving Polymarket or Kalshi weather contracts.

Primary references:

- `CLAUDE.md`
- `vault/Weather Vault/Project Scope.md`
- `vault/Weather Vault/wiki/index.md`
- `src/README.md`
- `src/consensus_fade_plus1/STRATEGY.md`
- `src/consensus_fade_plus1/ARCHITECTURE.md`

## Research contract

- Optimize for edge vs market-implied probability, not RMSE alone.
- Use time-based train/test and IS/OOS splits.
- Prove causal feature availability as-of the decision time.
- Include transaction costs, fees, spreads, fill assumptions, and capacity.
- Separate exploratory findings from deployable strategy claims.
- Preserve retractions and negative results in vault syntheses when they prevent repeated mistakes.

## Surfaces

- Use `notebooks/experiments/` for exploratory Marimo or Python research.
- Promote reusable code to `src/lib/` only after reuse pressure exists.
- Promote deployable edges to `src/<strategy_name>/` with `STRATEGY.md`, `strategy.py`, `cli.py`, and `backtest.py`.

## Review prompts

Before claiming an edge, answer:

1. What market and resolution rule does this trade target?
2. Which station/source defines ground truth?
3. What was known at order time?
4. What are fees, spread, fill probability, and capacity?
5. How does OOS performance compare with IS performance?
6. What kill switch prevents continued trading after edge decay?
