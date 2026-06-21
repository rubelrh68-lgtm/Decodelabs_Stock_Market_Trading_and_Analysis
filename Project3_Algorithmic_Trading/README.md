# Project 3: Algorithmic Trading Strategy & Backtesting

**DecodeLabs Industrial Training Kit — Batch 2026**
**Stock Market Trading and Analysis Intern**

A fully rules-based, emotionless algorithmic trading engine built for AstraZeneca (AZN.L), GSK (GSK.L), and Shell (SHEL.L), combining Project 1's technical signals and Project 2's fundamental valuation into a single three-gate entry system with an immutable 3:1 take-profit/stop-loss exit rule, validated by trade-by-trade backtesting and chronological Walk-Forward Optimisation.

---

## 📌 Project Goal

Per the DecodeLabs brief, this project removes discretionary human judgement from trading entirely by building:

1. A **strict if/else logic matrix** for trade entries
2. An **immutable if/else exit parameter** for both take-profit and stop-loss
3. A **backtest against historical time-series data** to establish whether a statistical edge exists

Every entry and exit in this engine is the deterministic output of a rule — never a discretionary call made in the moment.

---

## 🧠 Strategy Logic

### Entry: Three-Gate System

| Gate | Source | Condition |
|------|--------|-----------|
| **Gate A** — Fundamental | Project 2 | Price < Margin-of-Safety entry price (70% of DCF intrinsic value) |
| **Gate B** — Trend | Project 1 | EMA50 > EMA200 (bullish golden cross regime) |
| **Gate C** — Momentum | Project 1 | RSI(14) > 50, using true Wilder smoothing |

All three gates must pass **simultaneously** (logical AND). The resulting signal is shifted forward one trading day (`stance.shift(1)`) before being used to compute returns — the engine's primary defence against look-ahead bias.

### Exit: Fixed 3:1 Asymmetric Rule

- **Take-profit:** +9%
- **Stop-loss:** −3%
- **Reward-to-risk ratio:** 3:1
- **Mathematical break-even win rate:** 25%

Once a position opens, only these two fixed price levels can close it — never a discretionary override, and never the entry gates flipping back to non-signal.

---

## 🔁 Pipeline Overview

The notebook (`Stock_Market_Analysis_Project_3.ipynb`) runs as a 9-cell sequential pipeline:

| Step | Cell | What it does |
|------|------|---------------|
| 1 | Setup | Loads Project 2's final WACC and Margin-of-Safety outputs |
| 2 | Data | Downloads ~6 years of OHLCV data (2020–2025), forward-fill only (never backward-fill, to avoid look-ahead bias) |
| 3 | Features | Computes EMA50, EMA200, and true Wilder-smoothed RSI(14) |
| 4 | Gate Logic | Combines all three gates with `stance.shift(1)` |
| 5 | Exit Levels | Defines the fixed 3:1 TP/SL price levels |
| 6 | Backtest Engine | **Trade-by-trade simulation** — a sequential day-by-day loop tracking open positions, not a daily-return approximation |
| 7 | KPI Extraction | Sharpe, Max Drawdown, CAGR, Calmar, and **trade-log-based** win rate |
| 8 | Walk-Forward Optimisation | Three chronological out-of-sample cycles (2023, 2024, 2025), gated on a minimum of 3 closed trades per cycle |

> **Design note:** An earlier version of Step 6 approximated the exit rule by capping each day's return at +9%/−3% whenever the entry gates were active. This was found to misrepresent the strategy — it could not track whether a position was genuinely still open, nor produce a real trade count. The current version uses a proper sequential simulation that opens a position on signal, holds it (even across the gates turning back off) until price closes at or beyond the take-profit or stop-loss level, then logs the trade with its entry/exit dates and prices.

---

## 📊 Key Results (Full ~6-Year Backtest)

| Stock | Trades Closed | Take-Profit | Stop-Loss | Win Rate | Sharpe | CAGR |
|-------|:---:|:---:|:---:|:---:|:---:|:---:|
| **AstraZeneca** | 15 | 5 | 10 | 33.3% | 0.27 | 2.1% |
| **GSK** | 18 | 8 | 10 | 44.4% | **0.63** | **6.6%** |
| **Shell** | 43 | 11 | 32 | 25.6% | 0.04 | −0.4% |

All three win rates clear the strategy's 25% mathematical break-even threshold, but by very different margins. **GSK is the standout result** — the strongest Sharpe ratio, smallest drawdown, and the only stock whose edge is genuinely corroborated by out-of-sample Walk-Forward testing.

### Walk-Forward Optimisation (Out-of-Sample)

