# Binance Research Engine v3.3 (Thai Edition)

**Advanced Portfolio Research & Backtesting Engine for Crypto Assets**  
Built with **Pure HTML + JavaScript** (No backend required)

> Designed for:
> - Quantitative researchers
> - Systematic / momentum-based investors
> - Strategy robustness & risk analysis
> - Benchmark comparison vs Buy & Hold

---

## 📌 System Overview

**Binance Research Engine** is a browser-based research & backtesting engine designed to:

- Evaluate multiple portfolio strategies simultaneously
- Perform parameter grid search (Lookback × Rebalance)
- Measure risk-adjusted performance
- Analyze robustness across parameters and time
- Visualize portfolio behavior and future uncertainty

The system works entirely on the client side using public APIs and mathematical simulation.

---

## ✨ Key Features

- Multi-strategy portfolio backtesting
- Grid Search optimization (Lookback × Rebalance)
- Robustness Score (Parameter Stability + Time Stability)
- Equity Curve & Drawdown analysis
- 3D Parameter Landscape (Plotly)
- Risk Distribution (Histogram + SD Bands)
- Monte Carlo Forecast Simulation
- Detailed Rebalance Transaction Logs
- Automatic Mock Data fallback (CORS-safe)

---

## 🧠 System Workflow (Step-by-Step)

---

## STEP 1: Asset Universe Definition

The user defines a **static universe of assets** to be considered by the system.

**Example**
```

BTC, ETH, BNB, SOL, ADA

```

> The engine will **only allocate capital among assets inside this universe**.

---

## STEP 2: Market Data Ingestion

### 2.1 Real Market Data
- Daily closing prices are fetched from the **Binance API**
- Timeframe: `1d`
- Quote currency: `USDT`

### 2.2 Automatic Mock Data (Fallback)

If the Binance API is blocked (e.g. CORS restriction), the system automatically generates **synthetic price data** using **Geometric Brownian Motion (GBM)**.

### Price Simulation Formula (GBM)

\[
P_{t+1} = P_t \cdot e^{(\mu - 0.5\sigma^2) + \sigma Z}
\]

Where:
- \( \mu \) = Drift (expected return)
- \( \sigma \) = Volatility
- \( Z \sim \mathcal{N}(0,1) \)

This ensures the research workflow remains uninterrupted.

---

## STEP 3: Data Alignment & Return Calculation

### 3.1 Price Alignment
- Merge all asset timelines
- Forward-fill missing prices
- Keep only dates where all assets have valid prices

### 3.2 Daily Return Calculation

\[
Return_t = \frac{P_t - P_{t-1}}{P_{t-1}}
\]

These returns are used for portfolio aggregation and risk metrics.

---

## STEP 4: Parameter Grid Search

The engine performs a **full grid search** over:

- **Lookback (LB)** – historical window used for momentum & volatility
- **Rebalance (RB)** – frequency of portfolio reallocation

### Example
```

Lookback:   7 → 60 (step 7)
Rebalance:  7 → 30 (step 7)

```

Every strategy is tested across **all (LB, RB) combinations**.

---

## 📊 STEP 5: Portfolio Strategies

### 1. Equal Weight
\[
w_i = \frac{1}{N}
\]

All assets receive equal allocation.

---

### 2. Rank Momentum
\[
w_i = \frac{Rank_i}{\sum Rank}
\]

Assets are ranked by momentum; higher rank = higher weight.

---

### 3. Top 3 Equal
\[
w_i = \frac{1}{3}
\quad \text{(Top 3 momentum assets only)}
\]

---

### 4. Top 3 Ranked
\[
w_i \propto Rank_i \quad \text{(Top 3 only)}
\]

---

### 5. Top 50% Equal
\[
w_i = \frac{1}{\lceil N/2 \rceil}
\]

---

### 6. Top 50% Ranked
\[
w_i = \frac{Rank_i}{\sum Rank} \quad \text{(Top half only)}
\]

---

### 7. Absolute Momentum
\[
w_i =
\begin{cases}
\frac{1}{K}, & \text{if } Return_i > 0 \\
0, & \text{otherwise}
\end{cases}
\]

---

### 8. Momentum Weighted
\[
w_i = \frac{Return_i}{\sum Return_i}, \quad Return_i > 0
\]

---

### 9. Dual Momentum
- Asset must be:
  - Positive momentum
  - Ranked in Top 3

---

### 10. Inverse Volatility
\[
w_i = \frac{1/\sigma_i}{\sum (1/\sigma)}
\]

Lower volatility assets receive higher allocation.

---

### 11. Adaptive Aggressive Allocation (AAA)
- If market trend is positive:
  - Use Inverse Volatility
- Otherwise:
  - Fallback to Top 3 momentum assets

---

## 🔁 STEP 6: Rebalancing & Transaction Costs

### Portfolio Return
\[
R_p = \sum w_i \cdot R_i
\]

### NAV Update
\[
NAV_{t+1} = NAV_t \cdot (1 + R_p)
\]

### Turnover
\[
Turnover = \sum |w_i^{new} - w_i^{old}|
\]

### Transaction Fee
\[
Fee = NAV \cdot Turnover \cdot FeeRate
\]

---

## 📈 STEP 7: Performance Metrics

### CAGR
\[
CAGR = \left(\frac{NAV_{end}}{NAV_{start}}\right)^{\frac{365}{T}} - 1
\]

---

### Annualized Volatility
\[
\sigma_{ann} = \sigma_{daily} \cdot \sqrt{365}
\]

---

### Sharpe Ratio
\[
Sharpe = \frac{CAGR - R_f}{\sigma_{ann}}
\]

(Default \( R_f = 2\% \))

---

### Maximum Drawdown
\[
MaxDD = \min \left( \frac{NAV_t - Peak}{Peak} \right)
\]

---

### Rolling 30-Day Win Rate
\[
Win_{30d} = \frac{\#(Return_{30d} > 0)}{Total\ Windows}
\]

---

## 🧱 STEP 8: Robustness Score (R-Score)

The **Robustness Score** evaluates strategy stability:

### Component 1: Parameter Stability
Average Sharpe of neighboring parameter sets:
\[
Sharpe_{neighbors}
\]

### Component 2: Time Stability
Rolling 30-day Sharpe:
\[
Sharpe_{rolling}
\]

### Final Robust Score
\[
R = 0.6 \cdot Sharpe_{neighbors} + 0.4 \cdot Sharpe_{rolling}
\]

---

## 🌄 STEP 9: Visualization Modules

- Equity Curve vs Benchmark
- Drawdown Curve
- 3D Parameter Landscape (LB × RB × Metric)
- Risk Distribution Histogram
- Transaction Log Viewer
- Monte Carlo Forecast

---

## 🎲 STEP 10: Monte Carlo Simulation

### Price Evolution
\[
P_{t+1} = P_t \cdot e^{(\mu - 0.5\sigma^2) + \sigma Z}
\]

### Outputs
- Expected terminal NAV
- Worst / Best case (5% – 95%)
- Expected Max Drawdown
- Expected Volatility

---

## 🚀 Live Demo

👉 https://YOUR_GITHUB_USERNAME.github.io/binance-research-engine/

---

## 🛠 Tech Stack

- Tailwind CSS
- Chart.js + Annotation Plugin
- Plotly.js
- Font Awesome
- MathJax

---

## ⚠️ Disclaimer

This project is for **research & educational purposes only**.  
It does **not constitute financial advice**.

---

Developed by **Your Name**
```
