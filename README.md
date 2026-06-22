# Stock Market Trading & Analysis — Project 4: Portfolio Optimization & Risk Management

Industrial training project (DecodeLabs, Batch 2026) building a diversified, risk-constrained simulated portfolio on UK equities — shifting from single-trade decision-making (Projects 1–3) to macro-level capital allocation, systemic risk measurement, and institutional gatekeeper verification.

## Overview

Where Projects 1–3 each analysed one stock at a time, Project 4 asks a different question entirely: how should capital be split across *several* stocks at once so that their risks partially offset one another, and how do you measure the resulting structure's exposure to the broader market as a single number rather than security by security? A $100,000 simulated portfolio is built across four assets, weighted systematically by each asset's Beta rather than by guesswork, and verified against two hard institutional risk constraints before asking the more important question: is this defensively-structured portfolio actually a *good* investment, or merely a *safe* one?

**Assets analysed:** AstraZeneca (AZN.L), GSK (GSK.L), Shell (SHEL.L), Auto Trader Group (AUTO.L)
**Benchmark:** FTSE 100 (^FTSE)
**Data source:** Yahoo Finance (yfinance) for ~6 years of weekly OHLCV data (2020–2026), with a 3-year window (2023–2026) run as a robustness check

## What's in this folder

| File | Description |
|---|---|
| [`Project4_Portfolio_Optimization_Report.pdf`](Project4_Portfolio_Optimization_Report.pdf) | Full report — opens directly in-browser |
| [`Project4_Portfolio_Optimization_Report.docx`](Project4_Portfolio_Optimization_Report.docx) | Full report (download to view — GitHub does not preview .docx files in-browser) |
| [`Project4_Portfolio_Optimization_6y.ipynb`](Project4_Portfolio_Optimization_6y.ipynb) | Primary notebook — full pipeline run on the 6-year (2020–2026) dataset |
| [`Project4_Portfolio_Optimization_3y.ipynb`](Project4_Portfolio_Optimization_3y.ipynb) | Robustness-check notebook — identical pipeline run on a 3-year (2023–2026) window |

## Methodology — Diversify, Weight, Size, Verify, Evaluate

1. **Define inputs:** a $100,000 simulated capital base spread across four assets chosen for minimal sector overlap (two pharmaceuticals, one energy, one technology/consumer platform).
2. **Verify the diversification thesis empirically** rather than assuming it — a full pairwise correlation matrix confirms whether the chosen assets actually move independently of one another.
3. **Calculate covariance and Beta** for each asset against the FTSE 100 benchmark, mapping how much each holding amplifies or dampens market-wide moves.
4. **Derive portfolio weights systematically** using inverse-Beta allocation (bounded 10%–35% per asset, so the optimiser cannot collapse the portfolio into a concentrated bet on a single low-Beta asset).
5. **Size each position under a fixed-fractional 1% risk rule** — stop-losses derived from each asset's own historical volatility, not a fixed arbitrary percentage.
6. **Verify two gatekeeper constraints:** Portfolio Beta must be below 1.0, and Total Portfolio Heat (the worst-case simultaneous-stop loss) must not exceed 8% of capital.
7. **Evaluate risk-adjusted return via the Sharpe Ratio**, to test whether passing the gatekeepers also means the portfolio performs well — or merely that it is defensively structured.

**Market parameters used:**

| Parameter | Value |
|---|---|
| Total simulated capital | $100,000 |
| Maximum risk per position | 1% of capital ($1,000) |
| Portfolio Beta ceiling | < 1.0 |
| Portfolio Heat ceiling | ≤ 8% |
| Weight bounds per asset | 10%–35% |
| Risk-free rate (UK proxy) | 4.40% |

## A key tension and how it was reported

Optimising purely for low portfolio Beta does not produce a good Sharpe Ratio, because Beta measures sensitivity to market moves, not expected return. This project's inverse-Beta weighting comfortably passed both gatekeeper constraints under every window tested, yet produced a weak-to-negative Sharpe Ratio in every case. Rather than treating this as a flaw to be optimised away or concealed behind a more flattering lookback window, this project reports it directly: a portfolio satisfying defensive risk constraints is not automatically a well-performing one, and Beta, Heat, and Sharpe Ratio each measure a materially different property of a portfolio.

