**1. positive_days** = days that returns > mean returns on a 20-day period => short_term = average returns in 5 days.

**2. A popular concept:** Buying pass losers and selling pass winners

3. De Bondt and Thaler (1985) show that over 3-5 years holding periods stocks that performed poorly achieve higher returns than stocks that performed well over the same period.
*long-term losers outperform the long-term winners only in Januaries

4. Contrarian strategies that select stocks based on their returns in the previous week or month generate significant abnormal returns.

5. Buy stocks with current prices that are substantially higher than their average prices over the past 27 weeks realizes significant abnormal returns.

6. A majority of the mutual funds examined by Grinblatt and Titman (1989, 1991) show a tendency to buy stocks that have increased in price over the previous quarter.

7. Anecdotal evidence suggests: practitioners who use relative strength rules base their selections on price
movements over the past 3 to 12 months.

8. Trading strategies based on either very short-term return reversals (1w or 1m), or very long-term return reversals (3-5y).
However, anecdotal evidence suggests that practitioners who use relative strength rules base their selections on price movements over the past 3-12 months.

9. The portfolio formed on the basis of returns realized in the past 6m => an average cumulative return of 9.5% over the next 12 months but loses more than half of this return in the following 24 months.

10. Use momentum strategy: J/K - select stocks on the basis of returns over the past J months and hold them for K months.
This strategy is constructed as: At the beginning of each mon, `t`, the securities are ranked in asscending order based on their returns in the past J months.  
=> *10 decile portfolios* formed, equally weight the stocks contained in the 1, 2,... decile.  
==> *Top decile* is called "losers" decile and *bottom decile* is called "winners" decile.  
===> *Each month*, the strategy buys winner and sells loser, holding this position for K months.
Best result: 12J/3K

12. The paper implies that skip 1 week between formation and holding increases returns.  
=> Idea: Short-term price movement is usually affected by microstructure noise or short-term mean reversion.  
==> Signal ideas: Calculate long-term momentum -> remove short-term return (1 week/ few days)

13. Some stocks structurally have higher expected return.  
=> Idea: Stocks having consistent performance in multiple periods can continue to outperform.

14. Momentum can occur when factor returns have serial correlation.  
=> For example: market factor, sector factor...  
==> If there's a trend in a factor, stocks that have high exposure to that factor may outperform.

15. Paper shows a strategy called Weighted Relative Strength Strategy (WRSS).  
=> Idea: Stocks outperforming compares to market average will continue to outperform.  
==> Signal idea: return of stock - return of cross-section average.

16. Momentum profit rises when factor beta has a large variance.  
=> Idea: stock which has large beta dispersion -> stronger momentum signal.  
==> Signal idea: measure the dispersion of factor exposure -> combine with momentum

17. Momentum from idiosyncratic shocks => true inefficiency
Stock-specific shocks can persist over time => exploitable residual momentum.
- Idiosyncratic return: $$e_{i,t} = R_{i,t} - (\alpha_i+\beta_iR_{m,t})$$
 where:
  - $R_{i,t}$ = return of stock *i* at time *t*  
  - $R_{m,t}$ = market return at time *t*  
  - $\alpha_i, \beta_i$ = stock-specific CAPM parameters
- Residual momentum(RM) signal (over n periods): $$RM_{i,t} = Σ_{k=0}^{n-1} e_{i,t-k}$$
- Alternative using autocovariance (MomentumSignal - MS): $$MS_{i,t} ∼ Cov(e_{i,t},e_{i,t-1})$$  
=> Trading Signal
- **Long**: stocks with high $RM_{i,t}$  
- **Short**: stocks with low $RM_{i,t}$

19. Momentum strategies are always tested by: zero-cost portfolio:
- Long winners
- Short losers  
=> Idea: Alpha should be based on cross-section ranking, không phải direction của market.

19. This paper checks whether momentum by high beta stocks has high expected return or not.  
=> Results: losers have higher beta than winners.

20. Momentum is stronger in small caps: Both winners and losers are smaller than average stocks. Momentum tends to be stronger in small firms.  
Small caps usually experience slower information diffusion and have fewer analyst coverage.  
=> Idea: Signal could be stronger for small, less-covered stocks

21. Momentum comes from idiosyncratic returns. Market return serial covariance is negative, but stock residual covariance is positive.  
This suggests momentum arises from firm-specific information underreaction.  
=> Idea: Running momentum on residual returns may capture true inefficiencies

22. "Underreaction to firm-specific news": Momentum occurs because the market underreacts to firm-specific news, such as earnings surprises, analyst revisions, and news diffusion.  
=> Idea: Stocks with recent news but incomplete market reaction may exhibit stronger momentum

23. Lead-lag between stocks:  
Some stocks react immediately to common factors, while others respond slowly, creating lead-lag patterns.  
=> Idea: Delayed-reacting stocks can generate predictable short-term momentum

24. Momentum profits are not from factor autocorrelation.
If momentum came purely from factors, market returns would show positive serial correlation.  
Evidence shows that `cov(r_t, r_{t-1}) < 0`, indicating factor momentum is not the main source.  
=> Idea: Focus on stock-specific movements rather than factor trends

26. Momentum exists across size and beta groups  
Momentum is observed in small, medium, and large caps as well as low, medium, and high beta groups.  
This indicates momentum is a cross-sectional effect, largely independent of size or beta.

27. Momentum mainly from the long side  
Regression shows winners portfolios achieve positive abnormal returns, while losers portfolios are not strongly negative.  
Most momentum profits appear to come from buying winners rather than shorting losers.  
=> Idea: Long positions in winners are more profitable; short side is less important

28. Seasonal pattern of momentum  
Momentum underperforms strongly in January, while returns from February to December average around 1.66% per month.  
=> Idea: Consider adjusting strategy weight seasonally (e.g., reduce January exposure)

29. Event-time return shape  
Studies show positive returns in the first 12 months after formation, negative in year 2, and near zero in year 3, suggesting momentum is not permanent.  
=> Idea: Momentum signal strongest in the first 12 months, then reversal risk increases

30. Earnings announcement drift  
Stocks that experience positive earnings surprises tend to have positive returns, while those with negative surprises have negative returns.  
This demonstrates that the market often underreacts to earnings information.  
=> Idea: Exploit post-earnings momentum when market underreacts
