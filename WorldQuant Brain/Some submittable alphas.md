## Submitted Alphas with Explanations
These alphas quality are almost average; you can combine or optimize them to gain better results. Settings can also be a significantly effective way to peak these alphas.
### 1. Close Location Value (CLV) Weighted by Volume
Explanation: Measures where the close is relative to the high-low range, weighted by recent volume. Combines short-term price reversal with volume effects.  
`clv = ((close - low) - (high - close)) / (high - low);`  
`rank(ts_mean(-clv * (volume / ts_mean(volume,20)), 3)) * rank(-ts_delta(close,3))`

### 2. Positive Days vs Short-Term Returns
Explanation: Compares the number of positive return days over a long window with short-term average returns. Captures mean-reversion vs short-term momentum.  
`positive_days = ts_sum(returns > ts_mean(returns,20) ? 1 : 0,250);`  
`short_term = ts_mean(returns,5);`  
`rank(positive_days) - rank(short_term)`

### 3. Short-Term Momentum with Volume Adjustment
Explanation: Short-term price momentum adjusted for relative volume.  
`rank(-ts_delta(close,3)) * rank(ts_decay_linear(volume / ts_mean(volume,20),5))`

### 4. News Sentiment Alpha
Explanation: Uses news sentiment signals averaged over the last 60 periods. If sentiment is strongly positive, go long; otherwise combine with short-term price momentum.  
`rank(ts_sum(vec_avg(nws12_afterhsz_sl),60)) > 0.5 ? 1 : rank(-ts_delta(close, 2))`

### 5. Upside Volatility Normalized Alpha
Explanation: Focuses on upside returns relative to overall volatility, combined with short-term negative price movement.  
`rank(ts_sum(max(returns,0),250) / ts_std_dev(returns,60)) + rank(-ts_delta(close,3))`

### 6. Multi-Factor Weighted Alpha
Explanation: Combines multiple signals: short-term momentum, volume adjustment, long-term positive days vs short-term returns, short-term volatility relative to long-term, intraday CLV, and volume volatility. Weighted decay applied for smoothing.  
`ts_decay_linear(`  
`0.35 * (rank(-ts_delta(close,4)) * rank(volume / ts_mean(volume,20))) +`  
`0.2 * (rank(ts_sum(returns > 0 ? 1 : 0,250)) - rank(ts_mean(returns,5))) +`  
`0.15 * (rank(ts_std_dev(close,10) / ts_std_dev(close,50))) +`  
`0.15 * (rank(-(((close - low) - (high - close)) / (high - low)))) +`  
`0.15 * (rank(ts_std_dev(volume,5)) * rank(volume / ts_mean(volume,20))), 4)`

### 7. Decline Since Local Maximum
Explanation: Measures how far the current price is below the recent 30-day maximum, decayed over time to limit contribution from older peaks.  
`decay_days = 1;`  
`rel_days_since_max = rank(ts_arg_max(close, 30));`  
`decline_pct = (vwap - close) / close;`  
`decline_pct / min(ts_decay_linear(rel_days_since_max, decay_days), 0.15)`

### 8. Alpha Core with Flow and Quality
Explanation: Combines three components:
alpha_core: short-term price momentum vs VWAP with decay, plus mean-reversion of positive days
flow: relative volume
quality: risk-adjusted returns  
`pd = ts_sum(returns > 0 ? 1 : 0,200);`  
`alpha_core = ts_decay_linear(rank(-ts_delta((close - vwap) / vwap,5)),6) + 0.4 * rank(-ts_delta(close,2)) - ts_decay_linear(rank(pd - ts_mean(pd,20)),6);`  
`flow = rank(volume / ts_mean(volume,20));`  
`quality = rank(returns / (ts_std_dev(returns,20) + 0.000001));`  
`alpha = alpha_core + 0.25 * flow + 0.25 * quality;`  
`trade_when(abs(ts_delta(close,1)) > ts_mean(abs(ts_delta(close,1)),20), alpha, 0)`
