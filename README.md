# Cluster-Based Pairs Trading Strategy (Agglomerative Clustering)

## Overview

This project implements a **cluster-based statistical arbitrage strategy** for equity pairs trading using **Agglomerative Clustering** instead of traditional cointegration tests.

The core idea is to:
- Group stocks with **similar return behavior** using unsupervised learning
- Trade **temporary divergences** between closely related stocks
- Control risk using **daily pair selection, cluster caps, minimum holding periods, and volatility targeting**
- Evaluate performance strictly **out-of-sample**

The strategy is designed as a **realistic research backtest**, explicitly addressing common sources of overfitting such as:
- Lookahead bias reminding
- Pair overcrowding
- Excessive leverage
- Unrealistic execution assumptions

---

## Strategy Summary (High Level)

1. **Universe**: NIFTY 100 equities
2. **Training Period**: 2019–2024 (pair selection & parameter estimation)
3. **Testing Period**: 2025 (fully out-of-sample)
4. **Pair Selection**: Agglomerative clustering on historical returns
5. **Signal**: Z-score of log-price spread
6. **Execution**: Daily top-15 strongest signals with cluster diversification
7. **Risk Control**:
   - Minimum holding period
   - Volatility targeting
   - Hard leverage cap

---

## Detailed Strategy Explanation

### 1. Data Collection & Cleaning

- Daily adjusted close prices are downloaded using `yfinance`
- Stocks with missing or incomplete data are removed
- Prices are split into:
  - **Selection window** (2019–2024)
  - **Out-of-sample backtest window** (2025)

This strict separation ensures no future information leakage.

---

### 2. Feature Construction

- Daily percentage returns are computed using selection-period prices
- Each stock is represented as a **vector of historical returns**
- Features are standardized using `StandardScaler`

This representation captures **co-movement behavior** rather than price levels.

---

### 3. Agglomerative Clustering (Pair Selection)

- Stocks are clustered using **Ward linkage**
- Distance metric: Euclidean distance between standardized return vectors
- Only stock pairs **within the same cluster** are considered candidates

This replaces traditional cointegration testing with a **data-driven similarity approach**.

A fixed cap is applied to limit the candidate universe size.

---

### 4. Spread Construction

For each candidate pair:

- A hedge ratio (`beta`) is estimated using **OLS regression** on training data
- The trading spread is defined as:

\[
s_t = \log(P_A) - \beta \log(P_B)
\]

This creates a scale-invariant spread suitable for mean-reversion signals.

---

### 5. Signal Generation

- A rolling z-score of the spread is computed
- Entry signals trigger when the z-score exceeds a threshold
- Positions are exited once the spread normalizes

Signals are **evaluated with a T+1 execution delay** to avoid same-day lookahead bias.

---

### 6. Daily Pair Selection (Top-15 Constraint)

To prevent overtrading and crowding:

- All candidate pairs generate signals daily
- Only the **top 15 strongest signals** (by |z-score|) are traded
- A **cluster cap** ensures no two selected pairs come from the same cluster

This enforces **diversification across latent market factors**.

---

### 7. Holding Period Constraint

- A minimum holding period (e.g., 5 days) is enforced
- Prevents excessive turnover and noise-driven trades
- Improves realism under transaction costs

---

### 8. Portfolio Construction & Risk Management

- Pair-level PnLs are aggregated daily
- Portfolio volatility is estimated using a rolling window
- PnL is scaled to target a fixed volatility level
- A hard leverage cap prevents unrealistic exposure

---

### 9. Performance Evaluation

The following metrics are computed on **out-of-sample data only**:

- Annualized Sharpe ratio
- Maximum drawdown
- Equity curve
- Rolling and expanding Sharpe ratios

All performance plots are automatically saved to the `plots/` directory.

---

## Results (2025 Out-of-Sample)

Typical observed performance (example run):

- **Annualized Sharpe**: ~1.5
- **Max Drawdown**: ~8–10%
- **Active Trading Days**: ~30–40%
- **Pairs Traded per Day**: ≤ 15

Results vary depending on clustering granularity and signal thresholds.

---

## Limitations & Considerations

- No transaction cost modeling beyond simple linear costs
- No short-sale constraints or borrow fees
- No regime detection (performance may degrade in trending markets)
- Clustering assumes return similarity is stable over time
- Strategy is research-grade, not production-ready

This implementation is designed for **robustness and interpretability**, not maximal Sharpe optimization.

---

## How to Run

### 1. Install Dependencies

In Jupyter:

```python
%pip install yfinance statsmodels scikit-learn pandas numpy matplotlib
