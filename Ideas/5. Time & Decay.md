14. Time Horizon — “scale matters”

Idea: same signal behaves differently at different horizons

Typical form:

ts_sum(returns, short / mid / long)

Insight:

Short = noisy
Medium = optimal
Long = stable but lagging
15. Decay — “recent info matters more”

Idea: newer data is more relevant

Typical form:

ts_decay_linear(signal,n)

Insight:

Reduces noise
Controls turnover
