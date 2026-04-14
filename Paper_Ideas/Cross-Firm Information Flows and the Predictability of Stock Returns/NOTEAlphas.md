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
signal = ts_delay(group_mean(returns, subindustry, 1), 1);
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

