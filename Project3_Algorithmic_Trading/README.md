Stock Market Trading & Analysis — Project 3: Algorithmic Trading Strategy & Backtesting

Industrial training project (DecodeLabs, Batch 2026) building a fully rules-based, emotionless trading engine on UK equities, combining Project 1's technical signals with Project 2's fundamental valuation into a single three-gate entry system, validated by trade-by-trade backtesting and chronological Walk-Forward Optimisation.

Overview

Where Project 1 reads price action and Project 2 ignores it entirely in favour of intrinsic value, Project 3 brings both together and asks a different question: can a strategy that combines fundamental discipline with technical timing actually generate a statistical edge, tested honestly against real historical data? Every entry and exit in this engine is the deterministic output of a fixed rule — never a discretionary call made in the moment — and the backtest is built to expose, rather than hide, the limitations of the resulting strategy.

Assets analysed: AstraZeneca (AZN.L), GSK (GSK.L), Shell (SHEL.L)
Benchmark: FTSE 100 (^FTSE)
Data source: Yahoo Finance (yfinance) for ~6 years of daily OHLCV data (2020–2025)

What's in this folder

File | Description
--- | ---
<<<<<<< Updated upstream
[`Project_3_Algorithmic_Trading_Report.pdf`](Project_3_Algorithmic_Trading_Report.pdf) | Full report — opens directly in-browser
[`Project_3_Algorithmic_Trading_Report.docx`](Project_3_Algorithmic_Trading_Report.docx) | Full report (download to view — GitHub does not preview .docx files in-browser)
[`Stock_Market_Analysis_Project_3.ipynb`](Stock_Market_Analysis_Project_3.ipynb) | Full algorithmic trading notebook, end to end
=======
Project_3_Algorithmic_Trading_Report.pdf | Full report — opens directly in-browser
Project_3_Algorithmic_Trading_Report.docx | Full report (download to view — GitHub does not preview .docx files in-browser)
Stock_Market_Analysis_Project_3.ipynb | Full algorithmic trading notebook, end to end
>>>>>>> Stashed changes

Methodology — Three-Gate Entry, Fixed Exit, Trade-by-Trade Backtest

- Load Project 2's final margin-of-safety entry prices and WACC values as the fundamental gate threshold for each stock.
- Combine three independent signals into a single entry rule, requiring all three to pass simultaneously (logical AND):
  - Gate A (fundamental, from Project 2): current price below the margin-of-safety entry price.
  - Gate B (trend, from Project 1): 50-day EMA above the 200-day EMA.
  - Gate C (momentum, from Project 1): 14-day RSI above 50, using true Wilder exponential smoothing.
- Shift the combined signal forward by one trading day before computing returns, the project's primary defence against look-ahead bias.
- Apply a fixed, immutable 3:1 take-profit/stop-loss exit (+9% / −3%) — once a position opens, only these two price levels can close it, never a discretionary override.
- Simulate the strategy trade-by-trade with a sequential day-by-day loop, tracking real entry and exit prices rather than approximating returns with a daily cap.
- Validate with chronological Walk-Forward Optimisation across three out-of-sample years (2023, 2024, 2025), excluding any cycle with fewer than three closed trades rather than reporting a misleading zero.

Market parameters used:

Parameter | Value
--- | ---
Take-profit | +9%
Stop-loss | −3%
Reward-to-risk ratio | 3:1
Mathematical break-even win rate | 25%
Backtest window | 2020-01-02 to 2025-12-31 (~6 years)

A key design decision and how it was made

An earlier version of the backtest engine approximated the exit rule by capping each day's market return at +9%/−3% whenever the entry gates were active. This was found to misrepresent the strategy: it allowed the simulated position to reset every day regardless of whether a real trade would still be open, and it could not produce a genuine trade count, entry price, or exit price for reporting. This project avoids that trap by using a proper sequential, trade-by-trade simulation instead — a position stays open, even after the entry gates flip back off, until price genuinely closes at or beyond the fixed take-profit or stop-loss level.

Headline results

