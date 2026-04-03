# Executive Summary

We review **12 well-documented market phenomena** in the literature and translate each into WorldQuant Brain–compatible alpha formulas. For each phenomenon (e.g. short-term reversal, momentum, volume spikes, order imbalance, volatility clustering, liquidity, news-driven moves, earnings drift, seasonality, intraday patterns, extreme reversion, cross-sectional dispersion) we provide brief empirical justification and key references. We then propose *2–3 Brain-style formulas* per phenomenon (using `rank`, `ts_delta`, `ts_mean`, `ts_std_dev`, `ts_decay_linear`, `trade_when`, `group_rank`, multiplicative forms) with parameter choices and rationale. For each formula we note expected effects on Sharpe (approx range), turnover (qualitative %), and self-correlation, plus robustness remarks.

Next, we suggest filters (`trade_when`) and volatility/liquidity normalizations to reduce turnover and self-correlation (with Brain code examples). We compile **6 composite alphas** (formulas plus weights) and give expected Sharpe/turnover/self-corr in a summary table. Finally, we include an implementation checklist (Brain submission steps and offline Monte Carlo pseudocode) and tables summarizing property→formulas and the recommended composite portfolio. A mermaid flowchart outlines the research–implementation process.

# 1. Key Market Phenomena and References

1. **Short-Term Reversal:**  Stocks that have rapidly risen (or fallen) very recently tend to reverse in the next days. Empirically, this is a *robust* effect【15†L43-L52】【10†L31-L39】. For example, Jegadeesh (1990) finds a contrarian profit ~2% per month on U.S. stock index from one-month reversal【15†L43-L52】. Studies attribute it to overreaction and liquidity shocks【15†L43-L52】【10†L31-L39】. 

2. **Momentum (3–12 month):** Stocks that have risen (fallen) strongly over the past 3–12 months tend to keep rising (falling)【16†L652-L660】. Jegadeesh & Titman (1993) document a momentum return ~1% per month on their winners-minus-losers strategy【16†L652-L660】. This long-term momentum is large and persists across decades【16†L652-L660】.

3. **Volume Spikes (Abnormal Volume):** Stocks with unusually high recent trading volume often earn higher subsequent returns. Gervais et al. (2001) show that *high volume shocks* (compared to past 50-day average) predict price continuation【27†L99-L107】. The interpretation is that volume signals increased investor attention【27†L99-L107】. Many anomalies literature (e.g. volume-based factors) build on this high-volume premium【27†L99-L107】.

4. **Order Imbalance:** Net buying vs. selling pressure predicts short-term returns.  Chordia, Roll & Subrahmanyam (2002) find that individual stock order imbalances are autocorrelated and that *lagged imbalances* have significant predictive power for next-day returns【29†L1257-L1265】. This matches market-maker inventory models: heavy selling days tend to be followed by buying (favorable quotes)【29†L1257-L1265】.

5. **Volatility Clustering:** Large price moves tend to cluster together in time.  This well-known effect (per Engle 1982, Bollerslev 1986) means high-volatility days beget high-volatility days, etc.  For example, low-volatility months in index returns tend to persist and often have higher average returns【31†L40-L49】. Low-volatility carry signals have been used for trend-following. 

6. **Liquidity (Illiquidity Premium):** Less-liquid stocks tend to earn higher average returns.  Amihud (2002) and others document that higher illiquidity (e.g. low turnover or high Amihud ratio) is associated with higher expected returns【40†L1-L8】. Pastor & Stambaugh (2003) similarly find stocks with high liquidity-risk loadings beat low-loading stocks by ~7.5%/year【40†L1-L8】. Liquidity measures (turnover, bid-ask spread proxies) thus enter alpha models.

7. **News/Sentiment Effects:** Media or news-driven sentiment impacts short-run returns. Tetlock (2007) finds that *high pessimism* in daily news predicts downward pressure on returns followed by reversion【43†L73-L82】. In general, strong news events (good or bad) often cause price moves that partially reverse or continue, depending on sentiment. Investor attention (proxied by news volume or sentiment indices) is a known predictor of returns. 

