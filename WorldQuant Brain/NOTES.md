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
