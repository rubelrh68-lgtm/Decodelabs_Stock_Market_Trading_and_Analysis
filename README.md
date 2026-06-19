Stock Market Trading & Analysis — Project 1: Technical Analysis & Price Action

Industrial training project (DecodeLabs, Batch 2026) building a systematic, rule-based technical analysis and trade-signal engine on UK equities — from raw OHLC interpretation through to a backtested, risk-managed execution model.

Overview

This project treats price action as the observable footprint of market psychology and builds a fully systematic decision pipeline on top of it: read the candles → map the structure → confirm the trend → confirm the momentum → gate the signal → size and risk-manage the trade. No discretionary judgment is used at the signal-generation stage — every entry is the output of an explicit, reproducible rule set.

Assets analysed: AstraZeneca (AZN.L), GSK (GSK.L), Shell (SHEL.L) Benchmark: FTSE 100 (^FTSE) Period: 1 January 2024 – 31 May 2026 (609 daily candles per ticker) Data source: Yahoo Finance via yfinance

What’s in this repo

| File | Description |
|---|---|
| [`Project1_Technical_Analysis_Report.pdf`](Project1_Technical_Analysis_Report.pdf) | Full report — opens directly in-browser |
| [`Project1_Technical_Analysis_Report.docx`](Project1_Technical_Analysis_Report.docx) | Full report (download to view — GitHub does not preview .docx files in-browser) |
| [`Decode_Stock_Analysis_Project_1.ipynb`](Decode_Stock_Analysis_Project_1.ipynb) | Full analysis notebook — all 7 steps, end to end |
| `data/` | Cached OHLCV CSVs per ticker (generated on first run) |
| `charts/` | Exported chart PNGs referenced in the report |

Methodology — The IPO Decision Engine

The core framework is a three-gate Input → Process → Output filter. A trade signal is only valid when all three gates agree:

Gate	Condition	Purpose
Gate A	Price within 2% of the 50-day EMA	Structural — price at a meaningful level
Gate B	50-EMA above 200-EMA (bullish regime)	Trend — correct macro direction
Gate C	RSI(14) above 50	Momentum — confirms the move isn’t exhausted
If any gate fails, the system output is IDLE — no trade. This Gatekeeper Rule is deliberately strict: a missed opportunity is treated as preferable to a low-probability entry.

Notebook structure (7 steps)

Raw OHLC interpretation — candle anatomy, descriptive statistics, return distributions, volume, correlation
Wick-to-body ratio (R_wb) — quantifies price rejection per candle: (upper wick + lower wick) / body
Support, resistance & trendlines — pivot detection (10-day window), level clustering, linear-regression trend channels
EMA 50/200 indicators — regime detection, Golden Cross / Death Cross, ATR-14 volatility
RSI momentum filter — Wilder RSI(14), 50-threshold crossovers, divergence detection
IPO Decision Engine — the three-gate confluence logic described above
Trade levels & risk management — ATR-based stop-loss/take-profit (1.5x / 3.0x, R:R 2.0), an 80/20 train/test backtest, and a three-protocol circuit-breaker system (15-minute hard stop, half-size rule, session-end trigger)
Backtest setup

To distinguish genuine predictive signal from curve-fitting, the dataset is split chronologically:

Train: 1 Jan 2024 → 4 Dec 2025 (490 days, 80%)
Test: 5 Dec 2025 → 31 May 2026 (119 days, 20%, fully out-of-sample)
Gate parameters are derived once from the full-period logic and applied unchanged to both windows — the test result is a genuine forward simulation, not a fitted one. The test window deliberately spans the February 2026 market peak and the subsequent correction, so it functions as a real regime-change stress test rather than a quiet continuation of the training trend.

Account assumptions: £10,000 account · 1% risk per trade (£100) · 2% daily loss limit (£200) · stop-loss at 1.5×ATR(14) · take-profit at 3.0×ATR(14) → fixed 2:1 reward-to-risk.

Headline results (most recent run)