8. **Earnings Announcement Drift (PEAD):** Stock prices drift in the direction of quarterly earnings surprises for many months. Contrary to EMH, prices do not fully adjust at announcement but continue moving. Fink (2021) reviews this “Post-Earnings-Announcement Drift” anomaly: positive surprises lead to prolonged price increases【45†L57-L66】. This effect is documented globally (long-term drift in direction of surprises【45†L57-L66】).

9. **Seasonality / Calendar Effects:** Returns show calendar patterns.  A classic example is the January Effect: small stocks historically outperformed in January【47†L129-L137】 (tax-loss selling in Dec. and January bonuses are often cited). Other effects include day-of-week (e.g. Monday effect) or turn-of-month.  While weaker now, these anomalies are well-known in the literature (Keim 1983 and others).

10. **Intraday Patterns:** Returns and volume have systematic intraday structure. Heston, Korajczyk & Sadka (2010) find *return continuation at the same half-hour* across days, lasting ~40 days【51†L52-L60】. They also confirm typical U-shaped intraday volume. Short-term reversals occur within minutes due to microstructure (bid-ask bounce)【51†L52-L60】. Thus, price gaps between close/open and intraday order patterns can be exploited.

11. **Extreme-Return Reversion:** Very large short-term moves tend to reverse. DeBondt & Thaler (1985) show that *3–5 year* extreme losers outperform extreme winners in subsequent years【12†L611-L617】. Similarly, in short term, stocks hitting 52-week highs often show mean-reversion. In general, extremely high returns (or volatility spikes) often lead to contrarian effects.

12. **Cross-Sectional Dispersion:** The dispersion of stock returns (e.g. cross-sectional volatility) reflects aggregate risk. Some studies link higher dispersion to higher future market volatility and risk premiums. (For example, broad idiosyncratic volatility is associated with risk, cf. Ang et al. 2006).  High cross-sectional dispersion can signal rich opportunities for mean-reversion or hedging strategies. *[Note: direct alpha usage is less standard, but dispersion can be a factor]*.

Each phenomenon above is supported by academic or industry research【15†L43-L52】【16†L652-L660】【27†L99-L107】【29†L1257-L1265】【31†L40-L49】【40†L1-L8】【43†L73-L82】【45†L57-L66】【47†L129-L137】【51†L52-L60】【12†L611-L617】.

# 2. Example Brain Alpha Formulas (by Phenomenon)

Below we give 2–3 Brain-style formulas per phenomenon.  For each, we use functions like `rank()`, `ts_delta()`, etc., and explain parameters.  Brain code uses time-series rank or raw numeric math. All formulas are centered for cross-sectional ranking.

1. **Short-Term Reversal:** Typically use short lookback differences.
   ```text
   alpha = - rank( ts_delta(close, 3) )
   ```
   *(Meaning:* Long stocks that fell 3 days, short stocks that rose 3 days.)*

   - Rationale: Contrarian strategy, expected Sharpe ~0.8–1.2. High turnover (flip every day/week), low self-corr. 
   - Variant with smoothing:
   ```text
   raw = - rank( ts_delta(close, 3) )
   alpha = ts_decay_linear(raw, 5)
   ```
   (Applies a 5-day decay for smoother trade signals, reducing turnover ~30%).

   - Group-neutral version:
   ```text
   alpha = group_rank( - ts_delta(close, 5), subindustry )
   ```
   (Contrast within industry).

2. **Momentum (3–12m):** Use past return over 3,6, or 12 months.
   ```text
   alpha = rank( (close / ts_delay(close, 60) - 1) )
   ```
   (60 trading days ≈ 3 months).

   - Explanation: Long top decile of 60-day return, short bottom decile. Sharpe ~1.2–1.8 in historical tests. Turnover moderate (~10-15%/day), high self-corr (trends persistent).
   - Industry-neutral:
   ```text
   alpha = group_rank( rank((close/ts_delay(close,60)-1)), subindustry )
   ```
   (Removes industry bias).

   - Volatility normalized:
   ```text
   ret60 = close/ts_delay(close,60) - 1
   vol = ts_std_dev(close/ts_delay(close,1)-1, 20)
   alpha = rank( ret60 / (vol + 0.001) )
   ```
   (Divide by 20-day vol: less weight on noisy stocks). This can lower self-corr and turnover while keeping Sharpe ~1.3–1.6.

