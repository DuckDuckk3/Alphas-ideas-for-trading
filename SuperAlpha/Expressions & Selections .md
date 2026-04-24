Please refer to the below selection expression:

 

(

    (self_correlation < 0.6) &&

    (turnover < 0.18) &&

    (in(datacategories, "model"))

) * NOT(OWN)

Explanation:

NOT(OWN):
This ensures that the alpha is selected from the pool excluding your own submissions, as per the selection expression.
Selection Criteria:
The expression will filter alphas from the "Model" data category where:
Turnover is less than 18%.
Self-correlation is less than 60%.
This ensures a curated selection of alphas with lower turnover and minimal self-correlation from the "Model" category, while excluding your own pool for diverse signals.
