# Execution & Market Microstructure
- `Optimal Execution of Portfolio Transactions` — Robert Almgren & Neil Chriss  
The foundation of optimal execution. It gives you a framework to split large orders while balancing market impact, slippage, and risk. If you ever place real orders, this is essential.  
- `High-Frequency Trading in a Limit Order Book` — Marco Avellaneda & Sasha Stoikov  
A classic model for market making. It shows how to optimally quote bid/ask prices while managing inventory risk and stochastic order flow.
- `Algorithmic Trading and the Market for Liquidity` — Terrence Hendershott & Ryan Riordan  
Helps you understand how algorithmic traders affect market liquidity—useful when designing strategies that don’t destroy their own edge.

# Alpha Generation (Signals)
- `Returns to Buying Winners and Selling Losers` — Narasimhan Jegadeesh & Sheridan Titman  
The original cross-sectional momentum paper. Buy winners, short losers—simple, but still one of the most robust anomalies.  
- `Time Series Momentum` — Tobias Moskowitz, Yao Hua Ooi & Lasse Heje Pedersen  
A highly practical trend-following framework across asset classes (equities, FX, commodities). Works well in real portfolios.

# Risk, Factor Models & Reality Checks
- `On Persistence in Mutual Fund Performance` — Mark Carhart  
Introduces the Carhart 4-factor model (adds momentum). Crucial for avoiding false alpha in backtests.

# Advanced / Quant Research Direction
- `Algorithmic Trading Stochastic Control and Mutually Exciting Processes` — Álvaro Cartea, Sebastian Jaimungal & José Penalva
More advanced: combines signal + execution using stochastic control and order flow modeling (Hawkes-type processes).
- `Portfolio Liquidation Games with Self-Exciting Order Flow` — Gang Fu, Ulrich Horst & Jin Ma Xia  
Focuses on order flow clustering and strategic interaction, useful for execution in competitive environments.  

## If You Only Read 3 First
### Start with:
- Almgren–Chriss → execution
- Avellaneda–Stoikov → market making
- Moskowitz–Ooi–Pedersen → alpha (trend)
### That combination covers the core loop:
`signal → execution → inventory/risk management`