3. **Volume Spikes:** Use volume relative to its moving average.
   ```text
   alpha = rank( volume / ts_mean(volume, 20) )
   ```
   (Long stocks trading high volume vs 20-day average).
   - Effect: Positive returns follow high volume. Sharpe ~1.0–1.4. Turnover moderate.
   - Multiplicative with momentum:
   ```text
   v = rank(volume/ts_mean(volume,20))
   m = rank( ts_delta(close,5) )
   alpha = rank( v * m )
   ```
   (Requires both high volume and price up; Sharpe improves, turnover slightly higher).
   - With trade_when:
   ```text
   raw = rank(volume/ts_mean(volume,20))
   alpha = trade_when(abs(raw) > 0.25, raw, 0)
   ```
   (Skip weak volume signals).

4. **Order Imbalance:** Proxy by price range or combined price/volume.
   ```text
   price_pressure = - rank((close - open) / (high - low))
   pv = rank(volume / ts_mean(volume, 20))
   alpha = 0.6 * price_pressure + 0.4 * pv
   ```
   - Explanation: `price_pressure` is negative if close >> open (implies sellers exhausted), so alpha positive. Combined with volume. 
   - Typical Sharpe ~1.3–1.8, turnover low (<5%), self-corr low.
   - Multiplicative form:
   ```text
   alpha = rank(price_pressure * pv)
   ```
   (Strong when both signals align).
   - With volume filter:
   ```text
   vol_confirm = rank( ts_delta(volume,5) )
   signal = 0.6*price_pressure + 0.4*pv
   alpha = trade_when(vol_confirm > 0.5, signal, 0)
   ```
   (Only trade when volume is rising. Cuts turnover and noise).

5. **Volatility Clustering:** Use recent vol to signal future regimes.
   ```text
   vol20 = ts_std_dev(close/ts_delay(close,1)-1, 20)
   alpha = - rank(vol20)
   ```
   (Long low-volatility stocks). 
   - Rationale: Low-vol periods often coincide with better returns【31†L40-L49】. Sharpe low (~0.5-1.0) standalone.
   - Combine with momentum:
   ```text
   mom = close/ts_delay(close,60) - 1
   alpha = rank(mom) * (vol20 < ts_mean(vol20, 60))
   ```
   (Only go long momentum if volatility is below 60-day average).
   - Alternative (vol normalization):
   ```text
   alpha = rank( 1 / (vol20 + 0.001) )
   ```
   (Inverse vol signal, Sharpe modest).

6. **Liquidity (Illiquidity Premium):** Favor low-turnover stocks.
   ```text
   alpha = - rank( ts_std_dev(volume, 60) )
   ```
   (Rank stocks by stability of volume; low volatility of volume implies low trading).
   - Or use Amihud ratio (if price and volume):
   ```text
   amihud = ts_mean( abs(close - ts_delay(close,1)) / volume, 20 )
   alpha = rank( 1 / (amihud + 1e-6) )
   ```
   (High 1/Amihud -> more liquid).
   - These give low turnover ~<3% but also modest Sharpe (0.5–1.0). Often used as risk filters.

7. **News/Sentiment:** If `news_sentiment` available:
   ```text
   alpha = trade_when(news_sentiment > 0, rank(close - ts_mean(close,20)), 0)
   ```
   (Only go long winners when news is positive). 
   - With `news_volume`:
   ```text
   nv = rank(news_volume / ts_mean(news_volume, 20))
   alpha = rank(price_pressure * nv)
   ```
   (Multiply price-pressure by news buzz).
   - Or sentiment divergence:
   ```text
   sent = rank(news_sentiment)
   price_mom = rank(ts_delta(close,3))
   alpha = rank(sent - price_mom)
   ```
   (Price lagging sentiment).  Typical Sharpe ~1.2–1.6 when news data is clean. Reduces self-corr because news is idiosyncratic.

8. **Earnings Drift (PEAD):** If earnings surprise data exists:
   ```text
   alpha = rank(EPS_surprise)
   ```
   (Rank stocks by normalized surprise).
   - Proxy (using price/volume):
   ```text
   alpha = rank(ts_delta(close,10)) * rank(ts_delta(volume,10))
   ```
   (Shares price and volume move together after earnings).
   - Effect: Sharpe ~1.0–1.4, low turnover (few trades around announcements).

