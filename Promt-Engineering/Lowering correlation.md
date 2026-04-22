# Low-Correlation Alpha Ideas (High Stability & Strong Research Backing)

## 1. Fundamental Mispricing Alphas (Low Crowding)

Fundamental accounting-based alphas typically have:
- Low correlation with the pool
- High stability
- Strong payout

### Example Ideas:

**Profitability Signal**

rank(fnd2_operinc / fnd2_assets)


**Accrual Anomaly**

rank((fnd2_netinc - fnd2_cashflow) / fnd2_assets)


**Investment Anomaly**

rank(-ts_delta(fnd2_assets, 4))


These anomalies are well-documented in financial economics research.

Tip:
- decay = 8–20
- delay = 1

---

## 2. Industry Information Flow

Information tends to propagate within industries before fully reflecting in prices.

### Example Ideas:

rank(ts_delta(group_mean(close, industry), 5))

rank(group_mean(returns, industry))


### Intuition:
- If the industry is trending up, lagging stocks may follow.

---

## 3. Lead–Lag Alphas

Some stocks systematically lead others.

### Example Ideas:

rank(ts_corr(volume, returns, 20))

rank(ts_delta(group_mean(volume, industry), 5))


### Characteristics:
- Moderate turnover
- Low correlation with alpha pool

---

## 4. Analyst Revision Signals

Analyst estimate revisions have strong predictive power.

### Example Ideas:

rank(ts_delta(fnd2_eps_est, 4))

rank(ts_mean(fnd2_eps_est, 4))


### Insight:
- Upward revisions → positive return drift

---

## 5. Volatility Structure

Volatility dynamics can produce strong signals.

### Example Ideas:

rank(-ts_stddev(returns, 20))

rank(ts_delta(ts_stddev(returns, 20), 5))


### Intuition:
- Volatility spikes → potential reversal

---

## 6. Liquidity Signals

Liquidity shocks often predict future returns.

### Example Ideas:

rank(volume / ts_mean(volume, 20))

rank(ts_corr(volume, returns, 10))


---

## 7. Cross-Sectional Reversion

One of the most stable alpha families.

### Example Idea:

group_neutralize(rank(-ts_delta(close, 5)), industry)


---

## 8. Factor Combination (Critical)

Strong alphas usually combine multiple signals.

### Example Ideas:

rank(momentum) * rank(value)

rank(profitability) + rank(momentum)


### Benefits:
- Lower correlation with pool
- Higher stability

---

## 9. Neutralization (Very Important)

Many weak alphas become strong after proper neutralization.

### Methods:

group_neutralize(alpha, industry)


or


alpha - group_mean(alpha, industry)


### Purpose:
- Remove sector bias
- Reduce unintended market exposure