Stock	Train signals	Train win rate	Train P&L	Test signals	Test win rate	Test P&L	Combined P&L	Verdict
AstraZeneca	5	0.0%	−£500	2	0.0% (open)	£0	−£500	REVIEW
GSK	3	100.0%	+£600	2	100.0%	+£400	+£1,000	ROBUST
Shell	14	36.4%	+£100	4	33.3%	−£0	+£100	WATCH
GSK’s identical win rate across both train and test windows — including through a test period containing the sharpest correction in the dataset — is the strongest evidence in this project that the engine is capturing genuine structural logic rather than overfitting to the training period.

As of 31 May 2026, all three stocks sit in a confirmed bullish EMA regime (Gate B: 100% pass rate for all three during the test period) but fail Gate C (RSI below 50), so the engine currently returns IDLE for all three. AstraZeneca is closest to a full signal, already passing both Gate A and Gate B. Shell fails both Gate A and Gate C currently, with Gate B its only passing condition.

Requirements

pip install yfinance pandas numpy matplotlib seaborn mplfinance
 
Tested on Python 3.13. Internet access is required on first run to download OHLCV data via yfinance; subsequent runs can reuse the cached CSVs in data/.

Running the notebook

jupyter lab Decode_Stock_Analysis_Project_1.ipynb
 
Run all cells top to bottom (Kernel → Restart Kernel and Run All Cells) rather than executing cells individually out of order. Several later steps depend on columns added by earlier ones (R_wb, EMA, RSI, regime, gate flags), so an out-of-order run will throw KeyError/NameError even though the underlying logic is correct.

Known limitations

R_wb denominator floor (Step 2): the wick-to-body ratio uses a denominator floor of 1% of that day’s High-Low range to avoid dividing by zero on doji candles. For a true doji, this floor causes the ratio to collapse to almost exactly 100 regardless of that day’s actual range, since the range cancels out of the formula. This means the “Top 10 highest rejection” ranking for AstraZeneca and GSK is dominated by doji ties rather than a fully differentiated ranking; Shell’s ranking is more informative since it includes a genuine spread of non-doji values. This does not affect any EMA, RSI, gate, or backtest logic, which are computed independently of R_wb.
Two extreme intraday wicks are genuine market events, not data errors: AstraZeneca’s 20 May 2026 session and GSK’s 19 May 2026 session both show a sharp intraday low (~14% and ~10% below the day’s open respectively) with a full recovery by close. These were verified directly against the underlying OHLC values and are real single-session liquidity flushes, not corrupted data — they are shown unfiltered in the candlestick charts.
Two chart cells (Step 2 candle-type boxplot, Step 7 R:R distribution) may render only console warnings with no figure in some notebook exports, despite valid underlying plotting code — likely an artifact-capture quirk in the notebook-to-HTML conversion rather than a logic error.
This is a technical analysis project only — no fundamental valuation, earnings, or macro factors are considered. Signals should be cross-referenced with fundamental analysis (see Project 2) before any capital is committed.

Disclaimer

This repository and the accompanying report were produced solely for educational purposes as part of the DecodeLabs Industrial Training Programme, Batch 2026. While real historical market data is used for AstraZeneca (AZN.L), GSK (GSK.L), and Shell (SHEL.L), nothing in this repository constitutes financial advice, investment advice, or a recommendation to buy, sell, or hold any security.

The IPO Decision Engine, gate logic, trade levels, and backtested performance figures presented here are the output of a systematic technical analysis exercise built for training purposes only. They have not been reviewed by a qualified financial advisor and do not account for any individual’s personal financial circumstances, risk tolerance, or investment objectives. Past performance, including all win rates and P&L figures shown, is not indicative of future results. All trading involves risk, including the risk of loss of capital. The author is not a licensed financial advisor, and anyone considering a real investment decision should seek independent advice from a qualified, regulated professional.

Acknowledgements

Built as part of the DecodeLabs Industrial Training Kit, Batch 2026.
