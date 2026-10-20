# Project Roadmap

Where the project is, in plain language. Phases follow the approved
requirements document (v6, `docs/20261016-2315-Requirements...v6.md`).

## Done

| Phase | What it was | Status |
|---|---|---|
| **P0 — Data audit** | Checked every price row from the Flight service. Extended history back to 2010. Loaded earnings dates for 1,072 tickers into our local DB. Documented every known data flaw. | ✅ Complete |
| **P1 — Trade simulator** | One-trade simulator with real fills, gaps, splits, dividends, earnings exclusions, costs. | ✅ Complete |
| **P2 — Baselines + walk-forward** | "Do nothing special" benchmarks and the year-by-year test harness. | ✅ Complete |
| **P3 — Analog engine** | Finds similar past cases, counts independent market events, no look-ahead. | ✅ Complete |
| **P4 — Portfolio simulator** | Real account limits: position sizing, exposure caps, dividend cash. | ✅ Complete |
| **P5 — Promotion gates** | Three-stage pass/fail pipeline + permanent experiment ledger + statistics controls for "testing many things at once". | ✅ Complete |
| **External review** | Independent code review — **APPROVED** after 6 rounds. | ✅ Complete |
| **Version A sanity run** | First end-to-end run on real data (Q1 2023, 30 tickers). Pipeline works; strategy not yet proven. | ✅ Complete — see `docs/wiki/Version-A.md` |

These completed items describe implementation, not proof of a profitable strategy. External review approved the earlier implementation bundle; later sanity-run fixes have not been re-reviewed.

## Next up

Before more research, correct Version A's validation inputs (filled trades only), separate cost reporting, align baseline dates and ranking settings, and verify account calculations. Then freeze training windows and parameters before collecting new evidence.

| Step | What needs to happen |
|---|---|
| **Speed pass** | The matching engine takes ~20–30 s per signal day. Vectorize it so a full-universe run is practical. |
| **Real H1 evidence run** | Full tradable universe (~1,100 tickers), proper fixed 5-year training windows, frozen parameter grid, all walk-forward years. |
| **Five hypotheses** | Run the remaining four strategy ideas through the same pipeline. |
| **Multiplicity control** | Once many variants exist, apply the deflated-Sharpe / multiple-testing rules before anything is believed. |
| **Stage 3 holdout** | Only after strategy choices are frozen: unlock the untouched Oct-2025+ test period. |

## Later

| Step | What it is |
|---|---|
| **Daily signal generator** | Pre-market job that produces the Excel file of ranked signals. |
| **4–6 week shadow period** | Generate signals daily but trade nothing; compare predicted vs real fills. |
| **IBKR UK live deployment** | Only after shadow results match the backtest within agreed tolerance. |

## The rule that governs everything

> A signal is only ever used live if it came from a method that passed the
> full backtest and still showed a profit **after** all costs. Anything less
> stays in the lab.