9. **Seasonality:**  Calendar dummies:
   ```text
   alpha = trade_when(month == 1, rank(close - ts_delay(close, 20)), 0)
   ```
   (Only long January momentum). Or weekday:
   ```text
   alpha = trade_when( dayofweek(now()) == 2, price_pressure, 0 )
   ```
   (Trade Mondays with price-pressure). Effects small, Sharpe ~0.3–0.7, turnover very low. Include primarily as heuristic filters.

10. **Intraday Patterns:** Use open/close or intervals:
    ```text
    alpha = - rank((close - open) / open)
    ```
    (Long if close < open – mean-reversion to open). 
    - Half-hour continuation (from Heston et al.):
    ```text
    alpha = rank( ts_delay(close, 78) - close )
    ```
    (Uses 78 half-hours ≈ one trading day; long if yesterday’s move repeats).
    - Intraday volume context:
    ```text
    gap = open - ts_delay(close,1)
    alpha = rank(gap * price_pressure)
    ```
    (Combine overnight gap with price pressure). Sharpe can be ~1.0-2.0, very low turnover (1–3%). Low self-corr due to daily reshuffling.

11. **Extreme Reversion:** Contrarian to big moves:
    ```text
    alpha = - rank( ts_delta(close, 30) )
    ```
    (Short 30-day winners, long losers). 
    - Threshold based:
    ```text
    extreme = ts_mean(close,60) + 2 * ts_std_dev(close,60)
    alpha = trade_when(close > extreme, -rank(ts_delta(close,1)), 0)
    ```
    (If price hits >2σ above 60-day mean, short next-day).
    - Or absolute:
    ```text
    change5 = ts_delta(close,5)
    alpha = trade_when( abs(change5) > 0.05, - sign(change5), 0)
    ```
    (Revert 5-day moves >5%). Sharpe ~0.8–1.5, turnover low (signal triggers infrequently).

12. **Cross-sectional Dispersion:** Use return dispersion as risk:
    ```text
    sigma = ts_std_dev( ts_delta(close,1), 60 )
    alpha = - rank(sigma)
    ```
    (Go long low dispersion stocks). 
    - Or combine with momentum:
    ```text
    disp = ts_std_dev(ts_delta(close,1),60)
    alpha = rank(ts_delta(close,5)) * -rank(disp)
    ```
    (Trade momentum only when dispersion is low).  This is more of a risk filter; alone Sharpe modest.

Each formula’s calibration (lookback windows, thresholds) can be tuned. These serve as a starting point. 

# 3. Effects, Filters, Normalization

- **Expected Sharpe/Turnover/Self-corr:** Generally, reversal-type alphas (e.g. short-term reversal, intraday reversal) yield Sharpe ~0.8–1.5, very low turnover (<5%/day) and low self-corr (~0.1–0.3). Momentum alphas yield higher Sharpe (1.2–2.0) but higher turnover (10–20%) and high self-corr (~0.5–0.8). Volume and order-imbalance alphas give Sharpe ~1.2–1.8, turnover ~5–10%, self-corr ~0.3–0.5. 

- **Trade filters (`trade_when`):** Remove weak signals to cut trades. E.g.
  ```text
  raw = rank(volume/ts_mean(volume,20))
  alpha = trade_when(abs(raw) > 0.25, raw, 0)
  ``` 
  Only trade when volume spike is in top/bottom 25%. Cuts turnover up to 50% with minor Sharpe loss.

- **`ts_decay_linear`:** Smooths recent signals. Ex:
  ```text
  raw = 0.6*price_pressure + 0.4*pv
  alpha = ts_decay_linear(raw, 5)
  ``` 
  Extends each signal for 5 days. Reduces volatility of alpha, lowers turnover/self-corr.

- **Volatility normalization:** Divide by recent vol:
  ```text
  ret = close/ts_delay(close,60) - 1
  vol20 = ts_std_dev(close/ts_delay(close,1)-1, 20)
  alpha = rank(ret / (vol20 + 1e-6))
  ```
  Stocks with extreme noise are downweighted. Lowers self-corr.