## Headline results

| Window | Portfolio Beta | Total Portfolio Heat | Sharpe Ratio | Verdict |
|---|---|---|---|---|
| 6-year (primary) | 0.859 | 6.76% | 0.007 | Both gatekeepers PASS; weak risk-adjusted return |
| 3-year (robustness check) | 0.857 | 6.50% | −0.084 | Both gatekeepers PASS; negative risk-adjusted return |

Both lookback windows clear the Beta and Heat gatekeepers with genuine margin. AstraZeneca and GSK's Beta classifications invert entirely (defensive ↔ aggressive) depending on which window is used — a meaningful finding about the stability of Beta as a measurement, not a calculation error. Auto Trader Group, the portfolio's largest single holding by weight, is the main driver of the weak Sharpe Ratio: it is the most defensively-Beta'd asset in the portfolio, yet produced a negative annualised historical return over the period tested.

These results are a single-portfolio snapshot, not a guarantee: Beta and correlation estimates are sensitive to the lookback window chosen, and the resulting weights, gatekeeper verdicts, and Sharpe Ratio would all shift under a different window or benchmark. See [Limitations](#limitations) below.

## Requirements

```
pip install yfinance pandas numpy matplotlib seaborn
```

Tested on Python 3.13. Internet access is required on first run to download OHLCV data via yfinance.

## Running the notebooks

```
jupyter lab Project4_Portfolio_Optimization_6y.ipynb
```

As with Projects 1–3, use **Kernel → Restart Kernel and Run All Cells** rather than running cells individually — the Beta calculation, weight derivation, position sizing, and gatekeeper verification steps all depend on intermediate variables built earlier in the notebook. `Project4_Portfolio_Optimization_3y.ipynb` follows the identical pipeline with `YEARS_OF_HISTORY` set to 3.

## Limitations

- **Single-period weight optimisation:** portfolio weights are derived once from the chosen lookback window's Beta estimate and held static, with no periodic rebalancing schedule or drift-tolerance trigger modelled.
- **Beta estimate instability:** as shown directly in the robustness check, AstraZeneca and GSK's Beta classifications invert entirely between the 6-year and 3-year windows, indicating Beta as estimated here is a window-dependent quantity rather than a stable property of the underlying stock.
- **Weak risk-adjusted return:** the Sharpe Ratio is materially below levels typically considered acceptable for a real capital allocation under either window tested, and turns negative under the 3-year check.
- **No transaction costs or slippage** are modelled in the position-sizing or allocation calculations.
- **Static stop-loss assumption:** stop-loss levels are derived once from historical volatility at construction time and are not dynamically updated (e.g. via a trailing or ATR-adjusted mechanism, as used in Project 1).
- **Benchmark choice:** Beta is calculated against the FTSE 100 rather than the S&P 500 used in the brief's own illustrative example, for consistency with Projects 1–3. Results would differ under an S&P 500 benchmark.
- **Four-asset concentration:** a genuinely diversified institutional portfolio would typically span considerably more than four holdings and more than three economic sectors. This project's scale is appropriate for an educational exercise illustrating the underlying mechanics, not a production-scale capital allocation.

## Disclaimer

This repository and the accompanying report were produced solely for educational purposes as part of the DecodeLabs Industrial Training Programme, Batch 2026. While real historical market data is used for AstraZeneca (AZN.L), GSK (GSK.L), Shell (SHEL.L), and Auto Trader Group (AUTO.L), nothing in this repository constitutes financial advice, investment advice, or a recommendation to buy, sell, or hold any security.

The portfolio construction, Beta calculations, position-sizing methodology, and gatekeeper verifications presented here are the output of a systematic, educational portfolio-management exercise built for training purposes only. They have not been reviewed by a qualified financial advisor and do not account for transaction costs, taxation, or any individual's personal financial circumstances, risk tolerance, or investment objectives. Historical Beta, correlation, and return calculations are not indicative of future results and are subject to well-documented limitations, including the window-dependent instability demonstrated directly in this project. All trading and investing involves risk, including the risk of loss of capital. The author is not a licensed financial advisor, and anyone considering a real investment decision should seek independent advice from a qualified, regulated professional.

## Acknowledgements

Built as part of the DecodeLabs Industrial Training Kit, Batch 2026.
