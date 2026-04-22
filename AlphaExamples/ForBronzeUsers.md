## 1. Valuation based on cash flow
### Hypothesis
A lower EV/CF usually suggests the company is becoming cheaper relative to its cash-generating ability; a higher multiple suggests it’s getting more expensive.
### Implementation
Use ts_zscore to standardize the change of the ratio and group_rank to control the turnover.
### Hint to Improve Alpha
There are various types of cash flow, and switching the type used in the metric may improve its performance.  
`group_rank(-ts_zscore(enterprise_value/cashflow, 63),industry)`
### Simulation Settings
| Region | Universe | Language | Decay | Delay | Truncation | Neutralization | Pasteurization | Lookback | Max Trade | Max Position |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **deer** | TOP3000 | Fast Expression | 0 | 1 | 0.08 | Industry | On | — | OFF | OFF |

## 2. Overpriced stocks
### Hypothesis
When analyst price target estimates (est_ptp) and free cashflow estimates (est_fcf) move highly in sync over the past month (high positive correlation), it may signal that the market has already fully priced in the cash flow expectations into price targets — leaving little room for further upside.
### Implementation
Using est_ptp to capture price estimate and est_fcf to capture free cash flow and calculate the dynamics between them with ts_corr.
### Hint to Improve Alpha
The window of 1 year might be too long to react on the price correction. Try shorter window.  
`-ts_corr(est_ptp,est_fcf,252)`
### Simulation Settings
| Region | Universe | Language | Decay | Delay | Truncation | Neutralization | Pasteurization | Lookback | Max Trade | Max Position |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **deer** | TOP3000 | Fast Expression | 0 | 1 | 0.08 | Market | On | — | OFF | OFF |

## 3. Volatility arbitrage
### Hypothesis
Higher volatility is often observed during bearish markets, while lower volatility is typically seen during bullish markets. A lower Parkinson's volatility coupled with a higher implied volatility may suggest that there could be a stronger bullish sentiment for the stock in the future.  
### Implementation
Long the stock if its implied volatility significantly exceeds its historical volatility and short the opposite.  
### Hint to Improve Alpha
Can you use ts_backfill to avoid missing data on certain days?
`implied_volatility_call_120/parkinson_volatility_120`
### Simulation Settings
| Region | Universe | Language | Decay | Delay | Truncation | Neutralization | Pasteurization | Lookback | Max Trade | Max Position |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **deer** | TOP200 | Fast Expression | 0 | 1 | 0.08 | Sector | On | — | OFF | OFF |

