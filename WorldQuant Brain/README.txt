Note that most alphas here are executed at Region: USA, Universe: TOP3000, Delay: 1, Neutralize: SubIndustry, Decay: 6-10, Truncation: 0.05-0.08, Pasteurization: On
You can try another settings, because some alphas here may work effectively at Neutralize: Market or smaller Universe like TOP500, etc.
To reduce Weight-concentration, try reduce the Truncation, 
to smoothen the signal, try increase Decay (this usually results in a slight increase in Sharpe and Fitness),
Sector neutralize may reduce the bias under certain circumstance, but most of the time, SubIndustry is still the best.
