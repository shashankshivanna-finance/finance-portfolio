# finance-portfolio
# 📈 Market Data Dashboard

An interactive financial market data dashboard built with Python and Streamlit, demonstrating applied data analysis and visualisation skills in a real-world finance context.

**Built by:** Shashank Shivanna  
**Background:** MSc Accounting & Finance, University of Edinburgh  
**LinkedIn:** [linkedin.com/in/shashank-s-122six](https://www.linkedin.com/in/shashank-s-122six)

---

## What This Dashboard Does

This tool pulls live financial market data from Yahoo Finance and provides a comprehensive performance analysis across multiple assets, including:

- **Key Performance Metrics** — annualised return, volatility, Sharpe ratio, max drawdown, Calmar ratio
- **Cumulative Performance Chart** — growth of $1 invested across selected assets vs benchmark
- **Rolling 30-Day Volatility** — tracks how risk evolves over time for each asset
- **Drawdown Analysis** — visualises peak-to-trough losses across the selected period
- **Correlation Matrix** — heatmap showing return correlation between assets
- **Monthly Returns Heatmap** — calendar view of monthly performance for any selected asset

---

## Why I Built This

During my MSc dissertation I compared DCF and multiples-based valuation frameworks using Python for statistical analysis. This project extends that foundation into live market data analysis — applying the same analytical rigour to real-time fund and equity performance monitoring.

The skills demonstrated here — data extraction, transformation, statistical calculation, and interactive visualisation — directly reflect the work I am targeting in investment operations, market data analysis, and financial services roles.

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python | Core programming language |
| Streamlit | Interactive web dashboard framework |
| yfinance | Yahoo Finance API wrapper for market data |
| Pandas | Data manipulation and analysis |
| NumPy | Statistical calculations |
| Plotly | Interactive charting and visualisation |

---

## How to Run Locally

### Step 1 — Clone the repository
```bash
git clone https://github.com/shashankshivanna-finance/finance-portfolio.git
cd finance-portfolio
```

### Step 2 — Install dependencies
```bash
pip install -r requirements.txt
```

### Step 3 — Run the dashboard
```bash
streamlit run dashboard.py
```

The dashboard will automatically open in your browser at `http://localhost:8501`

---

## How to Use It

1. **Enter ticker symbols** in the sidebar — e.g. `SPY, QQQ, VWRL.L, GLD`
2. **Select a benchmark** — S&P 500, FTSE 100, Dow Jones, or NASDAQ
3. **Choose a time period** — 3 months to 5 years
4. **Adjust the risk-free rate** — used for Sharpe and Calmar ratio calculations
5. All charts and metrics update automatically

### Example ticker symbols to try
| Asset Class | Examples |
|-------------|---------|
| US Equity ETFs | SPY, QQQ, IWM, VTI |
| Global Equity ETFs | VWRL.L, ACWI, EEM |
| Fixed Income | AGG, TLT, IGLT.L |
| Commodities | GLD, SLV, USO |
| Individual Stocks | AAPL, MSFT, TSLA |

---

## Key Financial Metrics Explained

**Annualised Return** — average yearly return scaled from the selected period

**Annualised Volatility** — standard deviation of daily returns scaled to annual, measuring risk

**Sharpe Ratio** — return earned per unit of risk above the risk-free rate. Higher is better

**Max Drawdown** — largest peak-to-trough decline. Measures worst-case loss scenario

**Calmar Ratio** — annualised return divided by maximum drawdown. Higher is better

---

## Potential Extensions

- Add FRED macroeconomic data overlay (inflation, interest rates, GDP)
- Portfolio optimisation using Modern Portfolio Theory
- Monte Carlo simulation for return forecasting
- Export functionality for reports

---

## Disclaimer

This dashboard is built for educational and portfolio demonstration purposes only. Data is sourced from Yahoo Finance. Nothing here constitutes financial advice.
