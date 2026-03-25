# I. PRICE-BASED
## 1. Mean Reversion (MR) - “price snaps back”
### Idea: short-term overreaction → reversal
- If price goes up too fast → expect it to drop
- If price drops too fast → expect rebound
### Typical form:
`ts_delta(close, n)`
`(close - ts_mean(close,n)) / ts_std_dev(close,n)`
### Insight:
- Works best in sideways markets
- Fails in strong trends
- Very common → low but stable edge

## 2.Momentum (MOM) - “trend continuation”
### Idea: price keeps moving in same direction
- Winners keep winning
- Losers keep losing
### Typical form:
`ts_sum(returns, n)`
`ts_mean(returns, n)`
### Insight:
- Works in trending markets
- Fails in choppy/sideways regimes
- One of the most robust factors

## 3. Breakout - “new regime”
### Idea: breaking highs/lows → continuation
- New high → bullish continuation
- New low → bearish continuation
### Typical form:
`(close - ts_max(high,n)) / ts_max(high,n)`
`(close - ts_min(low,n)) / ts_min(low,n)`
### Insight:
- Works with strong volume
- Fails on fake breakouts
- Needs confirmation

## 4. Acceleration - “trend is strengthening”
### Idea: momentum is increasing over time
Not just going up, but going up faster
### Typical form:
`ts_delta(ts_sum(returns,n1), n2)`
### Insight:
- Works early in trends
- Fails near exhaustion
- Good for timing entries

## 5. Persistence - “consistency matters”

### Idea: stable moves are stronger than noisy ones
Many small gains > few large spikes
### Typical form:
`ts_sum(returns > 0 ? 1 : 0, n)`
### Insight:
- Filters out noisy assets
- Improves momentum signals
- Strong indicator of institutional flow
