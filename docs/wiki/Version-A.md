# Version A — First End-to-End Sanity Run

*This page is meant for the repo wiki. It explains, in plain language, what
the first full test run was for and what it found.*

## Why this run existed

The engine was built and code-reviewed, but nobody had ever run all the
pieces together on real market data. Version A was a **plumbing test**:
prove the whole pipeline works before spending hours of compute on real
research.

## The setup (deliberately small)

- **Strategy tested:** Idea #1 — "plain short-term reversal". When a stock
  drops, history suggests it often bounces back. Buy the dip, exit at +3%
  or −5%, or after 8 trading days — whichever comes first.
- **How matches were found:** distance matching — past days that "look
  like" today across 3 measurements (5-day return, distance from the 20-day
  low, short-term RSI).
- **Stocks:** 30 large, well-known tickers (AAPL, MSFT, NVDA, NFLX, …).
- **Period:** first quarter of 2023 (60 trading days), with history back to
  2017 used as the expanding analog library. This was not a fixed five-year training test.
- **Account:** $100,000 with real limits — max 10 open trades, max 2 new
  trades a day, max 1% of the account risked per trade, max 7% per stock.

## What happened

| Result | Number |
|---|---|
| Signals generated | 100 across 60 days |
| Trades actually filled | 57 |
| Account value at the end | $102,925 (**+2.9%**) |
| Winning trades | 43.9% |
| Profit factor (wins ÷ losses) | 1.50 |
| Worst drawdown | −14.3% |

## The verdict

**The pipeline works — the strategy doesn't pass (yet), and that's fine.**

- Matching, trade simulation, portfolio simulation, Stage 1 and ledger recording ran on historical market data. Stages 2 and 3 were not run.
- The recorded **Stage 1 failure** was too few independent events: 1 against a minimum of 10.
- Reported average trade return was +0.66%, versus +0.95% for the unconditional baseline. Beating that baseline is not a pass requirement. This comparison is provisional: the baseline covered more dates than the strategy, and rejected trade rows need to be excluded from validation.
- Reported account return, drawdown and Sharpe are diagnostic outputs, not validated performance. Ranking used the portfolio default blend rather than the strategy's EV-only setting, and the account calendar continued beyond the signal window.
- Nobody expected a pass — this was 30 stocks over one quarter. The real
  test uses ~1,100 stocks over many years with frozen rules.

## Bugs found and fixed during the run

| Problem | Fix |
|---|---|
| Engine scanned all 1,118 tickers against the case library every day | Only tickers actually in the library are now checked |
| Outcome cache re-read all ~58,000 rows for every single lookup | Cache reads are now filtered to just the tickers/dates needed |
| A stock gapping up at the open could exceed the 7% position cap | Such trades are now skipped with a reason instead of crashing |
| Stage-1 maths crashed on missing cost column | Crash fixed; separate cost reporting still needs work, because a missing cost column currently displays zero cost impact |

## What Version A does *not* tell us

- Whether the reversal idea has a real edge — the sample is far too small.
- Anything about the other four strategy ideas.
- Anything about the locked final test period (Oct 2025+), which remains
  untouched on purpose.

## Where the results live

- Signals: `reports/h1_sanity_candidates.parquet`
- Trades: `reports/h1_sanity_trades.parquet`
- Verdict: `data/quant.duckdb`, `experiments` table
- Script: `src/h1_sanity.py`
