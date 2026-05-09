---
name: weather-data-validation
description: "Audit weather and Polymarket data fidelity before modeling, backtesting, deployment, or claiming a source is done."
---

# Weather data validation

Use this skill before calling a data source complete, before using data for modeling/backtests, after download/transform changes, and before trading decisions that depend on newly produced data.

Canonical source: `.claude/skills/data-validation/SKILL.md`.

## Validation ladder

1. **Manifest and disk:** manifests exist, parse, report `complete`, and match disk contents.
2. **Row and column fidelity:** raw rows map to transformed rows; columns are preserved or explicitly documented.
3. **Value-level fidelity:** compare raw values to transformed values by primary key with documented null/sentinel rules.
4. **Schema and physical invariants:** dtype, range, timestamp, monotonic, NaN, and cross-column checks.
5. **Fresh upstream re-fetch:** compare saved windows against current upstream responses to catch boundary/query bugs.
6. **Stress and recurrence checks:** coverage, duplicates, corrected reports, encoding, idempotency, and edge cases.

## Required weather-market checks

- Timestamps are UTC internally.
- Market-resolution local-time conversions happen only at resolution boundaries.
- Feature joins are causal: no observations from `t >= valid_time` leak into prediction features.
- Time-series splits are time-based, never random.
- Polymarket/Kalshi market IDs, station mappings, and resolution windows match vault/entity documentation.

## Done bar

A validation pass must say which ladder levels ran, what evidence was checked, and what remains untested.
