import streamlit as st
import yfinance as yf
import pandas as pd
import numpy as np
import plotly.graph_objects as go
import plotly.express as px
from datetime import datetime, timedelta

# ── Page config ──────────────────────────────────────────────────────────────
st.set_page_config(
    page_title="Market Data Dashboard",
    page_icon="📈",
    layout="wide"
)

# ── Title ─────────────────────────────────────────────────────────────────────
st.title("📈 Market Data Dashboard")
st.markdown("*Built by Shashank Shivanna | MSc Accounting & Finance, University of Edinburgh*")
st.markdown("---")

# ── Sidebar controls ──────────────────────────────────────────────────────────
st.sidebar.header("Dashboard Controls")

st.sidebar.subheader("Asset Selection")
default_tickers = ["SPY", "QQQ", "VWRL.L", "EEM", "GLD"]
user_tickers = st.sidebar.text_input(
    "Enter ticker symbols (comma separated):",
    value="SPY, QQQ, VWRL.L, GLD"
)
tickers = [t.strip().upper() for t in user_tickers.split(",") if t.strip()]

benchmark = st.sidebar.selectbox(
    "Benchmark:",
    ["^GSPC", "^FTSE", "^DJI", "^IXIC"],
    format_func=lambda x: {
        "^GSPC": "S&P 500",
        "^FTSE": "FTSE 100",
        "^DJI": "Dow Jones",
        "^IXIC": "NASDAQ"
    }[x]
)

period = st.sidebar.selectbox(
    "Time Period:",
    ["3mo", "6mo", "1y", "2y", "3y", "5y"],
    index=2
)

risk_free_rate = st.sidebar.slider(
    "Risk-Free Rate (%):",
    min_value=0.0,
    max_value=10.0,
    value=5.0,
    step=0.1
) / 100

st.sidebar.markdown("---")
st.sidebar.markdown("**Data source:** Yahoo Finance (free)")
st.sidebar.markdown("**Built with:** Python, Streamlit, Plotly")

# ── Helper functions ───────────────────────────────────────────────────────────
@st.cache_data(ttl=3600)
def fetch_data(tickers, benchmark, period):
    all_tickers = tickers + [benchmark]
    raw = yf.download(all_tickers, period=period, auto_adjust=True, progress=False)
    if isinstance(raw.columns, pd.MultiIndex):
        prices = raw["Close"]
    else:
        prices = raw[["Close"]]
    prices = prices.dropna(how="all")
    return prices

def annualised_return(returns):
    return returns.mean() * 252

def annualised_volatility(returns):
    return returns.std() * np.sqrt(252)

def sharpe_ratio(returns, rf):
    excess = annualised_return(returns) - rf
    vol = annualised_volatility(returns)
    return excess / vol if vol != 0 else 0

def max_drawdown(price_series):
    rolling_max = price_series.cummax()
    drawdown = (price_series - rolling_max) / rolling_max
    return drawdown.min()

def calmar_ratio(returns, price_series, rf):
    ann_ret = annualised_return(returns) - rf
    mdd = abs(max_drawdown(price_series))
    return ann_ret / mdd if mdd != 0 else 0

# ── Fetch data ────────────────────────────────────────────────────────────────
with st.spinner("Fetching market data..."):
    try:
        prices = fetch_data(tickers, benchmark, period)
    except Exception as e:
        st.error(f"Error fetching data: {e}")
        st.stop()

available = [t for t in tickers if t in prices.columns]
missing = [t for t in tickers if t not in prices.columns]

if missing:
    st.warning(f"Could not fetch data for: {', '.join(missing)}")

if not available:
    st.error("No valid tickers found. Please check your inputs.")
    st.stop()

fund_prices = prices[available]
bench_prices = prices[benchmark] if benchmark in prices.columns else None
fund_returns = fund_prices.pct_change().dropna()

# ── Section 1: Key Metrics ────────────────────────────────────────────────────
st.header("📊 Key Performance Metrics")

metrics_data = []
for ticker in available:
    ret = fund_returns[ticker].dropna()
    pr = fund_prices[ticker].dropna()
    metrics_data.append({
        "Ticker": ticker,
        "Current Price": f"${pr.iloc[-1]:.2f}",
        "Period Return (%)": f"{((pr.iloc[-1]/pr.iloc[0])-1)*100:.2f}%",
        "Ann. Return (%)": f"{annualised_return(ret)*100:.2f}%",
        "Ann. Volatility (%)": f"{annualised_volatility(ret)*100:.2f}%",
        "Sharpe Ratio": f"{sharpe_ratio(ret, risk_free_rate):.2f}",
        "Max Drawdown (%)": f"{max_drawdown(pr)*100:.2f}%",
        "Calmar Ratio": f"{calmar_ratio(ret, pr, risk_free_rate):.2f}"
    })

metrics_df = pd.DataFrame(metrics_data).set_index("Ticker")
st.dataframe(metrics_df, use_container_width=True)

