## 1. The restaurant charges prices based on news.
### Hypothesis
If a stock's price takes a long time to rise after news is released, it may have significant momentum to continue its strong upward trend. A long position would be advantageous in this case.  
### Deployment
Use the news data field 'nws12_prez_4l' to implement the above idea. Fill the data gap with the ts_backfill operator and two years to ensure consistency of data coverage.  
### Suggestions for improvement
Increase the weighting of stocks with higher liquidity (high trading volume) to pass the Sub-universe test.  
`ts_backfill(vec_avg(nws12_prez_4l),504)`
### Simulation Settings
| Region | Universe | Language | Decay | Delay | Truncation | Neutralization | Pasteurization | Lookback | Max Trade | Max Position |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **deer** | TOP500 | Fast Expression | 0 | 1 | 0.08 | Industry | On | — | OFF | OFF |

## 2. Income before tax
### Hypothesis  
Earnings before tax are a good indicator of a company's financial health and profitability. Companies with increasing pre-tax earnings may have better growth prospects and higher potential for stock price appreciation. Therefore, we would buy stocks with increasing pre-tax earnings and short-sell stocks with decreasing pre-tax earnings.  
### Deployment
Use the time series ranking operator to compare the trend of pre-tax earnings (fundamental data) over the past two years, and use the 'quantile' operator to normalize the results.  
### Suggestions for implementation
Reinforce this signal with revenue data. For example, a company with higher revenue is likely to outperform.  
`quantile(ts_rank(pretax_income,250))`
### Simulation Settings
| Region | Universe | Language | Decay | Delay | Truncation | Neutralization | Pasteurization | Lookback | Max Trade | Max Position |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **deer** | TOP3000 | Fast Expression | 4 | 1 | 0.01 | Market | On | — | OFF | OFF |

## 3. Operating profit margin
### Hypothesis
If a company's current operating income is higher than it was a year ago, buy its stock and short sell it if the income is lower.  
### Deployment
Use ts_rank to measure a company's current operating income relative to its past performance, using the Fundamental "operating_income" data field.  
### Suggestions for implementation
Instead of directly comparing the data set, would using a ratio that encompasses the volatility of stock prices help improve the signal?
`ts_rank(operating_income,252)`
### Simulation Settings
| Region | Universe | Language | Decay | Delay | Truncation | Neutralization | Pasteurization | Lookback | Max Trade | Max Position |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **deer** | TOP3000 | Fast Expression | 0 | 1 | 0.08 | Subindustry | On | — | OFF | OFF |

## 4. Debt is escalating.
### Hypothesis
Rising debt prices suggest that interest expenses may be higher than expected. This could lead to lower profitability along with other financial difficulties.
### Deployment
Short sell stocks when the market value of a company's debt increases compared to the past year, and buy stocks in the opposite case.
### Suggestions for implementation
Try observing the data over a timeframe closer to one year to improve the accuracy of the signal.  
`-ts_rank(fn_liab_fair_val_l1_a,252)`
### Simulation Settings
| Region | Universe | Language | Decay | Delay | Truncation | Neutralization | Pasteurization | Lookback | Max Trade | Max Position |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **deer** | TOP3000 | Fast Expression | 0 | 1 | 0.08 | Subindustry | On | — | OFF | OFF |

## 5. Deferred revenue
### Hypothesis:
Companies with high levels of deferred revenue will surprise the market in the future when these deferred revenues are recognized.
### Implementation:
Use the fnd6_drc field to represent deferred revenue. To improve the coverage of this field, the ts_backfill operator is applied. Deferred revenue is divided by total assets to balance the size of businesses in comparison.  
### Suggested implementation
Instead of relying on the raw value of the calculated ratio to determine the weight of a stock, use cross-sectional operators. Use group operators to compare similar stocks.  
`ts_backfill(fnd6_drc, 252)/assets`
### Simulation Settings
| Region | Universe | Language | Decay | Delay | Truncation | Neutralization | Pasteurization | Lookback | Max Trade | Max Position |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **deer** | TOP3000 | Fast Expression | 0 | 1 | 1 | Sector | On | — | OFF | OFF |
