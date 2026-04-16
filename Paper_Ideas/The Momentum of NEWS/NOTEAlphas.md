# Idea 1:
### 1. The Fundamental Persistence    
- Hypothesis: Based on the Fundamental Hypothesis, earnings are persistent, leading to a steady, long-term stream of positive news. We capture this by smoothing sentiment over a typical earnings cycle (approx. 20 trading days).  
- Expression: Capturing the persistent "Fundamental" trend while reducing noise
`ts_decay_linear(group_neutralize(rank(ts_mean(scl12_sentiment, 22)), subindustry), 5)`

- Expansion: By using ts_mean over 22 days (one business month), we identify stocks where the "earning stream" mentioned in the text is consistently positive. Subindustry neutralization ensures we are finding the best-performing stocks relative to their direct peers, not just riding a sector-wide trend.  
### 2. Strategic Disclosure Deceleration (The "Bad News" Lead)  
- Hypothesis: The Information Environment Hypothesis states that managers delay bad news. When the momentum of news starts to decelerate (drop from its peak), it indicates that the "bad news" can no longer be hidden.  
- Expression: Shorting stocks where news momentum is starting to fade  
`-1 * group_neutralize(rank(ts_delta(fscore_momentum, 5)), industry)`
- Expansion: We use ts_delta over 5 days to measure the "velocity" of news. A negative delta suggests that the "News Momentum Phenomenon" is losing steam, which often precedes a downward price correction as the market realizes the fundamental "persistence" is ending.
### 3. High-Conviction News Quintiles  
- Hypothesis: The research found that stocks in the highest quintile of news scores outperform. We combine the momentum of news with the depth of coverage (rp_css_assets) to filter for the most reliable signals.
- Expression: Combining News Momentum with Coverage Depth for high-conviction signals  
`group_neutralize(rank(fscore_momentum) + rank(rp_css_assets), subindustry)`
- Expansion: rp_css_assets represents the number of news assets/stories. A high fscore_momentum is more statistically significant if it is backed by many news articles (rp_css_assets), effectively selecting the "strongest" stocks in the top quintile.
### 4. Immediate Disclosure Breakout (The "Rush" Signal)  
- Hypothesis: Managers "immediately reveal good news." A sudden surge in sentiment compared to its historical baseline acts as a signal for an immediate long position.
- Expression: Measuring current sentiment relative to its historical rank (20 days)  
`rank(scl12_sentiment - ts_rank(scl12_sentiment, 20))`
- Expansion: ts_rank compares the current sentiment to its own 20-day history. If today's sentiment is significantly higher than its historical distribution, it captures the "immediate disclosure" behavior described in the information environment hypothesis.
### 5. News-Price Divergence (The "Alpha" Filter)
- Hypothesis: If news is positive (fscore_momentum) but the price has not yet reacted (returns), there is a higher probability of future "Return Predictability."
- Expression: Finding stocks with strong news but lagging price performance  
`group_neutralize(rank(fscore_momentum) - rank(ts_mean(returns, 3)), industry)`
- Expansion: This identifies "undervalued" news momentum. We subtract the recent 3-day return rank from the news momentum rank. We want to buy stocks where the news is great, but the market hasn't fully "bought in" yet, capturing the lag in information dissemination.  

### Mapping Paper Concepts to BRAIN Data

| Paper Concept | Interpretation | BRAIN Proxy |
|---|---|---|
| positive / negative news | market reaction | short-term return |
| news clustering | repeated information arrival | volume spikes |
| information environment | steady information flow | trend + low volatility |
| fundamental persistence | earnings persistence | EPS / profitability trend |

---

### Alphas

Typical workflow:
1. detect **information shock**
2. measure **continuation**
3. smooth signal
4. neutralize sector risk

### Alpha Candidates

#### Alpha 1 — Price Reaction Momentum

Proxy for **positive news continuation**

```
alpha =
rank(ts_mean(close / delay(close,1) - 1, 5))
```

Interpretation:

