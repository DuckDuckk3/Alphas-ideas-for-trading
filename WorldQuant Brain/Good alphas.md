## Good alphas
Please NOTE that these alphas may not be submittable. These are just ingredients for you to combine with another "orthogonal" alpha or improve it to create a submittable alpha. I even utilize some of these and created submittable alphas. Good luck!

### Volume / Trend
Short-term momentum based on recent returns:  
`mom = rank(-ts_delta(close,5)) + 0.5 * rank(-ts_delta(close,2)) - 0.5 * rank(ts_mean(returns,5))`

Momentum using deviation from VWAP:  
`alpha = ts_decay_linear(rank(-ts_delta((close - vwap)/vwap, 5)), 6)`

Volatility-adjusted momentum:  
`rank(((1 - rank((ts_std_dev(returns, 2) / ts_std_dev(returns, 5)))) + (1 - rank(ts_delta(close, 1)))))`

### Volume / Liquidity
Liquidity impact indicator:  
`liq_impact = abs(ts_delta(close,1)) / (volume / adv20) + rank(-ts_mean(liq_impact, 20))`

Open vs VWAP divergence:  
`(rank((open - (ts_sum(vwap, 10) / 10))) * (-1 * abs(rank((close - vwap)))))`

Volume-price interaction:  
`(sign(ts_delta(volume, 1)) * (-1 * ts_delta(close, 1)))`

Short-term return vs volume correlation:  
`((-1 * rank(ts_delta(returns, 3))) * ts_corr(open, volume, 10))`

Combined close momentum and volume ranking:  
`(((-1 * rank(ts_rank(close, 10))) * rank(ts_delta(ts_delta(close, 1), 1))) * rank(ts_rank((volume / adv20), 5)))`

### Reversal / Mean Reversion
Mean reversion with micro-reversal:  
`rev = -(close - ts_mean(close,10)) / ts_std_dev(close,10)
micro_reversal = - ts_delta(close,2)
alpha2 = rank(rev) + rank(micro_reversal)`

Long-term positive day count reversal, very strong:  
`pd = ts_sum(returns > 0 ? 1 : 0,200)
alpha = -ts_decay_linear(rank(pd - ts_mean(pd,20)),6)`

Intraday position mean reversion:  
`pos = (close - low) / (high - low)
a6 = rank(-(pos - ts_mean(pos, 5)))
ts_decay_linear(a6, 6)`

### Edge / Shock

Deviation from VWAP as edge:  
`edge = rank((close - vwap) / vwap)
alpha = ts_decay_linear(-edge, 6)`

Volume shock combined with price move:  
`shock = rank(ts_delta(volume, 5))
price_move = rank(-ts_delta(close,5))
alpha = shock * price_move`

### Misc / Others
High breakout reversal, returns ~13%:  
`(((ts_sum(high, 20) / 20) < high) ? (-1 * ts_delta(high, 2)) : 0)`

High Sharpe but low fitness:  
`rank(((((-1 * returns) * adv20) * vwap) * (high - close)))`

Very strong combination signal:  
`(scale(((ts_sum(close, 7) / 7) - close)) + (20 * scale(ts_corr(vwap, ts_delay(close, 5),230))))`

Open/close momentum, simple:  
`rank((-1 * ((1 - (open / close))^1)))`

Combined volume, range, and return signal:  
`((Ts_Rank(volume, 32) * (1 - Ts_Rank(((close + high) - low), 16))) * (1 - Ts_Rank(returns, 32)))`

High Sharpe, returns good, fitness low:  
`((-1 * rank(Ts_Rank(close, 10))) * rank((close / open)))`

Moderate, Sharpe OK, turnover slightly high:  
`(ts_rank((volume / adv20), 20) * ts_rank((-1 * ts_delta(close, 7)), 8))`

Momentum vs VWAP over last 30 days:  
`-1 * ((close - vwap) / ts_decay_linear(rank(ts_arg_max(close, 30)), 2))`

Multi-day trend reversal, fitness low:  
`(((1.0 - rank(((sign((close - ts_delay(close, 1))) + sign((ts_delay(close, 1) - ts_delay(close, 2)))) + sign((ts_delay(close, 2) - ts_delay(close, 3)))))) * ts_sum(volume, 5)) / ts_sum(volume, 20))`
