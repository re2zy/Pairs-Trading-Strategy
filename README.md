
# Market-Neutral Statistical Pairs Trading Strategy

A systematic **pairs trading strategy** applied to Indian equities (NIFTY universe),
using **cointegration**, **mean-reversion half-life filtering**, and
**portfolio-level volatility targeting**.

Designed to demonstrate **quantitative research, statistical validation,
and risk-aware portfolio construction**.

---

## Strategy Summary (TL;DR)

- Identify statistically related stock pairs
- Trade deviations in their price spread
- Exploit mean reversion using z-score signals
- Control risk using volatility targeting

---

## Methodology

**Universe**
- NIFTY 100 stocks (2019–2024)

**Pair Selection**
- Correlation pre-filter
- Engle–Granger cointegration test
- ADF test on spread
- Mean-reversion half-life estimation

**Trading Logic**
- Z-score based entries/exits
- Market-neutral long–short exposure
- Transaction costs included

**Risk Management**
- Inverse volatility pair weighting
- Portfolio-level volatility targeting
- Leverage capped to prevent overexposure

---

## Results (Out-of-Sample Backtest)
Portfolio Sharpe Ratio: 0.93
Maximum Drawdown: -27%
Pairs Traded: 15
Total Trades: ~7,200
Average Pair Sharpe: 0.18


---

## Tech Stack
- Python
- NumPy, Pandas
- Statsmodels
- yFinance
- Matplotlib

---

## Why This Project Matters
- Demonstrates statistical arbitrage concepts
- Applies econometrics to real financial data
- Shows understanding of risk-adjusted returns
- Emphasizes portfolio-level controls, not just signals

---

## Disclaimer
Educational and research purposes only. Not financial advice.


