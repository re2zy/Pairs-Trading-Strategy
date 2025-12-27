# Pairs Trading Strategy on Nifty 100

## Overview

This repository contains a **statistical arbitrage pairs trading strategy** applied to a subset of the Nifty 100 stocks. The strategy identifies pairs of stocks that are cointegrated and trades the spread between them when it deviates from its historical mean.  

The main idea is:
- Identify stock pairs that move together historically (high correlation + cointegration).  
- Calculate the spread between the two stocks.  
- Enter a long-short trade when the spread deviates significantly from its mean (z-score threshold).  
- Exit when the spread reverts towards the mean.  
- Scale positions to a target volatility to manage risk.

---

## Methodology

The strategy follows these steps:

1. **Data Download & Cleaning**  
   - Daily adjusted close prices for 97 Nifty 100 stocks from 2019–2025 are downloaded using `yfinance`.  
   - Stocks with missing or zero variance data are removed.  

2. **Candidate Pair Selection**  
   - Calculate daily returns for all stocks.  
   - Select candidate pairs with Pearson correlation above a threshold (0.60).  
   - Limit to top 400 pairs by correlation.  

3. **Cointegration & Half-life Filtering**  
   - For each candidate pair, perform a linear regression to estimate beta.  
   - Calculate the spread and test for stationarity using the Augmented Dickey-Fuller (ADF) test.  
   - Calculate the spread’s mean-reversion half-life.  
   - Keep pairs with ADF p-value < 0.08 and half-life between 2 and 80 days.  

4. **Spread Volatility & Scoring**  
   - Compute normalized spread volatility.  
   - Score pairs by spread volatility divided by half-life.  
   - Select the top-K pairs based on this score.  

5. **Backtesting**  
   - Implement a z-score trading logic:  
     - **Entry:** Open long/short positions when z-score exceeds ±1.8.  
     - **Exit:** Close positions when z-score reverts within ±0.75.  
     - Include transaction costs (0.1%).  
   - Calculate daily PnL and cumulative equity per pair.  

6. **Portfolio Construction**  
   - Aggregate per-pair PnLs into a portfolio.  
   - Apply **volatility targeting** to achieve a consistent portfolio risk.  
   - Compute portfolio metrics: Sharpe ratio, maximum drawdown, average daily PnL, total trades.  

---

## Results (2025 Out-of-Sample)

| Metric | Value |
|--------|-------|
| Portfolio Sharpe | 0.511 |
| Portfolio Max Drawdown | -0.19 |
| Average Daily PnL | 0.0001 |
| Total Pairs Traded | 9 |
| Total Trades | 6,289 |
| Average Pair Sharpe | 0.297 |
| Non-zero PnL Days | 1,647 |
| Total Trading Days | 1,667 |

> The results reflect out-of-sample performance for 2025 using pairs selected on 2019–2024 data.

---

## How to Run

1. Clone the repository:  
   ```bash
   git clone <repo-url>
   cd <repo-folder>
