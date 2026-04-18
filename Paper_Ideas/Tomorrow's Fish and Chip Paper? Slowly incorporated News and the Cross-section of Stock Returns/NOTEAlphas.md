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
