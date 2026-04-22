## Idea 1: 
### The Spin Trap
```
ts_decay_linear(
    rank(ts_mean(returns, 120)) - rank(ts_mean(scl12_sentiment, 5)),
    8
)
```
### Delayed News Reaction
```
group_neutralize(
    ts_decay_linear(rank(ts_mean(scl12_sentiment, 22)) - rank(ts_mean(returns, 22)), 10),
    subindustry
)
```
### News Momentum
```
ts_decay_linear(
    group_neutralize(
        rank(stddev(returns, 60)) * rank(ts_delta(scl12_sentiment, 3)) * rank(returns),
        sector
    ),
    5
)
```
### Stationarity Shock Reversion
```
ts_decay_linear(
    -(rank(scl12_sentiment - ts_mean(scl12_sentiment, 20)) + rank(returns - ts_mean(returns, 20))),
    4
)
```
### Information Delay (Size-Based Inconsistency)
```
ts_decay_linear(
    rank(1 / cap) * (rank(ts_mean(scl12_sentiment, 5)) - rank(returns)),
    6
)
```
Datafield:
scl12_sentiment, psq_sentiment, nws12_sentiment

## Idea 2:
Strategy 1: The Pure SI Sentiment Signal
- Concept: The research states that SI news has unique predictive power. We use news sentiment as the proxy for SI and neutralize it against industry sectors to remove broad market "QI" noise.
- Logic: Buy stocks with high unique news sentiment that isn't just driven by a general sector trend.
- Data Fields: * news_sentiment_avg (or RavenPack sentiment fields)  
news_sentiment_count (to ensure significance)

`group_neutralize(vec_avg(nws18_bam), subindustry)`
`sig = rank(vec_avg(nws18_bee)) - rank(ts_mean(returns, 5));`
`group_neutralize(vec_avg(nws18_bee), subindustry)`

Strategy 2: SI Divergence (Neutralizing the "Reversal Myth")
- Concept: The paper proves the SI effect isn't just a short-term reversal. This alpha looks for stocks where sentiment is high, but the price hasn't "bounced" yet, intentionally ignoring the short-term return factor.
- Logic: High sentiment (SI) paired with a negative or neutral 1-day return (QI/Reversal). This targets the "under-reaction" to specific news.
- Data Fields: * news_sentiment_avg
returns (daily return)

`rank(news_sentiment_avg) - rank(ts_returns(close, 1))`