Recent abnormal returns imply **positive information flow**.

---

#### Alpha 2 — Price vs VWAP Signal

Detects sustained buying pressure after information arrival.

```
alpha =
rank(ts_mean(close - vwap, 5))
```

Logic:

After positive news:

```
close > vwap
```

for multiple days.

---

#### Alpha 3 — News Shock Proxy (Price + Volume)

News events usually cause **both price movement and volume spikes**.

```
alpha =
rank(ts_delta(close,5)) *
rank(ts_mean(volume,5))
```

Interpretation:

Large price movement + high volume  
→ possible information shock.

---

#### Alpha 4 — Information Environment Proxy

Firms with stable information environments tend to show **persistent trends with lower noise**.

```
alpha =
rank(ts_mean(close,20)) -
rank(ts_std_dev(close,20))
```

Logic:

Trend strength minus volatility.

#### Alpha 5 — Fundamental Persistence (EPS)
Paper suggests news persistence reflects **persistent fundamentals**.  
```
alpha =
rank(ts_delta(fnd2_eps,252))
```
Interpretation:
Companies with improving earnings generate **continuous positive news**.

#### Alpha 6 — Earnings Acceleration
```
alpha =
rank(ts_mean(fnd2_eps,4)) -
rank(ts_mean(fnd2_eps,12))
```
Detects improving earnings trend.

#### Alpha 7 — News Clustering via Volume
News tends to arrive in **clusters**.
```
alpha =
rank(ts_mean(volume,5) - ts_mean(volume,20))
```
Interpretation:
Short-term attention surge.

### Recommended Base Signals

Three signals most consistent with the paper:
### Signal A — News Shock
```
alpha =
rank(ts_delta(close,5)) *
rank(ts_mean(volume,5))
```
### Signal B — News Momentum
```
alpha =
rank(ts_mean(close,5) - ts_mean(close,20))
```
### Signal C — Fundamental Persistence
```
alpha =
rank(ts_delta(fnd2_eps,252))
```
### Possible Improvements

To reduce turnover and noise:
### Exponential decay

```
ts_decay_linear(signal, 10)
```
### Liquidity filter
```
volume > ts_mean(volume,20)
```

### Combined alpha

```
alpha =
rank(ts_delta(close,5)) *
rank(ts_mean(volume,5)) +
0.5 * rank(ts_delta(fnd2_eps,252))
```

# 8. Key Insight

Most academic finance factors **cannot be used directly** in BRAIN.

Instead we must translate them into **observable market proxies**.

Example:

| Academic Variable | BRAIN Proxy |
|---|---|
news sentiment | return + volume |
investor attention | volume spike |
earnings news | EPS change |
information flow | volatility |

### Submission Tips

Before submitting:

- smooth signals
- reduce turnover
- neutralize industries
- test multiple decay windows

Typical enhancement:

```
alpha =
group_neutralize(
    ts_decay_linear(rank(ts_mean(close,5)-ts_mean(close,20)),10),
    subindustry
)
```

---

### Summary

The **Momentum of News** effect can be implemented in BRAIN by constructing proxies for information flow:

Main approaches:

1. **Price reaction continuation**
2. **Volume-based attention signals**
3. **Fundamental persistence**

Combining these signals often produces stronger alphas.

```
alpha =
rank(ts_delta(close,5)) *
rank(ts_mean(volume,5)) +
rank(ts_delta(fnd2_eps,252))
```

### Critical Operators for BRAIN Optimization
- `rank(x):` Cross-sectional ranking. News scores have different scales; ranking them $[0, 1]$ makes them comparable.
- `group_neutralize(x, g):` Neutralization. News is often biased by sector (e.g., Tech has more news than Utilities). This removes that bias.
- `ts_decay_linear(x, n):` SmoothingNews signals are "decaying" assets. This lowers Turnover by exiting positions gradually.
- `ts_mean(x, n):` Time-series average. Captures the "Persistence" mentioned in the Fundamental Hypothesis.