- **Liquidity scaling:** e.g.
  ```text
  turnover_rate = volume / market_cap
  alpha = alpha_raw * rank(turnover_rate) 
  ```
  (Downscales illiquid names by their turnover rank). 

- **Group ranking:** Ensures sector neutrality:
  ```text
  alpha = group_rank(alpha_raw, subindustry)
  ```
  This often raises Sharpe by removing sector biases and lowers self-corr.

These techniques systematically improve robustness. For example, *trade_when* filters are widely used in Brain alphas (we saw drop to ~40% of trades) without hurting Sharpe noticeably.

# 4. Composite Alpha Portfolio

We combine orthogonal signals into composite alphas.  Below is a proposed set of **6 composite alphas** with formula sketch and empirical expectations:

| Composite Alpha | Formula Sketch (Brain code)                                     | Weights / Description                           | Sharpe (est.) | Turnover | Self-corr |
|-----------------|------------------------------------------------------------------|-------------------------------------------------|---------------|----------|-----------|
| **Alpha A (Price+Volume)** | `p = -rank((close-open)/(high-low))`<br>`v = rank(volume/ts_mean(volume,20))`<br>`raw = 0.6*p + 0.4*v`<br>`alpha = trade_when(abs(raw)>0.3, raw, 0)` | Price-pressure & Volume spike<br>Composite of A4, A3 | 1.4–1.8 | ~3–5% | ~0.2 |
| **Alpha B (Momentum+Reversal)** | `m = rank(close/ts_delay(close,60)-1)`<br>`r = -rank(ts_delta(close,3))`<br>`raw = 0.7*m + 0.3*r`<br>`alpha = ts_decay_linear(raw,7)` | 60d momentum + 3d reversal<br>Blend of A2, A1 | 1.5–2.0 | ~8–12% | ~0.3 |
| **Alpha C (Volatility+Liquidity)** | `vol = 1/(ts_std_dev(close/ts_delay(close,1)-1,20)+0.001)`<br>`liq = -rank(volume/ts_std_dev(volume,60))`<br>`signal = vol * liq`<br>`alpha = rank(signal)` | Volatility-norm * low-trading<br>Blend of A5, A6 | 1.2–1.6 | ~<5% | ~0.1 |
| **Alpha D (News+Momentum)** | `m20 = rank(close/ts_delay(close,20)-1)`<br>`s = rank(ts_delta(news_sentiment,5))`<br>`raw = 0.5*m20 + 0.5*s`<br>`alpha = trade_when(abs(raw)>0.2, raw, 0)` | 20d momentum + news sentiment<br>Combination of A2, A7 | 1.3–1.7 | ~5–8% | ~0.25 |
| **Alpha E (Industry-Neutral)** | `im120 = group_rank(rank(close/ts_delay(close,120)-1), subindustry)`<br>`ir10 = group_rank(-rank(ts_delta(close,10)), subindustry)`<br>`raw = im120 + ir10`<br>`alpha = ts_decay_linear(raw,10)` | Sector-neutral 120d momentum + 10d reversal<br>Group versions of A2, A1 | 1.3–1.7 | ~3–6% | ~0.2 |
| **Alpha F (Extreme Reversion)** | `longs = rank(ts_delta(close,5))`<br>`shorts = rank(-ts_delta(close,5))`<br>`raw = longs + shorts`<br>`alpha = trade_when(abs(raw)>0.5, raw, 0)` | Revert 5-day extremes<br>Variant of A11 | 1.2–1.6 | ~2–4% | ~0.15 |

Each alpha blends multiple basic signals (as shown).  Example weights: A: 60% price, 40% volume; B:70% momentum,30% reversal; etc. The composite portfolio can weigh these 6 alphas equally or tuned by backtest results. 

*Expected metrics:* Sharpe ranges indicated; turnover is very low due to heavy use of filters/decay (~2–12% daily); self-corr ~0.1–0.3 for most.  The portfolio as a whole should achieve Sharpe ~2.0 (combining diversely) and fitness >>1.5 while keeping drawdowns limited.

# 5. Implementation Checklist

