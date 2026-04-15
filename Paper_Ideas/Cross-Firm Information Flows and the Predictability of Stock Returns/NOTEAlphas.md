## Idea 1:
1. Basic Bellwether (Lead-Lag Return)
Stock khác dẫn dắt return của stock hiện tại  
`alpha = rank(-ts_mean(indneutralize(returns, industry), 3));`  
Idea:
Lấy return ngành (proxy cho bellwether)
Giả định industry leaders phản ứng trước

2. Attention-based Leader Alpha
High-attention stocks lead low-attention stocks
```
attention = news_cap;

leader_signal = ts_rank(attention, 20) * returns;

alpha = rank(-ts_mean(leader_signal, 3));
```
Insight:
Stocks có news nhiều → phản ứng nhanh hơn
Dùng chúng làm predictor
3. News Spillover Alpha (core idea)
News của 1 stock lan sang peer stocks

```
news_shock = news_cap * abs(returns);

alpha = rank(ts_mean(indneutralize(news_shock, industry), 5));
```
Idea:
News mạnh + move mạnh → shock
Shock này lan sang industry
4. Bellwether Network Alpha (quan trọng nhất)
Dùng “network” đơn giản qua industry
```
peer_ret = indneutralize(returns, industry);

alpha = rank(-ts_delay(ts_mean(peer_ret, 3), 1));
```
Idea:
Return của peers hôm qua → predict hôm nay
Chính là lead-lag structure  

5. Central Stock (News Leader) Alpha
Stock “ở trung tâm news” dẫn dắt  
```
centrality = ts_rank(news_cap, 10);

alpha = rank(-ts_mean(centrality * returns, 3));
```
Ý nghĩa:
News nhiều liên tục → central node
Return của nó có predictive power  

6. Advanced: Low Turnover Bellwether Alpha  
Combine + giảm turnover (quan trọng khi submit)
```
peer = ts_mean(indneutralize(returns, industry), 5);
attention = ts_rank(news_cap, 20);

signal = 0.6 * peer + 0.4 * (attention * returns);

alpha = rank(-ts_mean(signal, 5));
```
## Idea 2:
1. Industry Lead–Lag Alpha
Industry leaders move first, followers catch up.
```
leader = group_mean(returns, 1, subindustry);
alpha = rank(ts_delay(leader, 1) - returns);
```
2️. Leader Momentum Spillover
```
leader = group_mean(ts_mean(returns, 5), 1, subindustry);
alpha = rank(ts_delay(leader, 1));
```
3️. Leader Surprise Signal
Unexpected industry returns propagate slowly.
```
leader_surprise = group_mean(returns - ts_mean(returns, 20), 1, subindustry);
alpha = rank(ts_delay(leader_surprise, 1));
```
4️. Leader Volatility Attention
Major announcements create high volatility.
```
leader_vol = group_mean(ts_std_dev(returns, 10), 1, subindustry);
signal = ts_delay(group_mean(returns, 1, subindustry), 1);
alpha = rank(signal * leader_vol);
```
5️. Lagging Follower Alpha
```
leader = ts_delay(group_mean(returns, 1, subindustry), 1);
alpha = rank(leader - returns);
```
6️. Market Leader Signal (Large Cap Leaders)
```
leader = group_mean(returns * rank(cap), 1, market);
alpha = rank(ts_delay(leader, 1));
```

Idea 3:
```
industry_ret = group_mean(returns, 1, industry);

alpha = ts_decay_linear(rank(industry_ret - returns), 15);
```
```
leader_strength = ts_rank(returns, 5);

alpha = ts_mean(leader_strength, 20);
```
Idea A: The Small-to-Large Sector Lead
Instead of using the whole subindustry mean as a leader, we isolate the smallest stocks in that subindustry to lead the larger ones.

Alpha Logic: Calculate the average return of the bottom 20% of stocks by market cap within a subindustry. Apply this as a delayed signal to the top 20% of stocks by market cap.
```
// Identify small stocks (Bottom 20% by Cap)
is_small = rank(cap) < 0.2;
// Mean return of small stocks in the subindustry
small_leader = group_mean(returns, is_small, subindustry);
// Apply to large stocks (Top 20%)
alpha = trade_when(rank(cap) > 0.8, ts_delay(small_leader, 1) - returns, -1);
Explanation: This captures the "Information Centrality" of smaller firms mentioned in Panel A. We bet that large caps will eventually mean-revert toward the path set by their smaller, more nimble industry peers.
```
Idea B: Cross-Industry Correlation Lead (The "Supply Chain" Alpha)
This targets the finding in Panel B that leaders can belong to different industries.

Alpha Logic: Use a "proxy" industry that typically moves first (e.g., Semiconductors) to lead a "follower" industry (e.g., Software or Consumer Electronics).
```
// Calculate mean returns for a specific 'Leader' Sector (e.g., Sector 10)
sector_leader = group_mean(returns, 1, sector == 10);
// Apply this signal to stocks in a different 'Follower' Sector (e.g., Sector 20)
raw_signal = ts_delay(sector_leader, 1);
alpha = trade_when(sector == 20, rank(raw_signal - returns), -1);
```
Explanation: This exploits economic linkages. If the "source" industry moves, the connected industry follows. By sorting within the follower industry, we capture the specific sensitivities mentioned in the text.

Idea C: Idiosyncratic Leader Momentum
Instead of using industry means, identify specific "central" stocks based on high residual returns (returns not explained by the market).

Alpha Logic: Identify stocks with the highest idiosyncratic volatility (the "news centers") and use their delayed returns to predict the rest of the universe.
```
// Find stocks with high idiosyncratic moves (proxy for 'news center')
news_center = rank(abs(returns - group_mean(returns, 1, industry))) > 0.9;
// Use their delayed return as a trend signal for the industry
leader_signal = group_mean(returns, news_center, industry);
alpha = ts_decay_linear(rank(ts_delay(leader_signal, 1) - returns), 5);
```
Explanation: This targets the "center of relevant news" concept. If the most volatile/active stocks in an industry move, the "quiet" stocks in that same industry are likely to lag and eventually follow.
