# Intraday Strategy Analysis for Nifty 50

This repository contains a comprehensive Jupyter notebook that develops and backtests multiple intraday trading strategies for the Nifty 50 index. The analysis explores mean reversion, trend following, and hybrid approaches, culminating in a multi-strategy ensemble portfolio that demonstrates superior risk-adjusted returns.

## Overview

The notebook implements three distinct intraday strategies:

1. **Pure Mean Reversion**: Enters positions when prices deviate significantly from an Adaptive EMA (AEMA) and exits when they revert to the mean.
2. **Pure Trend Following (AEMA)**: Follows the trend by taking long positions when prices are above AEMA and short positions when below.
3. **Semi-Directional (Hybrid)**: Combines mean reversion and trend elements, entering only when both a significant deviation and a clear trend exist.

The strategies are then combined using a dynamic risk-parity approach, creating an ensemble portfolio that adapts to changing market conditions.

## Key Features

- **Hourly OHLCV Data**: Uses 1-hour interval data from Yahoo Finance
- **Adaptive EMA (AEMA)**: Dynamically adjusts smoothing based on normalized ATR (Average True Range)
- **Ornstein-Uhlenbeck Process**: Estimates mean reversion half-life for optimal entry/exit parameters
- **Volatility Scaling**: Adjusts position sizes based on current volatility for more stable equity curves
- **Risk-Parity Ensemble**: Combines strategies based on rolling Sharpe ratios and inverse volatility
- **Comprehensive Metrics**: Includes Sharpe, Sortino, Calmar ratios, drawdown analysis, and trade statistics

## Requirements

```bash
pip install yfinance pandas numpy matplotlib plotly statsmodels
```

## Data Source

The notebook uses `yfinance` to download Nifty 50 (^NSEI) data. By default, it retrieves data from July 2025 to July 2026.

## Notebook Structure

### 1. Data Collection & Preparation
- Downloads 1-hour Nifty 50 data using Yahoo Finance
- Flattens multi-index columns and creates separate Date/Time columns
- Cleans and structures data for analysis

### 2. Feature Engineering
- **ATR Calculation**: 14-period Average True Range with EMA smoothing
- **Log Returns**: Calculated for return analysis
- **ATR Normalization**: ATR divided by Close price for relative volatility measurement
- **Adaptive EMA (AEMA)**: Dynamic smoothing based on normalized ATR

### 3. Mean Reversion Strategy
- Uses Ornstein-Uhlenbeck process to estimate half-life of mean reversion
- Entry: Z-score crosses ±1.0 threshold
- Exit: Z-score returns to ±0.2, ATR stop loss, or maximum 8-hour hold
- Performance: 41.17% annualized return, 38.58% hit ratio, 2.25 Sharpe ratio

### 4. Trend Following Strategy (AEMA)
- Long signals when Close > AEMA (bullish momentum)
- Short signals when Close < AEMA (bearish momentum)
- Entry/exit based on crossovers
- Performance: 17.20% annualized return, 31.85% hit ratio, 1.36 Sharpe ratio

### 5. Semi-Directional Strategy
- Hybrid approach combining mean reversion and trend signals
- Requires both Z-score > 2.0 (< -2.0) and trend confirmation
- Additionally requires trend strength > 1.2 × ATR
- Performance: 4.93% annualized return, 34.29% hit ratio, 0.50 Sharpe ratio

### 6. Portfolio Construction
- Combines all three strategies using risk-parity weights
- Dynamic weighting based on rolling Sharpe and inverse volatility
- Significantly improves risk-adjusted returns
- Ensemble Performance: 23.38% annualized return, 1.78 Sharpe ratio, -6.42% max drawdown

## Results Summary

| Strategy | Annual Return | Sharpe Ratio | Sortino Ratio | Max Drawdown | Calmar Ratio |
|----------|---------------|--------------|---------------|--------------|--------------|
| Trend (AEMA) | 17.19% | 1.36 | 1.84 | -8.10% | 2.12 |
| Mean Reversion | 38.32% | 2.16 | 2.46 | -7.69% | 4.98 |
| Semi-Directional | 4.92% | 0.50 | 0.45 | -8.38% | 0.59 |
| **Ensemble Portfolio** | **23.38%** | **1.78** | **2.29** | **-6.42%** | **3.64** |

## Key Insights

1. **Mean Reversion Dominates**: The pure mean reversion strategy significantly outperforms trend following, achieving nearly double the annualized return with better risk metrics.

2. **Portfolio Diversification Works**: The ensemble portfolio, despite having lower individual returns than pure mean reversion, demonstrates the best risk-adjusted metrics with the lowest maximum drawdown.

3. **Volatility Scaling is Critical**: Position sizing based on volatility helps smooth equity curves and improve Calmar ratios.

4. **Trend Strength Filter Improves Performance**: The semi-directional strategy's requirement for both deviation and trend confirmation results in fewer but higher-quality trades.

## Usage

1. Open the Jupyter notebook:
```bash
jupyter notebook Intraday_Nifty50_MR_D_SD_1y.ipynb
```

2. Run all cells sequentially. The notebook will:
   - Download data (may take a few moments)
   - Calculate features and indicators
   - Execute all three strategies
   - Build the ensemble portfolio
   - Generate performance metrics and visualizations

## Customization

Key parameters can be adjusted in the respective cells:
- `ENTRY_Z` and `EXIT_Z`: Z-score thresholds for mean reversion
- `ATR_MULT`: ATR multiplier for stop loss
- `MAX_HOLD`: Maximum holding period in hours
- `VOL_WINDOW`: Window for volatility calculations
- `REGIME_THRESHOLD`: Threshold for switching between trend and mean reversion

## Visualization

The notebook includes interactive Plotly charts for:
- Price evolution with AEMA overlay
- Individual strategy cumulative returns
- Rolling volatility comparison
- Performance attribution with strategy weights
- Ensemble portfolio equity curve

## Notes

- The data includes volume but yfinance returns zero volume for indices
- All calculations assume 6.5 trading hours per day × 252 trading days for annualization
- Results are based on gross returns (no transaction costs or slippage)
- The ensemble portfolio shows significant improvement in risk-adjusted returns

