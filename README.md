# Cluster-Based Pairs Trading Strategy (NIFTY100)

## Purpose

This project implements a **realistic market-neutral pairs trading strategy**
on Indian equities (NIFTY100).  
The goal is to demonstrate a **robust quantitative research pipeline**
that avoids common backtesting pitfalls such as look-ahead bias,
implicit leverage, and overtrading.

The strategy focuses on **statistical mean reversion** using:
- Agglomerative clustering for pair discovery
- Cointegration for spread validity
- Z-score–based trading signals
- Daily top-K pair selection
- Volatility-controlled portfolio construction

---

## Data

- **Universe:** NIFTY100 stocks  
- **Source:** Yahoo Finance (`yfinance`)
- **Time Period:**
  - Training / Selection: **2019–2024**
  - Out-of-Sample Test: **2025**

---

## Strategy Overview

### 1. Pair Discovery (Training Only)
- Compute daily returns
- Standardize return vectors
- Apply **Agglomerative Clustering (Ward linkage)**
- Generate stock pairs within clusters
- Rank by similarity
- Cap candidate universe

> Clustering is used only for **pair selection**, never for trading signals.

---

### 2. Cointegration Filter
- Apply Engle–Granger test on training data
- Retain only stationary spreads
- Reduces false mean-reversion signals

---

### 3. Spread Construction
For each pair:
Spread = log(P_A) - β * log(P_B)

- β estimated via rolling OLS
- Used strictly as a **hedge ratio**

---

### 4. Signal Generation (Out-of-Sample)
- Rolling mean & standard deviation
- Z-score computation
- Entry on large deviations
- Exit on reversion
- Transaction costs included
- Signals are **lagged** (no look-ahead)

---

### 5. Daily Top-K Pair Selection
- Rank pairs daily by |z-score|
- Trade **only top 15 pairs**
- Optional cluster diversification cap

This step prevents overtrading and Sharpe inflation.

---

### 6. Portfolio Construction
- Equal-weighted across active pairs
- Volatility targeting
- Leverage cap
- Aggregate portfolio PnL

---

## Final Out-of-Sample Performance (2025)

- **Portfolio Sharpe:** ~1.5  
- **Max Drawdown:** ~8%  
- **Max active pairs per day:** 15  
- **Candidate universe:** ~120 pairs  

These results are intentionally conservative and realistic.

---

## Disclaimer

This project is for **research and educational purposes only**  
and does not constitute investment advice.

## Installation

Clone the repository and install dependencies:

```bash
pip install -r requirements.txt

