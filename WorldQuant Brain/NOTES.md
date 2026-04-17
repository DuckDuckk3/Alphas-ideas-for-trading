Alpha 1: VALUE FACTOR (BOOK-TO-MARKET RATIO)

Logic: Stocks with higher book-to-market (B/M) ratios are undervalued and tend to outperform over time.

Alpha Formula: alpha = rank(book_value_per_share / market_price)

Explanation:
- Use the ratio of book value per share to the current market price as a measure of value.
- Higher ranks represent undervalued stocks with potential for better returns.


16


PH82915
Osmosis AllocatorMaster consultant
1 year ago
Based on your research paper, I have a few simple alpha ideas as follows. Thank you for sharing such an interesting and accessible paper!

Alpha 2: PROFITABILITY REVERSAL

Logic: Stocks with unusually high profitability may experience reversal due to overreaction by the market.

Alpha Formula: alpha = -ts_zscore(net_income / total_assets, 60)

Explanation:
- Calculate the profitability (return on assets) as net income divided by total assets.
- Use a z-score to identify stocks with abnormally high profitability, and expect a reversal effect.


16


PH82915
Osmosis AllocatorMaster consultant
1 year ago
Based on your research paper, I have a few simple alpha ideas as follows. Thank you for sharing such an interesting and accessible paper!

Alpha 3: INVESTMENT EFFECT (ASSET GROWTH)

Logic: Firms that invest heavily (high asset growth) tend to underperform due to overextension or inefficiencies.

Alpha Formula: alpha = -ts_delta(total_assets, 252)

Explanation:
- Measure the growth in total assets over the past year.
- Negative alpha indicates that stocks with higher asset growth may have lower future returns.


16


PH82915
Osmosis AllocatorMaster consultant
1 year ago
Based on your research paper, I have a few simple alpha ideas as follows. Thank you for sharing such an interesting and accessible paper!


Alpha 4: SIZE PREMIUM (MARKET CAP)

Logic: Smaller-cap stocks tend to outperform larger-cap stocks over time.

Alpha Formula: alpha = rank(1 / market_cap)

Explanation:
- Rank stocks based on the inverse of their market capitalization.
- Smaller ranks (larger values) favor smaller companies with better return potential.


-rank((close - open) / (high - low))
WE CAN COMBINE: pv =
rank(volume / ts_mean(volume,20));

price_pressure =
-rank((close - open) / (high - low ));

alpha =
0.6 * price_pressure
+
0.4 * pv;
OR ANOTHER VERSION: 


-1 * ts_rank(rank(low),9)
- rank(ts_corr(close, ts_mean(volume, 50),8)) < rank(ts_delta(close,2))

(rank(high * low) - rank(vwap)) * rank(sales / assets) * rank(volume / ts_mean(volume, 20))

alpha = rank(ts_sum(ts_delta(vec_avg(scl12_alltype_buzzvec), 3), 60)) > 0.5
        ? rank(ts_delta(vec_avg(scl12_alltype_buzzvec), 3))
        : rank(-ts_delta(close, 2));s
clv = ((close - low) - (high - close)) / (high - low);

vol_signal = -clv * signed_power(volume / ts_mean(volume, 20), 1.2);

alpha = rank(ts_mean(vol_signal, 3)) 
        + rank(-ts_delta(close, 1)) 
        + rank(ts_delta(news_cap, 1)); turnover too high but good overall

Good dataset with low corr:
1️⃣ Fundamental Mispricing Alphas (ít bị crowd nhất)

Alpha dựa trên fundamental accounting variables thường có:

corr với pool thấp
stability cao
payout tốt

Ví dụ idea:

profitability signal
rank(fnd2_operinc / fnd2_assets)
accrual anomaly
rank((fnd2_netinc - fnd2_cashflow) / fnd2_assets)
investment anomaly
rank(-ts_delta(fnd2_assets, 4))

Những anomaly này xuất phát từ research trong Financial Economics.

📌 Tip:
Fundamental alpha thường cần

decay = 8–20
delay = 1
2️⃣ Industry Information Flow

Thông tin thường lan trong cùng industry trước khi phản ánh đầy đủ vào giá.

Ví dụ:

rank(ts_delta(group_mean(close, industry),5))

hoặc

rank(group_mean(returns, industry))

Ý tưởng:

nếu cả ngành đang tăng → cổ phiếu còn lại có thể follow.

3️⃣ Lead–Lag Alphas

Một số cổ phiếu dẫn dắt cổ phiếu khác.

Ví dụ:

rank(ts_corr(volume, returns, 20))

hoặc

rank(ts_delta(group_mean(volume, industry),5))

Những alpha này thường:

turnover vừa
corr pool thấp
4️⃣ Analyst Revision Signals

Các revision của analyst có predictability mạnh.

Ví dụ:

rank(ts_delta(fnd2_eps_est, 4))

hoặc

rank(ts_mean(fnd2_eps_est, 4))

Research cho thấy:

upward revision → positive return drift.

5️⃣ Volatility Structure

Nhiều alpha mạnh đến từ volatility dynamics.

Ví dụ:

rank(-ts_stddev(returns,20))

hoặc

rank(ts_delta(ts_stddev(returns,20),5))

Ý tưởng:

volatility spike → reversal
6️⃣ Liquidity Signals

Liquidity shocks thường dự báo return.

Ví dụ:

rank(volume / ts_mean(volume,20))

hoặc

rank(ts_corr(volume, returns, 10))
7️⃣ Cross-Sectional Reversion

Một trong những alpha ổn định nhất.

Ví dụ:

rank(-ts_delta(close,5))

Nhưng phải neutralize:

group_neutralize(rank(-ts_delta(close,5)), industry)
8️⃣ Factor Combination (quan trọng)

Alpha tốt thường không chỉ có 1 signal.

Ví dụ:

rank(momentum)
*
rank(value)
hoặc
rank(profitability) + rank(momentum)
Combination thường:
corr pool thấp hơn
stability cao hơn
9️⃣ Neutralization (cực kỳ quan trọng)

Rất nhiều alpha yếu trở nên mạnh sau khi neutralize:
group_neutralize(alpha, industry)
hoặc
alpha - group_mean(alpha, industry)
Điều này loại bỏ:
sector bias
market exposure
