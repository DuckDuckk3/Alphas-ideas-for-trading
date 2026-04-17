# Executive Summary

The paper **“The Momentum of News” (Wang, Zhang, and Zhu, 2018)** documents a persistent phenomenon known as **news momentum**: stocks that receive positive news sentiment in the recent past tend to continue receiving positive news and subsequently generate higher future returns. Conversely, firms with negative news coverage tend to experience continued negative sentiment and weaker performance.

A trading strategy that exploits this effect—**long stocks with improving positive news sentiment and short stocks with deteriorating sentiment**—produces economically significant abnormal returns. The authors report an alpha of roughly **0.3–0.4% per month** after controlling for standard risk factors.

This document translates the economic intuition of the paper into **implementable alpha signals for the WorldQuant BRAIN platform**, using available BRAIN datafields. The alphas leverage:

* News sentiment
* News volume and event intensity
* Time-decay weighting of information
* Cross-sectional ranking
* Risk neutralization
* Combination with orthogonal signals (price momentum, volatility, fundamentals)

The report proposes **multiple alpha formulations** designed specifically for BRAIN-style factor construction.

---

# 1. The Concept of News Momentum

The key insight of the paper is that **news sentiment itself exhibits momentum**.

Let

* ( S_{i,t} ) = aggregated news sentiment for stock (i) at time (t)

Empirically,

[
S_{i,t+1} \sim S_{i,t}
]

meaning **current sentiment predicts future sentiment**. Because markets often **underreact to information**, this persistence leads to predictable return patterns.

Therefore, if a stock experiences **increasingly positive news**, the market typically incorporates this information **gradually**, causing the price to continue drifting upward.

Similarly, persistent negative coverage leads to downward drift.

This phenomenon is analogous to **price momentum**, but driven by **information flow rather than past returns**.

---

# 2. News Data Available on WorldQuant BRAIN

Several BRAIN datasets allow the construction of news-based signals.

Typical datafields include:

### News Sentiment

Examples:

* `rp_ess` — Event Sentiment Score
* `rp_cesi` — Cumulative Event Sentiment Index

These measure how positive or negative a news event is.

---

### News Volume / Coverage

Examples:

* `rp_eventcount`

Measures the number of news events about a firm.

Higher coverage often implies stronger information flow.

---

### Positive vs Negative News

Possible fields:

* `rp_positiveevents`
* `rp_negativeevents`

These allow construction of **net sentiment indicators**.

---

### News Relevance / Importance

Example:

* `rp_relevancescore`

Weights news events by how strongly they relate to the company.

---

### Market Data

Used to adjust signals:

* `close`
* `volume`
* `returns`
* `beta`
* `atr`

---

# 3. Core Alpha Signals Inspired by News Momentum

Below are **practical alpha formulations designed for WorldQuant BRAIN syntax**.

All signals should typically be **cross-sectionally ranked**.

---

# Alpha 1 — News Sentiment Momentum

Measures improvement in sentiment.

```
rank(ts_mean(rp_ess,20) - ts_mean(rp_ess,120))
```

Interpretation

* Short-term sentiment vs long-term sentiment
* Captures **recent news improvement**

---

# Alpha 2 — Exponentially Weighted News Sentiment

Prioritizes recent news.

```
rank(ts_decay_linear(rp_ess,60))
```

Intuition

Recent news has stronger market impact than older information.

---

# Alpha 3 — News Sentiment × News Coverage

Large news flow increases reliability.

```
rank(ts_mean(rp_ess,20) * log(1 + ts_sum(rp_eventcount,20)))
```

Economic logic

* One positive article → weak signal
* Many positive articles → stronger information signal

---

# Alpha 4 — News Sentiment Shock

Detects sudden information shocks.

```
rank((rp_ess - ts_mean(rp_ess,60)) / ts_stddev(rp_ess,60))
```

This identifies **unusual sentiment changes** relative to history.

---

# Alpha 5 — News Momentum Adjusted by Price Reaction

Measures **underreaction to news**.

```
rank(ts_mean(rp_ess,20) * (1 - ts_mean(returns,20)))
```

Logic

* Positive news
* Weak recent price reaction

→ likely **future drift upward**

---

# Alpha 6 — News Sentiment vs Market Attention

Combines sentiment with market activity.

```
rank(ts_mean(rp_ess,20) * log(volume))
```

Idea

If positive news occurs while trading attention rises, it may signal **information diffusion**.

---

# Alpha 7 — Net News Balance

Measures dominance of positive vs negative coverage.

```
rank((ts_sum(rp_positiveevents,20) - ts_sum(rp_negativeevents,20)) /
     (1 + ts_sum(rp_eventcount,20)))
```

This captures **directional tone of news flow**.

---

# 4. Orthogonal Signal Combination

News momentum works best when combined with **independent factors**.

Good candidates include:

### Price Momentum

```
rank(ts_mean(returns,120))
```

---

### Volatility Signal

```
rank(-ts_stddev(returns,60))
```

---

### Liquidity Signal

```
rank(log(volume))
```

---

### Fundamental Quality

Examples

```
rank(roe)
rank(gross_margin)
```

---

### Multi-Factor Example

A combined alpha:

```
rank(ts_decay_linear(rp_ess,60))
+
0.5 * rank(ts_mean(returns,120))
-
0.3 * rank(ts_stddev(returns,60))
```

This integrates

* news momentum
* price trend
* volatility adjustment

---

# 5. Risk Neutralization

To prevent structural bias, signals should be neutralized.

Common neutralizations:

### Sector Neutral

```
group_neutralize(alpha, sector)
```

---

### Market Cap Neutral

```
group_neutralize(alpha, market_cap_bucket)
```

---

### Beta Neutral

```
group_neutralize(alpha, beta_bucket)
```

---

# 6. Recommended Alpha Construction Pipeline

A practical workflow on BRAIN:

1️⃣ Compute raw news signal
2️⃣ Apply time decay or smoothing
3️⃣ Cross-sectional ranking
4️⃣ Neutralize sector and size
5️⃣ Combine with orthogonal signals
6️⃣ Backtest with turnover constraints

---

# 7. Key Economic Mechanisms Behind News Momentum

The effect arises from several behavioral forces:

### Information Diffusion

News spreads gradually across investors.

---

### Investor Attention

Many investors react only after repeated coverage.

---

### Institutional Frictions

Large investors cannot rebalance immediately.

---

### Media Amplification

Positive news tends to cluster.

---

# 8. Best Practical Strategy

Empirically, the most stable signals usually combine:

* **Sentiment momentum**
* **News volume**
* **price underreaction**

Example final alpha:

```
rank(ts_decay_linear(rp_ess,60))
* rank(log(1 + ts_sum(rp_eventcount,20)))
* rank(1 - ts_mean(returns,20))
```

This captures:

* persistent sentiment
* information intensity
* delayed market reaction

---

✅ **Summary**

News momentum provides a powerful alternative information signal.
When implemented with BRAIN datafields and combined with orthogonal factors, it can generate **robust cross-sectional alpha signals**.

The most effective designs:

* emphasize **recent sentiment**
* weight **information intensity**
* detect **market underreaction**
* remain **risk-neutralized**
* combine with **price and volatility factors**

---

If you want, I can also show you **10–15 advanced news alphas that are more likely to pass WorldQuant BRAIN tests (fitness, turnover, Sharpe)** and explain **why most news alphas fail on BRAIN and how to fix them.** 📈
