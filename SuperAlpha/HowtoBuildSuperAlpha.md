# Creating and Simulating a Super Alpha in WorldQuant BRAIN 
## Step 1: 
Select Alphas Choose 20 high-performing alphas from the same region (e.g., USA) to ensure consistency inuniverse coverage and performance behavior.  
## Step 2: 
Assign a Custom Code Name After selecting the 20 alphas, assign a unique code name to your group.   
Example: 321. This codewill be used to reference your selection in simulation.  
## Step 3:  
Navigate to the Simulation Section Go to the Simulate tab from the top menu to begin building your Super Alpha.  
## Step 4:  
Configure Simulation Settings In the simulation settings panel:  
- Set the Region to match the one used in your alpha selections (e.g., USA).
- Adjust additional settings as required (e.g., rebalance frequency, neutralization options).
## Step 5: 
Define Selection Expression In the Selection Expression box, input the following code:  
name == '321'  
This ensures that the simulator only combines alphas tagged with your chosen code.
## Step 6: Define Combo Expression In the Combo Expression section, use the following:  

This applies a uniform weight to all selected alphas in the group.
## Step 7: 
Start Simulation Click Start to run the simulation.  
Review performance metrics like Sharpe Ratio, Return, Turnover, and Drawdown to evaluate effectiveness.  
To select the best alphas for a SuperAlpha with low correlation and high fitness, focus on diversity and complementarity. Analyze the correlation matrix of candidate alphas and select those with low pairwise correlations (<0.2) to minimize redundancy. Prioritize alphas with consistently high IC, Sharpe ratio, and return spread across backtests. Ensure regional or factor-neutralization (e.g., subindustry, size) to avoid hidden biases. Use techniques like hierarchical clustering to group similar alphas and choose representatives from each cluster. Regularly evaluate production performance to ensure robustness. Balancing high fitness with low correlation is key to building an effective SuperAlpha.

I’d say the key with SuperAlpha is to be picky about which alphas you keep (look at stability and correlation, not just mean IC), and then combine them more smartly than equal weighting — rank averaging, IC-weighting with shrinkage, or PCA can all help. Neutralization doesn’t have to be all-or-nothing, partial works fine, and decay length should follow your IC profile. Finally, don’t forget turnover: costs matter, and keeping it in a moderate range usually makes out-of-sample results look a lot better.
