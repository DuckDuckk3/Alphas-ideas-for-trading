## Idea 1:
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
- Expression:C// Measuring current sentiment relative to its historical rank (20 days)
`rank(scl12_sentiment - ts_rank(scl12_sentiment, 20))`
- Expansion: ts_rank compares the current sentiment to its own 20-day history. If today's sentiment is significantly higher than its historical distribution, it captures the "immediate disclosure" behavior described in the information environment hypothesis.
### 5. News-Price Divergence (The "Alpha" Filter)
- Hypothesis: If news is positive (fscore_momentum) but the price has not yet reacted (returns), there is a higher probability of future "Return Predictability."
- Expression: Finding stocks with strong news but lagging price performance  
`group_neutralize(rank(fscore_momentum) - rank(ts_mean(returns, 3)), industry)`
- Expansion: This identifies "undervalued" news momentum. We subtract the recent 3-day return rank from the news momentum rank. We want to buy stocks where the news is great, but the market hasn't fully "bought in" yet, capturing the lag in information dissemination.  

### Critical Operators for BRAIN Optimization
- `rank(x):` Cross-sectional ranking. News scores have different scales; ranking them $[0, 1]$ makes them comparable.
- `group_neutralize(x, g):` Neutralization. News is often biased by sector (e.g., Tech has more news than Utilities). This removes that bias.
- `ts_decay_linear(x, n):` SmoothingNews signals are "decaying" assets. This lowers Turnover by exiting positions gradually.
- `ts_mean(x, n):` Time-series average. Captures the "Persistence" mentioned in the Fundamental Hypothesis.
