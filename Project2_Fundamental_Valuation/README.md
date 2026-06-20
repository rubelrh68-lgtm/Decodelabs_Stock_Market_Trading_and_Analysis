# Stock Market Trading & Analysis — Project 2: Fundamental Valuation & Financial Modelling

Industrial training project (DecodeLabs, Batch 2026) building a DCF-based intrinsic valuation model on UK equities, layered with a margin-of-safety framework to translate raw financials into a clear buy/hold/avoid verdict.

## Overview

Where Project 1 reads price action, Project 2 ignores it. This project asks a different question entirely: independent of where the market is currently pricing a stock, what is it actually *worth*, based on its own cash flows? A discounted cash flow (DCF) model is built from each company's fundamentals, and a margin of safety is applied to the resulting intrinsic value to produce a disciplined, conservative entry threshold — the same logic a value investor would apply before deploying capital.

**Assets analysed:** AstraZeneca (AZN.L), GSK (GSK.L), Shell (SHEL.L)
**Benchmark:** FTSE 100 (^FTSE)
**Data sources:** Yahoo Finance (yfinance) for market and financial statement data, SEC EDGAR for filings cross-reference

## What's in this folder

| File | Description |
|---|---|
| [`Project_2_Fundamental_Valuation_Report.pdf`](Project_2_Fundamental_Valuation_Report.pdf) | Full report — opens directly in-browser |
| [`Project_2_Fundamental_Valuation_Report.docx`](Project_2_Fundamental_Valuation_Report.docx) | Full report (download to view — GitHub does not preview .docx files in-browser) |
| [`Stock_Analysis_Project_2.ipynb`](Stock_Analysis_Project_2.ipynb) | Full valuation notebook, end to end |

## Methodology — DCF + Margin of Safety

1. **Forecast free cash flows** for each company using historical financial statements pulled via yfinance.
2. **Discount future cash flows** to present value using a risk-adjusted discount rate built from the UK gilt risk-free rate plus an equity risk premium.
3. **Apply a terminal growth rate** to estimate value beyond the explicit forecast horizon.
4. **Derive intrinsic value per share**, then apply a 30% margin of safety to set a conservative "fair entry" price — i.e. the price at which the stock would need to trade for the investment to have a meaningful buffer against forecasting error.
5. **Compare intrinsic value (with margin of safety) against the current market price** to produce a verdict.

**Market parameters used:**

| Parameter | Value |
|---|---|
| Risk-free rate (Rf) — UK gilt | 4.40% |
| Equity Risk Premium (ERP) | 5.50% |
| Terminal growth rate (g) | 2.5% |
| Margin of Safety (MoS) discount | 30% |

## A key data trap and how it was avoided

LSE-listed equities report prices in **pence**, not pounds, in raw OHLCV data pulled via yfinance. A manual P/E calculation using the raw LSE price against pound-denominated EPS inflates the resulting ratio by roughly 100x, producing a nonsensical valuation multiple. This project avoids that trap by using the `trailingPE` field from yfinance's `info` object directly, since it is already correctly currency-adjusted, rather than computing P/E manually from raw price and EPS fields.

## Headline results

| Stock | Verdict | Basis |
|---|---|---|
| AstraZeneca (AZN.L) | **STRONG BUY** | DCF intrinsic value with 30% margin of safety exceeds current market price |
| GSK (GSK.L) | **STRONG BUY** | DCF intrinsic value with 30% margin of safety exceeds current market price |
| Shell (SHEL.L) | **STRONG BUY** | DCF intrinsic value with 30% margin of safety exceeds current market price |

All three stocks screened as undervalued against their DCF-derived intrinsic value, even after applying a conservative 30% margin of safety — meaning the current market price sits comfortably below the fair-value threshold for all three names over the analysis period.

These verdicts are a single-model snapshot, not a guarantee: DCF output is highly sensitive to the discount rate, terminal growth assumption, and the quality of the underlying cash flow forecast. See [Limitations](#limitations) below.

## Requirements

```
pip install yfinance pandas numpy scipy
```

Tested on Python 3.13. Internet access is required on first run to download financial statement and market data via yfinance.

## Running the notebook

```
jupyter lab Stock_Analysis_Project_2.ipynb
```

As with Project 1, use Kernel → Restart Kernel and Run All Cells rather than running cells individually — later valuation steps depend on intermediate DataFrames built earlier in the notebook.

## Limitations

- **Single-scenario DCF:** the model presents one central-case valuation per stock rather than a distribution across multiple discount-rate/growth-rate scenarios. A formal sensitivity table (e.g. varying Rf ±1% and terminal growth ±0.5%) would give a more complete picture of how robust each STRONG BUY verdict is to assumption changes.
- **Terminal growth rate is a simplification:** a single 2.5% perpetual growth assumption is applied uniformly across all three companies, despite differing sector dynamics (pharmaceuticals vs. integrated energy).
- **This is a fundamental analysis project only** — it does not incorporate the technical entry/exit timing signals from [Project 1](../Project1_Technical_Analysis). A STRONG BUY verdict here indicates the stock is undervalued on fundamentals; it does not by itself indicate the optimal *timing* of entry, which is what Project 1's gate logic and Project 3's combined engine address.

## Disclaimer

This repository and the accompanying report were produced solely for educational purposes as part of the DecodeLabs Industrial Training Programme, Batch 2026. While real historical financial data is used for AstraZeneca (AZN.L), GSK (GSK.L), and Shell (SHEL.L), nothing in this repository constitutes financial advice, investment advice, or a recommendation to buy, sell, or hold any security.

The DCF model, margin-of-safety framework, and resulting verdicts presented here are the output of a fundamental valuation exercise built for training purposes only. They have not been reviewed by a qualified financial advisor and do not account for any individual's personal financial circumstances, risk tolerance, or investment objectives. Discounted cash flow valuations are inherently sensitive to forecasting assumptions and do not guarantee future returns. All investing involves risk, including the risk of loss of capital. The author is not a licensed financial advisor, and anyone considering a real investment decision should seek independent advice from a qualified, regulated professional.

## Acknowledgements

Built as part of the DecodeLabs Industrial Training Kit, Batch 2026.
