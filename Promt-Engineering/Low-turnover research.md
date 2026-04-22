# High-Sharpe, Low-Turnover Alpha Strategies (Clean & Corrected)

## Executive Summary

This document summarizes alpha families that are **empirically more likely** to achieve:
- relatively high Sharpe,
- low to moderate turnover,
- and good robustness across markets.

Focus is on **slow-moving, economically grounded signals**, not fast trading strategies.

Core families:
- Value
- Quality / Profitability
- Low Volatility / Low Beta
- Carry / Yield
- Long-Horizon Mean Reversion
- Seasonal (weak, optional)

Key principle:
> The best real-world alphas are usually **simple, stable, and well-neutralized**, not overly complex.

---

## 1. Value

### Definition
Stocks that are cheap relative to fundamentals.

### Robust implementations
Use multiple valuation ratios instead of a single one:
- Book-to-Price
- Earnings-to-Price
- Cashflow-to-Price
- EV / EBITDA (inverted)

### Example

value = rank(book_equity / market_cap)


Better (composite):

value = rank(book_to_price) + rank(earnings_to_price) + rank(cashflow_to_price)


### Construction
- Cross-sectional rank
- Long top quantile, short bottom
- Rebalance: monthly or quarterly

### Properties
- Turnover: low–moderate
- Works better with **industry neutralization**
- Very regime-dependent (can underperform for years)

---

## 2. Quality / Profitability

### Definition
Firms with strong profitability and healthy balance sheets.

### Correct signals (avoid mixing inconsistent units)
- Operating income / assets
- Gross profit / assets
- ROE (with caution)
- Cashflow / assets
- Low leverage

### Example

quality = rank(operating_income / total_assets)


Better (multi-signal):

quality = rank(opinc/assets) + rank(cashflow/assets) - rank(debt/assets)


### Construction
- Annual or quarterly updates
- Hold longer (low turnover)

### Properties
- Turnover: low
- More stable than value
- Often overlaps with low-vol

---

## 3. Low Volatility / Low Beta

### Definition
Prefer stocks with lower risk.

### Correct implementation
Use **rolling volatility**, not raw price levels:

low_vol = rank(-ts_stddev(returns, 252))


Alternative:

low_vol = rank(-beta_to_market)


### Construction
- Rebalance monthly or slower
- Often long-only tilt works well

### Properties
- Turnover: low
- Strong Sharpe historically
- Underperforms in risk-on markets

---

## 4. Carry / Yield

### Definition
Earn return from yield differences.

### Equity version (safer than FX carry for beginners):

carry = rank(dividend / price)


Or:

carry = rank(free_cashflow / market_cap)


### Notes
- Equity carry ≈ value variant
- FX/bond carry requires macro data (rates, forwards)

### Properties
- Turnover: low–moderate
- Tail risk exists (especially in FX)

---

## 5. Long-Horizon Mean Reversion

### Correct idea
Use **long horizon returns**, not short-term reversal:

reversion = rank(-ts_delta(close, 252))


Better:

reversion = rank(-ts_return(close, 252))


### Important fix
- DO NOT mix with short-term reversal unintentionally
- Optionally skip recent period:

reversion = rank(-ts_delay(ts_return(close, 252), 21))


### Properties
- Turnover: moderate
- Works best when combined with value

---

## 6. Seasonal / Calendar Effects (Weak Signal)

### Example

signal = (month >= 11 or month <= 4)


### Reality check
- Weak standalone alpha
- Often disappears after costs
- Use only as a minor overlay

---

## 7. Factor Combination (VERY IMPORTANT)

Single signals are fragile.

### Correct way

alpha = rank(value) + rank(quality) + rank(low_vol)


Or multiplicative (stronger filtering):

alpha = rank(value) * rank(quality)


### Why this works
- Reduces noise
- Lowers correlation to pool
- Improves stability

---

## 8. Neutralization (CRITICAL)

Most raw alphas are misleading without this.

### Correct usage

alpha = group_neutralize(alpha, industry)


Alternative:

alpha = alpha - group_mean(alpha, industry)


### What it removes
- Sector bias
- Hidden beta exposures
- Structural distortions

---

## 9. Common Mistakes (Fixing “Sai tùm lum”)

Avoid these:

❌ Mixing incompatible units  
→ e.g. EV / returns, or price / volatility

❌ Using raw values instead of ranks  
→ always prefer `rank()` for cross-sectional signals

❌ Ignoring negative denominators  
→ cashflow, earnings can be negative → unstable ratios

❌ Too short lookbacks for slow signals  
→ fundamentals need longer horizons

❌ No neutralization  
→ leads to fake alpha (just sector bets)

❌ Overfitting with too many conditions  

---

## 10. Practical Defaults (WorldQuant-style)

Good baseline setup:

- decay: 8–20
- delay: 1
- neutralization: industry
- truncation: 0.01–0.05
- universe: top liquid stocks

---

## 11. Final Takeaways

- Best low-turnover alphas = **Value + Quality + Low Vol**
- Always:
  - rank signals
  - neutralize
  - combine multiple factors
- Avoid:
  - noisy short-term signals
  - unstable ratios
  - overfitting

> If an alpha looks too strong without neutralization, it's probably wrong.
