21. Noise Reduction — “clean signal”

Idea: remove random fluctuations

Typical form:

ts_mean(signal,n)

Insight:

Essential for stability
Too much → lag
22. Stabilization — “reduce flipping”

Idea: smooth transitions

Typical form:

ts_decay_linear(signal,n)

Insight:

Controls turnover
Improves consistency