| Stock | Cycle | Test Year | Trades | Sharpe | CAGR |
|-------|:---:|:---:|:---:|:---:|:---:|
| AstraZeneca | 1–3 | 2023–2025 | 0 each | *Insufficient trades* | — |
| GSK | 2 | 2024 | 6 | 1.09 | 18.0% |
| GSK | 3 | 2025 | 4 | 0.89 | 11.7% |
| Shell | 1 | 2023 | 6 | 0.43 | 5.2% |
| Shell | 2 | 2024 | 3 | 0.30 | 2.5% |
| Shell | 3 | 2025 | 4 | 0.00 | −0.5% |

Cycles with fewer than 3 closed trades are **explicitly excluded** rather than reported with a misleading Sharpe ratio of 0.00. AstraZeneca traded too infrequently for any single calendar-year window to validate — a genuine limitation of trade frequency, documented as a finding rather than hidden.

### Live Engine Status (Most Recent Data)

| Stock | Gate A | Gate B | Gate C | Engine State |
|-------|:---:|:---:|:---:|:---:|
| AstraZeneca | ❌ FAIL | ✅ PASS | ✅ PASS | IDLE |
| GSK | ❌ FAIL | ✅ PASS | ✅ PASS | IDLE |
| Shell | ✅ PASS | ✅ PASS | ✅ PASS | **EXECUTE** |

AstraZeneca and GSK have both moved above their Project 2 margin-of-safety thresholds as prices rose over the most recent year of data — the engine correctly refuses to chase them despite a confirmed bullish trend. Shell shows a live signal with a position currently open, awaiting take-profit or stop-loss resolution.

---

## ⚠️ Key Limitations

- **Daily-close exit approximation** — no intraday OHLC data; stop-loss is checked before take-profit on any day both thresholds are technically crossed (a conservative convention).
- **Indicator-methodology sensitivity** — switching RSI from simple-rolling-mean to true Wilder smoothing measurably changed Shell's results (Sharpe 0.21 → 0.04), showing its edge is not robust to this choice.
- **Low trade frequency for AstraZeneca** — only 15 trades in ~6 years, zero in any single WFO test year.
- **Dependence on Project 2's valuation model** — Gate A inherits Project 2's DCF assumptions (sector-adjusted beta, 2.5% terminal growth) and would shift if those change.
- **No transaction costs or slippage modelled.**
- **Single-asset position sizing** — no portfolio-level risk budgeting across the three stocks.

See Section 7 of the full report for complete details.

---

## 🛠️ Tools & Environment

- **Development:** JupyterLab, Python (`pandas`, `numpy`, `yfinance`, `matplotlib`)
- **Report generation:** Node.js with the `docx` npm package, charts embedded as base64 PNGs
- **Data source:** `yfinance` (daily OHLCV, 2020-01-02 to 2025-12-31)

### Reproducing the Pipeline

```bash
# From this folder
jupyter lab Stock_Market_Analysis_Project_3.ipynb
# Kernel → Restart Kernel and Run All Cells
```

All outputs (CSVs in `data/`, charts in `charts/`) regenerate automatically.

---

## 📁 Folder Contents

```
Project_3/
├── Stock_Market_Analysis_Project_3.ipynb   # Full 9-step pipeline notebook
├── Project_3_Algorithmic_Trading_Report.docx  # Full written report
├── data/
│   ├── p3_closes.csv                       # Cleaned OHLCV data
│   ├── p3_kpis.csv                         # Strategy KPI summary
│   └── p3_wfo_results.csv                  # Walk-Forward Optimisation results
├── charts/
│   ├── p3_step6_equity_curves.png
│   └── p3_step8_wfo_curves.png
└── README.md                               # This file
```

---

## 🔗 Project Portfolio

This is **Project 3 of 4** in the DecodeLabs Stock Market Trading and Analysis portfolio:

1. **Project 1** — Technical Analysis & Price Action
2. **Project 2** — Fundamental Valuation & Financial Modelling
3. **Project 3** — Algorithmic Trading Strategy & Backtesting *(this project)*
4. **Project 4** — *(in progress)*

All four projects analyse the same three equities — AstraZeneca (AZN.L), GSK (GSK.L), and Shell (SHEL.L) — benchmarked against the FTSE 100, building toward a single coherent trading and analysis portfolio.

---

## ⚖️ Disclaimer

This project was built solely for educational purposes as part of the DecodeLabs Industrial Training Programme, Batch 2026. Nothing in this repository constitutes financial advice, investment advice, or a recommendation to buy, sell, or hold any security. Backtested performance is not indicative of future results. See the full report's Disclaimer section for complete terms.