Stock | Trades Closed | Win Rate | Sharpe | CAGR | Verdict
--- | --- | --- | --- | --- | ---
AstraZeneca (AZN.L) | 15 | 33.3% | 0.27 | 2.1% | Modest edge, unvalidated out-of-sample
GSK (GSK.L) | 18 | 44.4% | 0.63 | 6.6% | Strongest result, confirmed by Walk-Forward testing
Shell (SHEL.L) | 43 | 25.6% | 0.04 | −0.4% | Weakest risk-adjusted result, edge sensitive to methodology

All three win rates clear the strategy's 25% mathematical break-even threshold implied by the 3:1 reward-to-risk ratio, but by very different margins. GSK delivered the strongest result on every risk-adjusted measure and is the only stock whose edge is genuinely corroborated by out-of-sample Walk-Forward Optimisation (Sharpe 1.09 and 0.89 in its two valid test cycles). Shell, despite generating by far the most trading activity, produced the weakest risk-adjusted result of the three.

These results are a single-strategy snapshot, not a guarantee: backtest performance is sensitive to the exact indicator methodology used, the underlying fundamental valuation assumptions inherited from Project 2, and the absence of transaction costs. See Limitations below.

Requirements

pip install yfinance pandas numpy matplotlib

Tested on Python 3.13. Internet access is required on first run to download OHLCV data via yfinance.

Running the notebook

jupyter lab Stock_Market_Analysis_Project_3.ipynb

As with Projects 1 and 2, use Kernel → Restart Kernel and Run All Cells rather than running cells individually — the trade-by-trade backtest, KPI extraction, and Walk-Forward Optimisation steps all depend on intermediate DataFrames built earlier in the notebook.

Limitations

- Daily-close exit approximation: the backtest evaluates take-profit and stop-loss against daily closing prices only, since the underlying data does not include intraday highs and lows. Stop-loss is checked first on any day both thresholds are technically crossed, a conservative convention rather than an observed fact.
- Indicator-methodology sensitivity: switching the RSI(14) calculation from a simple rolling-mean approximation to true Wilder exponential smoothing measurably changed Shell's results (full-window Sharpe ratio moved from 0.21 to 0.04), showing its edge is not robust to small, defensible changes in a single input indicator.
- Low trade frequency for AstraZeneca: with only 15 closed trades across six years, and zero trades closed within any single calendar-year Walk-Forward window, AstraZeneca's result has not been corroborated by genuine out-of-sample testing.
- Dependence on Project 2's valuation model: Gate A inherits its threshold directly from Project 2's DCF-derived margin-of-safety price. Any future revision to Project 2's assumptions would shift Gate A's threshold and could change every trade-timing result in this report.
- No transaction costs or slippage are modelled. Given Shell's 43-trade count in particular, even modest per-trade costs could meaningfully erode the already-thin edge reported.
- Single-asset position sizing: each signal is modelled as a full, undiversified position with no portfolio-level risk budgeting across the three stocks — appropriate for isolating each stock's standalone signal quality, but not a directly deployable portfolio strategy as constructed.

Disclaimer

This repository and the accompanying report were produced solely for educational purposes as part of the DecodeLabs Industrial Training Programme, Batch 2026. While real historical market data is used for AstraZeneca (AZN.L), GSK (GSK.L), and Shell (SHEL.L), nothing in this repository constitutes financial advice, investment advice, or a recommendation to buy, sell, or hold any security.

The trading engine, backtest results, and Walk-Forward Optimisation presented here are the output of a systematic, educational algorithmic-trading exercise built for training purposes only. They have not been reviewed by a qualified financial advisor and do not account for transaction costs, taxation, or any individual's personal financial circumstances, risk tolerance, or investment objectives. Backtested performance is not indicative of future results and is subject to well-documented biases that this project has attempted, but cannot guarantee, to fully control for. All trading and investing involves risk, including the risk of loss of capital. The author is not a licensed financial advisor, and anyone considering a real trading or investment decision should seek independent advice from a qualified, regulated professional.

Acknowledgements

Built as part of the DecodeLabs Industrial Training Kit, Batch 2026.