# ── Section 2: Cumulative Performance ─────────────────────────────────────────
st.header("📈 Cumulative Performance")

cumulative = (1 + fund_returns).cumprod()
if bench_prices is not None:
    bench_returns = bench_prices.pct_change().dropna()
    bench_cumulative = (1 + bench_returns).cumprod()
    bench_cumulative.name = f"Benchmark ({benchmark})"

fig_cum = go.Figure()
for ticker in available:
    fig_cum.add_trace(go.Scatter(
        x=cumulative.index,
        y=cumulative[ticker],
        name=ticker,
        mode="lines",
        line=dict(width=2)
    ))

if bench_prices is not None:
    fig_cum.add_trace(go.Scatter(
        x=bench_cumulative.index,
        y=bench_cumulative,
        name=f"Benchmark ({benchmark})",
        mode="lines",
        line=dict(width=2, dash="dash", color="gray")
    ))

fig_cum.update_layout(
    title="Growth of $1 Invested",
    xaxis_title="Date",
    yaxis_title="Portfolio Value ($)",
    hovermode="x unified",
    legend=dict(orientation="h", yanchor="bottom", y=1.02),
    height=450
)
st.plotly_chart(fig_cum, use_container_width=True)

# ── Section 3: Rolling Volatility ─────────────────────────────────────────────
st.header("📉 Rolling 30-Day Volatility (Annualised)")

fig_vol = go.Figure()
for ticker in available:
    rolling_vol = fund_returns[ticker].rolling(30).std() * np.sqrt(252) * 100
    fig_vol.add_trace(go.Scatter(
        x=rolling_vol.index,
        y=rolling_vol,
        name=ticker,
        mode="lines",
        line=dict(width=1.5)
    ))

fig_vol.update_layout(
    xaxis_title="Date",
    yaxis_title="Volatility (%)",
    hovermode="x unified",
    legend=dict(orientation="h", yanchor="bottom", y=1.02),
    height=380
)
st.plotly_chart(fig_vol, use_container_width=True)

# ── Section 4: Drawdown Analysis ──────────────────────────────────────────────
st.header("📉 Drawdown Analysis")

fig_dd = go.Figure()
for ticker in available:
    pr = fund_prices[ticker].dropna()
    rolling_max = pr.cummax()
    drawdown = (pr - rolling_max) / rolling_max * 100
    fig_dd.add_trace(go.Scatter(
        x=drawdown.index,
        y=drawdown,
        name=ticker,
        mode="lines",
        fill="tozeroy",
        opacity=0.4,
        line=dict(width=1.5)
    ))

fig_dd.update_layout(
    xaxis_title="Date",
    yaxis_title="Drawdown (%)",
    hovermode="x unified",
    legend=dict(orientation="h", yanchor="bottom", y=1.02),
    height=380
)
st.plotly_chart(fig_dd, use_container_width=True)

# ── Section 5: Correlation Matrix ─────────────────────────────────────────────
st.header("🔗 Correlation Matrix")

if len(available) > 1:
    corr = fund_returns.corr()
    fig_corr = px.imshow(
        corr,
        text_auto=True,
        color_continuous_scale="RdBu_r",
        zmin=-1, zmax=1,
        title="Return Correlation Between Assets"
    )
    fig_corr.update_layout(height=400)
    st.plotly_chart(fig_corr, use_container_width=True)
else:
    st.info("Add more tickers to see correlation analysis.")

# ── Section 6: Monthly Returns Heatmap ────────────────────────────────────────
st.header("📅 Monthly Returns Heatmap")

selected_for_heatmap = st.selectbox("Select asset for monthly heatmap:", available)

monthly = fund_prices[selected_for_heatmap].resample("ME").last().pct_change().dropna() * 100
monthly_df = pd.DataFrame({
    "Year": monthly.index.year,
    "Month": monthly.index.month_name(),
    "Return": monthly.values
})

pivot = monthly_df.pivot_table(index="Year", columns="Month", values="Return")
month_order = ["January","February","March","April","May","June",
               "July","August","September","October","November","December"]
pivot = pivot.reindex(columns=[m for m in month_order if m in pivot.columns])

fig_heat = px.imshow(
    pivot,
    text_auto=".1f",
    color_continuous_scale="RdYlGn",
    title=f"Monthly Returns (%) — {selected_for_heatmap}",
    aspect="auto"
)
fig_heat.update_layout(height=350)
st.plotly_chart(fig_heat, use_container_width=True)

# ── Footer ─────────────────────────────────────────────────────────────────────
st.markdown("---")
st.markdown(
    "**Shashank Shivanna** | MSc Accounting & Finance, University of Edinburgh | "
    "[LinkedIn](https://www.linkedin.com/in/shashank-s-122six) | "
    "[GitHub](https://github.com/your-github-username)"
)
st.caption("Data sourced from Yahoo Finance. For educational purposes only. Not financial advice.")
