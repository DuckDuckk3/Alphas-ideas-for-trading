# Good alphas
Please NOTE that these alphas may not be submittable. These are just ingredients for you to combine with another "orthogonal" alpha or improve it to create a submittable alpha. I even utilize some of these and created submittable alphas. Good luck!

## Volume / Trend 
### Short-term momentum based on recent returns:  
`mom = rank(-ts_delta(close,5)) + 0.5 * rank(-ts_delta(close,2)) - 0.5 * rank(ts_mean(returns,5))`
 
### Momentum using deviation from VWAP:  
`alpha = ts_decay_linear(rank(-ts_delta((close - vwap)/vwap, 5)), 6)`

### Volatility-adjusted momentum:  
`rank(((1 - rank((ts_std_dev(returns, 2) / ts_std_dev(returns, 5)))) + (1 - rank(ts_delta(close, 1)))))`

## Volume / Liquidity
### Liquidity impact indicator:  
`liq_impact = abs(ts_delta(close,1)) / (volume / adv20) + rank(-ts_mean(liq_impact, 20))`

### Open vs VWAP divergence:  
`(rank((open - (ts_sum(vwap, 10) / 10))) * (-1 * abs(rank((close - vwap)))))`

### Volume-price interaction:  
`(sign(ts_delta(volume, 1)) * (-1 * ts_delta(close, 1)))`

### Short-term return vs volume correlation:  
`((-1 * rank(ts_delta(returns, 3))) * ts_corr(open, volume, 10))`

### Combined close momentum and volume ranking:  
`(((-1 * rank(ts_rank(close, 10))) * rank(ts_delta(ts_delta(close, 1), 1))) * rank(ts_rank((volume / adv20), 5)))`

## Reversal / Mean Reversion
### Mean reversion with micro-reversal:  
`rev = -(close - ts_mean(close,10)) / ts_std_dev(close,10)
micro_reversal = - ts_delta(close,2)
alpha2 = rank(rev) + rank(micro_reversal)`

### Long-term positive day count reversal, very strong:  
`pd = ts_sum(returns > 0 ? 1 : 0,200)
alpha = -ts_decay_linear(rank(pd - ts_mean(pd,20)),6)`

### Intraday position mean reversion:  
`pos = (close - low) / (high - low)
a6 = rank(-(pos - ts_mean(pos, 5)))
ts_decay_linear(a6, 6)`

## Edge / Shock

### Deviation from VWAP as edge:  
`edge = rank((close - vwap) / vwap)
alpha = ts_decay_linear(-edge, 6)`

### Volume shock combined with price move:  
`shock = rank(ts_delta(volume, 5))
price_move = rank(-ts_delta(close,5))
alpha = shock * price_move`

## Misc / Others
### High breakout reversal, returns ~13%:  
`(((ts_sum(high, 20) / 20) < high) ? (-1 * ts_delta(high, 2)) : 0)`

### High Sharpe but low fitness:  
`rank(((((-1 * returns) * adv20) * vwap) * (high - close)))`

### Very strong combination signal:  
`(scale(((ts_sum(close, 7) / 7) - close)) + (20 * scale(ts_corr(vwap, ts_delay(close, 5),230))))`

### Open/close momentum, simple:  
`rank((-1 * ((1 - (open / close))^1)))`

### Combined volume, range, and return signal:  
`((Ts_Rank(volume, 32) * (1 - Ts_Rank(((close + high) - low), 16))) * (1 - Ts_Rank(returns, 32)))`

### High Sharpe, returns good, fitness low:  
`((-1 * rank(Ts_Rank(close, 10))) * rank((close / open)))`

### Moderate, Sharpe OK, turnover slightly high:  
`(ts_rank((volume / adv20), 20) * ts_rank((-1 * ts_delta(close, 7)), 8))`

### Momentum vs VWAP over last 30 days:  
`-1 * ((close - vwap) / ts_decay_linear(rank(ts_arg_max(close, 30)), 2))`

### Multi-day trend reversal, fitness low:  
`(((1.0 - rank(((sign((close - ts_delay(close, 1))) + sign((ts_delay(close, 1) - ts_delay(close, 2)))) + sign((ts_delay(close, 2) - ts_delay(close, 3)))))) * ts_sum(volume, 5)) / ts_sum(volume, 20))`

### Basics comparision (nearly 40% returns but low sharpe):  
`rank(ebit / capex)  
+  
rank(operating_income / debt)`  

