# DecodeLabs Stock Market Trading & Analysis

Industrial training portfolio (DecodeLabs, Batch 2026) — a three-project pipeline covering technical analysis, fundamental valuation, and algorithmic strategy backtesting on UK equities.

## Overview

This repository documents a connected, end-to-end analytical pipeline applied to three London Stock Exchange equities, building from pure price-action reading through to a fully backtested, walk-forward-validated trading engine:

**Assets analysed:** AstraZeneca (AZN.L), GSK (GSK.L), Shell (SHEL.L)
**Benchmark:** FTSE 100 (^FTSE)
**Period:** 1 January 2024 – 31 May 2026 (609 trading days)
**Data source:** Yahoo Finance via yfinance

Each project builds on the last:

| Project | Focus | Folder |
|---|---|---|
| **1. Technical Analysis & Price Action** | Reads raw OHLC structure, support/resistance, and EMA/RSI momentum to build a rule-based "IPO Decision Engine" (Input → Process → Output) for trade signal generation | [`Project1_Technical_Analysis/`](Project1_Technical_Analysis) |
| **2. Fundamental Valuation & Financial Modelling** | Independently of price action, values each company via discounted cash flow (DCF) with a margin-of-safety overlay to determine intrinsic worth | [`Project2_Fundamental_Valuation/`](Project2_Fundamental_Valuation) |
| **3. Algorithmic Trading Strategy & Backtesting** | Combines the Project 1 and Project 2 logic into a single systematic engine, stress-tested via Walk-Forward Optimisation | *(see repo root for notebook/report once added)* |

Each project folder contains its own detailed README with full methodology, results, requirements, and known limitations — see the links above for the in-depth write-up of each stage.

## Quick links to full reports

| Project | PDF Report | Notebook |
|---|---|---|
| Project 1 | [Report](Project1_Technical_Analysis/Project1_Technical_Analysis_Report.pdf) | [Notebook](Project1_Technical_Analysis/Decode_Stock_Analysis_Project_1.ipynb) |
| Project 2 | [Report](Project2_Fundamental_Valuation/Project_2_Fundamental_Valuation_Report.pdf) | [Notebook](Project2_Fundamental_Valuation/Stock_Analysis_Project_2.ipynb) |

## Headline results at a glance

**Project 1 (Technical, IPO Decision Engine):**

| Stock | Train win rate | Test win rate | Verdict |
|---|---|---|---|
| AstraZeneca | 0.0% | 0.0% | REVIEW |
| GSK | 100.0% | 100.0% | ROBUST |
| Shell | 36.4% | 33.3% | WATCH |

**Project 2 (Fundamental, DCF + 30% Margin of Safety):**

| Stock | Verdict |
|---|---|
| AstraZeneca | STRONG BUY |
| GSK | STRONG BUY |
| Shell | STRONG BUY |

Full context, methodology, and caveats for each result are in the respective project README — headline numbers above should not be read in isolation.

## Requirements

```
pip install yfinance pandas numpy matplotlib seaborn mplfinance scipy
```

Tested on Python 3.13. See each project's own README for project-specific setup notes.

## Disclaimer

This repository and all accompanying reports were produced solely for educational purposes as part of the DecodeLabs Industrial Training Programme, Batch 2026. While real historical market and financial data is used for AstraZeneca (AZN.L), GSK (GSK.L), and Shell (SHEL.L), nothing in this repository constitutes financial advice, investment advice, or a recommendation to buy, sell, or hold any security. All figures, signals, and verdicts are the output of training exercises and have not been reviewed by a qualified financial advisor. Past performance is not indicative of future results, and all investing involves risk of loss. Anyone considering a real investment decision should seek independent advice from a qualified, regulated professional.

## Acknowledgements

Built as part of the DecodeLabs Industrial Training Kit, Batch 2026.
