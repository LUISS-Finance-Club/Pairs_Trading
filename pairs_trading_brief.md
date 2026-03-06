# LFC MarketView: Pairs Trading Strategy

## Project Type
Solo Jupyter Notebook

## Overview
Build a statistical arbitrage (stat arb) strategy that exploits the relationship between two historically correlated stocks. When their prices diverge beyond a threshold, you go long the underperformer and short the outperformer — betting they will converge back. This is one of the most classic and widely used quantitative strategies on Wall Street.

This project teaches you how quants find mispricings between related assets and build systematic strategies to profit from them.

## What You'll Build
A complete Jupyter notebook implementing:
1. Find cointegrated pairs (Coke/Pepsi, XOM/CVX, Goldman/JPMorgan)
2. Model the spread between them
3. Generate Z-score trading signals
4. Backtest with transaction costs
5. Compare performance vs SPY benchmark

## Important
The use of Generative AI (Claude) is encouraged, however it remains essential that the student understands and is able to explain without the use of the technology any design choices/implementations in the code.

## Learning Objectives
- Understand cointegration vs correlation (they are not the same)
- Implement the Engle-Granger cointegration test
- Build a Z-score signal from scratch
- Construct a long-short portfolio
- Calculate risk-adjusted performance metrics

## PART 1: Finding Pairs & Testing Cointegration

**Goal**: Identify statistically valid pairs using cointegration tests

**What is cointegration?**
Two stocks are cointegrated if their prices tend to move together long-term, even if they diverge short-term. Correlation measures direction. Cointegration measures long-run equilibrium.

**Candidate Pairs to Test**:
```
Consumer:   KO / PEP    (Coca-Cola / Pepsi)
Energy:     XOM / CVX   (ExxonMobil / Chevron)
Banks:      GS / JPM    (Goldman / JPMorgan)
Tech:       MSFT / GOOGL
Airlines:   DAL / UAL
ETFs:       XLF / KBE   (Finance sector ETFs)
```

**Tasks**:
- Download 5+ years of daily price data for all pairs
- Visual inspection: plot normalized prices on same chart
- Run Augmented Dickey-Fuller (ADF) test on each spread
- Run Engle-Granger cointegration test
- Select 2-3 pairs that pass statistical tests (p-value < 0.05)
- Calculate hedge ratio (β) using OLS regression

**Deliverables**:
- [ ] DataCamp tutorial code runs successfully
- [ ] 6+ candidate pairs downloaded and plotted
- [ ] Cointegration test results table (p-values for each pair)
- [ ] 2-3 valid pairs confirmed with hedge ratio calculated
- [ ] README: Explain the difference between correlation and cointegration

## PART 2: Signal Generation & Strategy Logic

**Goal**: Build Z-score signals and implement trading rules

**The Spread**:
```python
# Spread = price of stock A minus hedge ratio × price of stock B
spread = price_A - beta * price_B

# Z-score normalizes the spread
z_score = (spread - spread.mean()) / spread.std()
```

**Trading Rules**:
```
Entry signals:
  Z > +2.0  → Short A, Long B  (A expensive, B cheap)
  Z < -2.0  → Long A, Short B  (A cheap, B expensive)

Exit signals:
  |Z| < 0.5 → Close position (spread converged)

Stop-loss:
  |Z| > 3.5 → Close position (spread diverging dangerously)
```

**Tasks**:
- Calculate spread for each valid pair
- Compute rolling Z-score (60-day lookback window)
- Code entry/exit/stop-loss logic
- Plot Z-score with entry/exit threshold lines
- Apply transaction costs (0.1% per trade, both legs)

**Deliverables**:
- [ ] Spread and Z-score calculated for each pair
- [ ] Z-score chart with threshold lines (±2, ±0.5, ±3.5)
- [ ] Trade log: entry date, exit date, P&L per trade
- [ ] README: Explain what Z-score means and why ±2 is the threshold

## PART 3: Backtesting & Performance Analysis

**Goal**: Backtest strategy and measure risk-adjusted returns

**Metrics to Calculate**:
```python
CAGR        = annualized compound return
Sharpe      = (return - risk_free) / volatility
Max DD      = max peak-to-trough loss
Win Rate    = % of trades profitable
Avg Hold    = average days position open
Num Trades  = total trades over period
```

**Visualizations**:
1. Growth of $10,000 chart (strategy vs SPY)
2. Z-score chart with trade entry/exit markers
3. Spread over time with convergence periods highlighted
4. Trade P&L distribution (histogram)
5. Drawdown chart

**Tasks**:
- Run full backtest for each pair
- Calculate all metrics
- Build performance comparison table
- Analyse: which pair works best? Why?
- Document: when does the strategy fail?

**Deliverables**:
- [ ] Full backtest results for 2-3 pairs
- [ ] Growth of $10k chart vs SPY benchmark
- [ ] Performance table across all pairs
- [ ] Trade P&L histogram
- [ ] README: Explain Sharpe ratio, max drawdown, why strategy failed/succeeded


**Performance Table Example**:
| Metric | KO/PEP | XOM/CVX | GS/JPM | SPY |
|--------|--------|---------|--------|-----|
| CAGR | 9.2% | 11.4% | 8.7% | 11.8% |
| Sharpe | 1.31 | 1.52 | 1.18 | 0.89 |
| Max DD | -8% | -11% | -14% | -28% |
| Win Rate | 64% | 61% | 57% | — |
| Avg Hold | 12d | 9d | 15d | — |

**Optional Slides** (3 slides):
1. Strategy logic: What is cointegration, how Z-score signals work
2. Key results: Best pair, growth chart, performance table
3. Insights: When it works, when it breaks, real-world limitations

## Success Metrics

**Minimum Viable Product (MVP)**:
- [ ] 2 valid cointegrated pairs found
- [ ] Z-score signals correctly coded
- [ ] Backtest with transaction costs
- [ ] Growth chart vs SPY
- [ ] Performance table with 5+ metrics
- [ ] Clean, documented code

**Stretch Goals (Optional)**:
- [ ] Kalman filter for dynamic hedge ratio (instead of fixed OLS)
- [ ] Test multiple Z-score thresholds (1.5, 2.0, 2.5) → find optimal
- [ ] Rolling cointegration test (does relationship hold over time?)
- [ ] Portfolio of 3 pairs simultaneously
- [ ] Regime detection: avoid trading in high-volatility markets

## Resources

**DataCamp Tutorial**: https://github.com/datacamp/datacamp-community-tutorials/blob/master/Python%20Finance%20Tutorial%20For%20Beginners/Python%20For%20Finance%20Beginners%20Tutorial.ipynb

**Candidate Pairs**:
- KO / PEP (Consumer staples — most stable pair)
- XOM / CVX (Energy — highly correlated business models)
- GS / JPM (Banks — similar macro exposure)
- DAL / UAL (Airlines — same industry, same cost drivers)

**Key Concepts**:
- Engle-Granger test: statsmodels.tsa.stattools.coint()
- ADF test: statsmodels.tsa.stattools.adfuller()
- OLS regression: numpy.polyfit() or statsmodels.OLS()

**Key Papers**:
- Gatev, Goetzmann & Rouwenhorst (2006): "Pairs Trading: Performance of a Relative Value Arbitrage Rule"
- Vidyamurthy (2004): "Pairs Trading: Quantitative Methods and Analysis"

