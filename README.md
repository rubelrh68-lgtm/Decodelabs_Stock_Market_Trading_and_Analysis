Decodelabs Stock Market Trading & Analysis

Industrial Training Portfolio — DecodeLabs, Batch 2026 Track: Stock Market Trading and Analysis Intern

A four-project portfolio building a complete, end-to-end equity research and risk-management toolkit on three (later four) UK-listed equities — progressing from reading raw price action, to valuing a business independently of its market price, to combining both into a backtested trading engine, to finally stepping back and managing a diversified portfolio of capital rather than a single trade at a time.

Each project is self-contained in its own folder, with its own detailed README, full written report, and Jupyter notebook. This file gives the overall arc; see each project’s own README for full methodology, results, and limitations.

Assets Covered

Ticker	Company	Sector	Used in
AZN.L	AstraZeneca	Pharmaceuticals	Projects 1–4
GSK.L	GSK	Pharmaceuticals	Projects 1–4
SHEL.L	Shell	Energy	Projects 1–4
AUTO.L	Auto Trader Group	Technology / Consumer Platform	Project 4 only
Benchmark used throughout: FTSE 100 (^FTSE)

The Four Projects

Project 1 — Technical Analysis & Price Action

Treats price action as the observable footprint of market psychology. Builds a fully systematic, rule-based signal engine: candle anatomy and wick-to-body rejection ratios, support/resistance and trendline detection, EMA 50/200 trend regime, Wilder RSI(14) momentum, a three-gate “IPO Decision Engine” (price structure × trend × momentum), and ATR-based stop-loss/take-profit sizing with an 80/20 train/test backtest split. No discretionary judgement is used at the signal-generation stage — every entry is the output of an explicit, reproducible rule set.

Headline finding: GSK’s signal held up identically across both the training window and a genuinely out-of-sample test window spanning a real market correction — the strongest evidence in this project that the engine captures real structure rather than overfitting.

Project 2 — Fundamental Valuation & Financial Modelling

Ignores price action entirely and asks a different question: independent of where the market is pricing a stock right now, what is it actually worth? Builds a discounted cash flow (DCF) model from each company’s own fundamentals, using a CAPM-derived discount rate (UK risk-free rate + equity risk premium) and sector-adjusted beta, then applies a 30% margin of safety to produce a disciplined, conservative entry threshold for each stock.

Headline finding: all three stocks screened as undervalued against their DCF-derived intrinsic value even after the margin-of-safety discount — though the verdict is, as with any DCF, highly sensitive to the discount-rate and terminal-growth assumptions used.

Project 3 — Algorithmic Trading Strategy & Backtesting

Combines Project 1’s technical signals with Project 2’s fundamental valuation into a single three-gate, fully rules-based trading engine, removing discretionary human judgement from the trading decision entirely. A position can only open when the fundamental gate (Project 2), trend gate, and momentum gate (both Project 1) all agree simultaneously, and is closed only by a fixed, immutable 3:1 take-profit/stop-loss rule — validated with a proper trade-by-trade backtest simulation and chronological Walk-Forward Optimisation across three out-of-sample years.

Headline finding: GSK delivered the only result genuinely corroborated by out-of-sample testing; Shell, despite generating by far the most trades, produced the weakest risk-adjusted result of the three — trade frequency and trade quality are not the same thing.

Project 4 — Portfolio Optimization & Risk Management

Shifts entirely from single-trade decision-making to macro-level portfolio construction. Builds a diversified $100,000 simulated portfolio across four assets (adding Auto Trader Group, a technology/consumer platform stock, to test sector diversification), derives position weights systematically via inverse-Beta allocation, and verifies the result against two institutional gatekeeper constraints — Portfolio Beta below 1.0, and Total Portfolio Heat (worst-case simultaneous-stop loss) no greater than 8% of capital — before evaluating whether the resulting structure is a good investment via the Sharpe Ratio, not just a safe one.

Headline finding: the portfolio passed both risk gatekeepers comfortably under two independently-tested lookback windows, but produced a weak-to-negative Sharpe Ratio in both — direct evidence that a defensively-structured portfolio is not automatically a well-performing one.

How the Projects Build on Each Other

Project 1 (Technical)  ──┐
                         ├──► Project 3 (Combined Engine) ──► Project 4 (Portfolio)
Project 2 (Fundamental) ─┘
 
Project 3’s Gate A inherits its threshold directly from Project 2’s margin-of-safety price; Gates B and C inherit their trend/momentum logic directly from Project 1. Project 4 then steps back from any single trade-timing decision entirely, asking instead how capital should be allocated across multiple names at once.

Repository Structure

Decodelabs_Stock_Market_Trading_and_Analysis/
├── README.md                              (this file)
├── Project1_Technical_Analysis/
├── Project2_Fundamental_Valuation/
├── Project3_Algorithmic_Trading/
└── Project4_Portfolio_Optimization/
 
Each project folder contains its own report (PDF and/or DOCX), Jupyter notebook(s), and a project-specific README with full methodology, headline results, and limitations.

Requirements (all projects)

pip install yfinance pandas numpy matplotlib seaborn scipy
 
Tested on Python 3.13. Internet access is required on first run of any notebook to download market data via yfinance. Each notebook should be run with Kernel → Restart Kernel and Run All Cells rather than executing cells individually — later steps in every project depend on intermediate variables and DataFrames built earlier in the same notebook.

Disclaimer

This repository and all four projects within it were produced solely for educational purposes as part of the DecodeLabs Industrial Training Programme, Batch 2026. While real historical market data is used throughout, nothing in this repository constitutes financial advice, investment advice, or a recommendation to buy, sell, or hold any security.

All models, signals, valuations, backtests, and portfolio constructions presented across these four projects are the output of systematic, educational exercises built for training purposes only. None have been reviewed by a qualified financial advisor, none account for transaction costs, taxation, or any individual’s personal financial circumstances, risk tolerance, or investment objectives, and none should be relied upon to make any real trading or investment decision. Past and backtested performance is not indicative of future results. All trading and investing involves risk, including the risk of loss of capital. The author is not a licensed financial advisor, and anyone considering a real investment decision should seek independent advice from a qualified, regulated professional.

Acknowledgements

Built as part of the DecodeLabs Industrial Training Kit, Batch 2026.
