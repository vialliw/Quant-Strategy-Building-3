# Quant Strategy Research Lab

A local research system that tests short-term stock reversal strategies and
keeps only the ones that still make money **after** realistic trading costs.

## What it does, in one sentence

Finds stocks that dropped recently in a way that "looks like" past drops,
checks what actually happened after those past drops, and only suggests a
trade if history says the odds are good once fees and slippage are included.

## Project documents

- [Roadmap](docs/ROADMAP.md)
- [Version A: aims, results and limits](docs/wiki/Version-A.md)

The engine has been built and a small sanity run completed. No strategy is approved for live use. Source, tests and local data listed below describe the local workspace; this publication includes documentation only.

## How it works

1. **Data** — Daily prices for ~1,200 US tickers (2010–today) come from a
   local Arrow Flight service. Earnings dates, raw prices, dividends and test
   results are stored in a local DuckDB file (`data/quant.duckdb`).
2. **Trade simulator** (`src/simulator.py`) — Simulates one trade at a time
   with trading assumptions: buy at the next day's open, exit at a profit
   target, a stop-loss, or after 8 trading days. Includes commissions,
   slippage, gaps, splits, dividends, and earnings exclusions.
3. **Analog engine** (`src/analogs.py`) — For each candidate signal, finds
   similar past situations ("analogs") and counts what happened next. Only
   past events are used — never future information.
4. **Portfolio simulator** (`src/portfolio.py`) — Applies real-world limits:
   max 10 open positions, max 2 new trades per day, no more than 1% of the
   account risked per trade, no more than 7% of the account in one stock.
5. **Validation** (`src/validation.py`, `src/promotion.py`) — Every strategy
   must pass three gates before it can be used live: (1) does it make money
   after costs, (2) does the edge hold up in years it has never seen, (3) does
   it work on a locked-away final test period.
6. **Experiment ledger** (`src/experiment_ledger.py`) — Every test attempt,
   pass or fail, is permanently recorded so results can't be cherry-picked.

## The five strategy ideas being tested

All are "buy the dip" variations:

1. Plain reversal — stock dropped, buy it back.
2. Market-adjusted reversal — dropped *more than the market*.
3. Volatility-adjusted reversal — dropped a lot *for that stock*.
4. Reversal inside an uptrend — dropped but long-term trend still up.
5. Reversal conditioned on the market regime (VIX, rates, breadth).

Each idea is tested two ways: as a simple rule and as analog matching.

## Honest known limitations

- The stock universe only contains tickers that exist today — delisted stocks
  are missing ("survivorship bias"). We compensate with stricter pass bars.
- Historical earnings dates are approximate (we know *when* results were
  reported, not always *when they were announced*). Strategies using this
  stay "exploratory" until bounded.
- A handful of tickers (WBD, QRVO, LESL, DBRG) have gaps where Yahoo stopped
  serving history during corporate actions.
- Nothing here is live trading advice; the daily signal file comes later and
  only after a 4–6 week shadow period.

## Layout

```
src/        engine code (data, simulator, analogs, portfolio, validation)
tests/      57 unit/integration tests — run: python -m pytest tests/ -q
docs/       requirements (v6, approved), audit findings, roadmap, wiki
data/       local DuckDB (earnings, raw closes, sim cache, experiment ledger)
reports/    run outputs (candidates, trades, audit JSON)
```