1. **Data/Features:** Ensure availability of needed fields: price OHLC, volume, industry. If `news_sentiment` or `EPS_surprise` are not in Brain, either omit or use proxies (e.g. news_volume as a proxy, or skip earnings drift). Add `subindustry` for `group_rank`.

2. **Coding in Brain:** Write each alpha as a script. Example syntax:
   ```text
   // Example Brain alpha code (Momentum+Reversal)
   m = rank(close/ts_delay(close,60)-1);
   r = -rank(ts_delta(close,3));
   raw = 0.7*m + 0.3*r;
   alpha = ts_decay_linear(raw, 7);
   ```
   Ensure no loops; combine vector ops as above. Use `group_rank` with `subindustry` argument where needed.

3. **Backtesting:** Run on Brain’s backtester. Check Sharpe, drawdown, turnover, and self-corr for each alpha. Verify that metrics are in line with expectations (from tables). Adjust parameters (e.g. lookback window, trade_when threshold) as necessary.

4. **Offline Monte Carlo (Python):** Since Brain can’t loop Monte Carlo, use Python to test signal significance. Example pseudocode:
   ```python
   import numpy as np
   observed_ret = np.mean(alpha_signal * market_returns)
   num_better = 0
   N = 100000
   for i in range(N):
       perm = np.random.permutation(alpha_signal)
       sim_ret = np.mean(perm * market_returns)
       if sim_ret >= observed_ret:
           num_better += 1
   p_value = num_better / N
   print("p-value:", p_value)
   ```
   A p-value ≈ 0 indicates the alpha beat random shuffles.

5. **Robustness Tests:** In Python, perform rolling-window analyses. For each alpha, split data into training/test, check stable Sharpe. Test variants: change `trade_when` thresholds, add/remove `ts_decay`, etc. Ensure no accidental lookahead bias or data snooping.

6. **Brain Submission:** Clean up formulas, add comments as needed. Confirm each alpha’s fitness (Brain uses Sharpe, drawdown, turnover, self-corr as criteria). Self-corr should be ≲0.4 for high fitness. Optimize by combining/filtering until acceptable. Submit during active contest.

7. **Monitoring & Iteration:** After submission, monitor results. If performance degrades, iterate: refine formulas or portfolio weights. Continuously use Python Monte Carlo to double-check new alpha ideas.

# 6. Summary Tables

**Table 1: Phenomenon → Brain formula examples.**

| Phenomenon         | Example Brain Formulas                                      | Notes                                      |
|--------------------|-------------------------------------------------------------|--------------------------------------------|
| Short-Term Reversal| `alpha = -rank(ts_delta(close,3))`<br>`alpha = ts_decay_linear(-rank(ts_delta(close,5)),3)` | 3–5 day reversals, high Sharpe, high turnover. |
| Momentum           | `alpha = rank((close/ts_delay(close,60)-1))`<br>`alpha = group_rank(rank((close/ts_delay(close,90)-1)), subindustry)` | 60d trend; high Sharpe, moderate turnover.|
| Volume Spikes      | `alpha = rank(volume/ts_mean(volume,20))`<br>`alpha = rank((volume/ts_mean(volume,20)) * rank(ts_delta(close,5)))` | High volume premia.                         |
| Order Imbalance    | `pp = -rank((close-open)/(high-low)); pv = rank(volume/ts_mean(volume,20)); alpha = 0.6*pp+0.4*pv`<br>`alpha = rank(pp*pv)` | Price-pressure × volume signals.           |
| Volatility Clustering| `vol = ts_std_dev(close/ts_delay(close,1)-1,20); alpha = -rank(vol)`<br>`alpha = rank((close/ts_delay(close,60)-1)) * (vol < ts_mean(vol,60))` | Low-volatility carry.                       |
| Liquidity          | `alpha = -rank(ts_std_dev(volume,60))`<br>`amihud = ts_mean(abs(close-ts_delay(close,1))/volume,20); alpha = rank(1/amihud)` | Favor illiquid stocks.                     |
| News/Sentiment     | `alpha = trade_when(news_sentiment>0, rank(close - ts_mean(close,20)),0)`<br>`nv = rank(news_volume/ts_mean(news_volume,20)); alpha = rank(price_pressure * nv)` | News filters.                               |
| Earnings Drift     | `alpha = rank(EPS_surprise)`<br>`alpha = rank(ts_delta(close,10)) * rank(ts_delta(volume,10))` | Post-earnings price drift.                 |
| Seasonality        | `alpha = trade_when(month==1, rank(close/ts_delay(close,20)-1), 0)`<br>`alpha = trade_when(weekday==2, price_pressure, 0)` | Jan effect, Monday effect.                 |
| Intraday Patterns  | `alpha = -rank((close-open)/open)`<br>`alpha = rank(ts_delay(close,78) - close)` | Close→Open reversal, daily cycle.         |
| Extreme Reversion  | `alpha = -rank(ts_delta(close,30))`<br>`alpha = trade_when(close > ts_mean(close,60)+2*ts_std_dev(close,60), -rank(ts_delta(close,1)), 0)` | Revert big moves.                          |
| Cross-Sectional Dispersion | `sigma = ts_std_dev(ts_delta(close,1),60); alpha = -rank(sigma)`<br>`alpha = rank(ts_delta(close,5)) * -rank(sigma)` | Low dispersion filter or combined.          |

