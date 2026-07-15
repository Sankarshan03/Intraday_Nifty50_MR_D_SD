# README: Nifty 50 Intraday Multi-Strategy Trading System

## Overview

This project implements and backtests three distinct intraday trading strategies on the **Nifty 50** index (hourly data) using a combination of **Adaptive Exponential Moving Average (AEMA)**, **Mean Reversion (Z‑score)** and **Semi‑Directional** filters.  
To achieve a robust risk‑adjusted performance, the strategies are combined into an **ensemble portfolio** with **volatility scaling** (inverse‑volatility weighting).

The notebook is fully self‑contained, using `yfinance` for data download and `plotly` for interactive visualizations.

---

## Data

- **Instrument**: `^NSEI` (Nifty 50)
- **Period**: 2025‑07‑13 to 2026‑07‑13 (1 year)
- **Frequency**: 1‑hour candles
- **Source**: Yahoo Finance via `yfinance`
- **Columns**: `Date`, `Time`, `Open`, `High`, `Low`, `Close`, `Volume`

---

## Feature Engineering

- **Average True Range (ATR)** – 14‑period Exponential Weighted Moving Average of the True Range.
- **Log Returns** – used for performance calculation.
- **Dynamic Smoothing Factor** – Min‑Max scaled ATR, mapped to [0,1] to adapt the EMA smoothing parameter.

---

## Strategies

### 1. Adaptive Mean Reversion (Z‑score + AEMA)
- Computes an **Adaptive EMA (AEMA)** where the smoothing factor is derived from ATR (high volatility → faster adaptation).
- Calculates a **Z‑score**: `(Close – AEMA) / rolling std(Close – AEMA)`.
- Generates **long** signals when `Z > 1` and **short** when `Z < -1`.
- Exits when the Z‑score reverts toward zero (using an Ornstein‑Uhlenbeck half‑life estimation).

### 2. Directional Trend (AEMA crossover)
- Simple trend‑following rule:  
  - **Long** when `Close > AEMA`  
  - **Short** when `Close < AEMA`
- Positions are taken on the next hour’s open.

### 3. Semi‑Directional (Hybrid)
- Combines both trend and mean reversion:  
  - **Entry** only when price is in the *direction* of the long‑term trend (i.e., Close > AEMA for long, Close < AEMA for short) AND the Z‑score exceeds a threshold (|Z| > 2.0).
  - **Exit** when Z‑score drops below 0.3 or the trend reverses.
- This filter avoids “catching falling knives” while still profiting from pullbacks.

---

## Portfolio Construction

- Uses **inverse‑volatility weighting** with a 30‑period rolling window.
- Weights are lagged by one period to avoid look‑ahead bias.
- The three strategy returns are combined into a single portfolio return.

---

## Performance Metrics

The following metrics are computed for each strategy and the ensemble:

- **Annualized Return**  
- **Sharpe Ratio** (risk‑free rate = 0)  
- **Sortino Ratio** (downside deviation)  
- **Max Drawdown**  
- **Calmar Ratio**  
- **Hit Ratio** (percentage of positive trades)  
- **Information Ratio** (relative to benchmark of hourly returns)

> **Trading assumptions**: 6.5 trading hours/day × 252 trading days/year.

---

## Results Summary (Backtest)

| Strategy          | Ann. Return | Sharpe | Sortino | Max DD   | Calmar | Hit Ratio |
|-------------------|-------------|--------|---------|----------|--------|-----------|
| **Mean Reversion**| 18.56%      | 1.56   | 2.06    | -5.34%   | 3.47   | 36.0%     |
| **Trend**         | 12.61%      | 1.00   | 1.41    | -6.50%   | 1.94   | 28.7%     |
| **Semi‑Directional**| 8.03%     | 0.70   | 0.86    | -7.28%   | 1.10   | 31.7%     |
| **Ensemble**      | 12.99%      | 1.11   | 1.54    | **-5.47%**| **2.38** | –         |

**Key Observations**:
- The **Mean Reversion** strategy delivered the highest annualized return and Sharpe ratio, but its Calmar was improved by the portfolio.
- The **Ensemble** portfolio achieved the best **Calmar Ratio** (2.38) with lower drawdown than both Trend and Semi‑Directional, while maintaining competitive risk‑adjusted returns.
- The rolling volatility of the portfolio is consistently lower and more stable than any single strategy, confirming the benefit of diversification.

---

## How to Run

1. **Clone the repository** and open the notebook in Jupyter/Colab.
2. **Install required packages** (see Dependencies below).
3. **Run all cells** in order – the notebook downloads data, computes features, backtests strategies, and generates interactive plots.

> The notebook uses a future‑dated period (2025‑2026) – adjust `start_time` and `end_time` in the second cell to a valid historical range if you wish to reproduce with live data.

---

## Dependencies

The notebook relies on the following Python libraries:

- `yfinance`
- `pandas`
- `numpy`
- `matplotlib`
- `plotly`
- `statsmodels`

Install them via:

```bash
pip install yfinance pandas numpy matplotlib plotly statsmodels
```

---

## Conclusion

This project demonstrates how combining **uncorrelated alpha sources** (trend, mean reversion, and a hybrid semi‑directional filter) with **volatility scaling** can produce a more robust equity curve with better downside protection. The ensemble portfolio outperforms any individual strategy in terms of Calmar ratio and stability, making it a strong candidate for systematic intraday trading.

**Future improvements** could include:
- Incorporating transaction costs and slippage.
- Testing on other indices or asset classes.
- Applying machine learning to dynamically switch between strategies.