```
clv = ((close - low) - (high - close)) / (high - low);

a1 = rank(
    ts_mean(
        -clv * signed_power(volume / ts_mean(volume, 20), 1.2),
        3
    )
);
```
```
a2 = rank(-ts_delta(close, 1)) 
     + 0.5 * rank(-ts_delta(close, 3));
```

```
clv = ((close - low) - (high - close)) / (high - low);

flow = -clv * (volume / ts_mean(volume, 20));

a4 = rank(ts_mean(flow, 3))
     * rank(-ts_delta(close, 1));
```

```
clv = ((close - low) - (high - close)) / (high - low);
signal = rank(ts_mean(-clv * signed_power(volume / ts_mean(volume, 20), 1.2), 3)) 
         + rank(-ts_delta(close, 1));

a5 = trade_when(
    volume > ts_mean(volume, 20),
    signal,
    -1
);
```
`ts_arg_max(rank(ts_corr(rank(volume), rank(vwap),5)),5)`

`((-1 * ((low - close) * (open^5))) / ((low - high) * (close^5)))`

`-1 * ts_delta(((close - low) - (high - close)) / (close - low),9)` This is good but too high turnover

`rank(group_neutralize(((ts_mean(ts_delay(close, 5), 20) - close) * ts_rank(ts_corr(close, volume, 2), 5)), subindustry))`

`rank(ts_decay_linear(ts_corr(vwap, ts_mean(volume, 50),9),8)) - rank(ts_decay_linear(ts_delta(close,3),6))`

`group_neutralize(ts_decay_linear(rank(-1 * ts_delta(close, 5)), 10), subindustry)`

```
peer_ret = group_neutralize(returns, industry);

alpha = rank(-ts_delay(ts_mean(peer_ret, 3), 1));
```
```
centrality = ts_rank(news_cap, 10);

alpha = rank(-ts_mean(centrality * returns, 3));
```
```
skew = implied_volatility_put_30 - implied_volatility_call_30;

alpha = -ts_decay_linear(rank(ts_mean(skew, 5)), 20);
```
Very good but high sub-universe shape

### Fama-French five factor model
```
// 1. Size (SMB - Small Minus Big): Ưu tiên vốn hóa nhỏ
f_size = rank(-cap); 

// 2. Value (HML - High Minus Low): P/B thấp (Book-to-Market cao)
f_value = rank(book_value / cap);

// 3. Profitability (RMW - Robust Minus Weak): Lợi nhuận hoạt động trên tài sản
f_profit = rank(operating_income / assets);

// 4. Investment (CMA - Conservative Minus Aggressive): Tăng trưởng tài sản thấp
// Những doanh nghiệp đầu tư quá ồ ạt thường có hiệu suất kém hơn trong dài hạn
f_inv = rank(-(ts_delta(assets, 252) / assets));

// 5. Market / Momentum (Yếu tố bổ trợ để tạo "Strong Long")
// FF5 gốc dùng Market Beta, nhưng trong Alpha ta nên dùng Momentum 1 năm 
// để lọc ra những mã thực sự đang "chạy"
f_mom = rank(ts_delta(close, 252) / ts_delay(close, 20));

// TỔNG HỢP: Kết hợp các nhân tố (Equal Weight)
raw_ff5 = f_size + f_value + f_profit + f_inv + f_mom;

// FILTER: Chỉ Long khi có sự đồng thuận từ Momentum và Profitability (Tín hiệu Long mạnh)
// Điều này sẽ cắt giảm Turnover cực mạnh vì bạn chỉ vào lệnh khi "Thiên thời - Địa lợi"
alpha_ff5 = if_else(
    (f_mom > 0.7) && (f_profit > 0.6), 
    group_rank(raw_ff5, subindustry), 
    0
);
```

```
-rank(
    ((high - close) / (high - low))
    *
    returns
)
```

## Very nice combine alpha that acts as a filter:
```
res_mom =
-ts_regression(
    returns,
    group_mean(returns,1, subindustry),
    60
);
```
## Intraday & Overnight
```
overnight = open / ts_delay(close,1) - 1;
intraday = close / open - 1;
-rank(-overnight) * rank(intraday)
```
## VWAP Deviation
`-scale((close - vwap)/vwap)`

`b =
-ts_zscore(
    (close-vwap)/vwap,
    20
);`  
`b =
-rank(
    ((close-vwap)/vwap)
    *
    (volume/adv20)
);`
## Short-Term Volume Strength 
`power(rank(ts_mean(volume,5)/ts_mean(volume, 252)), 0.5)`
## Earnings Yield Momentum
`group_rank(ts_rank(est_epsr/close, 60), industry)`

`group_rank(ts_rank(operating_income / cap, 252), subindustry)`