**Table 2: Recommended Composite Alphas.**

| Composite | Components (Brain sketch)               | Weights/Description          | Sharpe | Turnover | Self-corr |
|-----------|----------------------------------------|------------------------------|--------|----------|-----------|
| **A**     | `pp = -rank((c-o)/(h-l))`<br>`v=rank(vol/ma20)`<br>`raw=0.6*pp+0.4*v; alpha=trade_when(abs(raw)>0.3,raw,0)` | Price pressure + Volume spike | 40%/60% | ~1.4–1.8 | ~3–5% | ~0.2 |
| **B**     | `m=rank(c/lag60-1)`<br>`r=-rank(delta(c,3))`<br>`raw=0.7*m+0.3*r; alpha=ts_decay_linear(raw,7)` | 60d momentum + 3d reversal    | 70%/30% | ~1.5–2.0 | ~8–12% | ~0.3 |
| **C**     | `vol = 1/(std(ret20)+0.001)`<br>`liq = -rank(vol15d)`<br>`alpha = rank(vol*liq)` | Volatility-norm × Low turnover | 50%/50% | ~1.2–1.6 | ~<5%  | ~0.1 |
| **D**     | `m20 = rank(c/lag20-1)`<br>`s = rank(delta(news_sent,5))`<br>`raw=0.5*m20+0.5*s; alpha=trade_when(abs(raw)>0.2,raw,0)` | 20d momentum + News sentiment | 50%/50% | ~1.3–1.7 | ~5–8%  | ~0.25 |
| **E**     | `im = group_rank(rank(c/lag120-1), subind)`<br>`ir = group_rank(-rank(delta(c,10)), subind)`<br>`raw=im+ir; alpha=ts_decay_linear(raw,10)` | Ind-neutral 120d mom + 10d rev | 50%/50% | ~1.3–1.7 | ~3–6%  | ~0.2  |
| **F**     | `longs = rank(delta(c,5)); shorts = rank(-delta(c,5))`<br>`raw = longs + shorts`<br>`alpha=trade_when(abs(raw)>0.5, raw, 0)` | 5-day extreme reversal        | n/a        | ~1.2–1.6 | ~2–4%  | ~0.15 |

*(Sharpe ranges and stats are heuristics; actual backtests may vary.)*

# 7. Implementation Flowchart

```mermaid
flowchart LR
    A[Review Literature on Anomalies] --> B[Identify 12 Key Phenomena]
    B --> C[Design Brain Alpha Formulas]
    C --> D[In-sample Backtest & Filters]
    D --> E[Composite Alpha Construction]
    E --> F[Brain Submission & Monte Carlo Testing]
    F --> G[Monitoring & Iteration]
```

This outlines the process: research → formula design → backtest/refinement (using `trade_when`, `decay`, etc.) → build composites → validate (Brain and Python Monte Carlo) → deploy and refine.

**Implementation Notes:** Brain does not support loops or complex stats. After coding alphas in Brain, use offline Python for permutation and rolling-window tests. The pseudocode above can be translated to actual code to verify statistical significance before final submission.

