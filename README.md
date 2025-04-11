# Cog_Rehab

This study investigates the relationship between specific cognitive functions (e.g., working memory, attention) and functional rehabilitation outcomes in individuals with schizophrenia enrolled in psychosocial rehabiltitation programs.  
In particular, we will investigate the _targeted_ association between 1) working memory, attention, verbal episodic memory, social cognition complaints, and general intelligence; and 2) well-being/quality of life, self-esteem, autonomy, self-stigma, adherence to treatment, insight, and recovery.  
We will use targeted maximum likelihood estimation to measure the effect of a shift in cognitive tests (e.g. one more point in digit span).  
The study design is cross-sectional and the results will have to be interpreted with caution, i.e. non-causally.

## Set of covariates
See associated DAG. 

## Missing data

### Missing exposure and outcome
Observations with missing outcome data will be removed from the analysis.
Observations with missing targeted dependent variable data (cogntive function) will also be removed.  
We acknowledge that this may be seen as over-simplification.

### Missing covariates
Missing covariate data will be imputed using multiple imputations (MI). We anticipate 5% missing data and will use m=10-20 imputed datasets.

### Imputation model 
For valid inference with MI, each univariate imputation model should be compatible with the analysis method, i.e. all variables included in the analysis should be included as predictors in the imputation model. Interaction terms and non-linearities should also be included.  
Specifying interaction terms in both the outcome and exposure models can be error-prone, particularly when prior knowledge of effect modifiers is limited. To mitigate this risk, leveraging SuperLearner in both the target (causal) model and the imputation model offers a data-adaptive solution. This ensemble learning approach systematically captures potential interactions and nonlinearities without requiring explicit prespecification, enhancing robustness to model misspecification in both stages of analysis.  To impute using SuperLearner, we will be using the `misl R` package

### Pooling out point estimates and their variances  
Analysis is performed within each completed dataset and the results are pooled using Rubin’s rules to obtain the final estimate and its standard error (SE).  
Estimates will be averaged over imputed datasets. Variance estimation will combine within-imputation (average of TMLE SEs^2) and between-imputation (variance of mean estimations) variance. Total variance will be estimated according to Rubin's formula
`var_total <- var_within + var_between + (var_between / 20)  
se_pooled <- sqrt(var_total)`

#### Confidence Intervals
Use Barnard-Rubin degrees of freedom for small samples:

`
lambda <- (var_between + var_between/20) / var_total  
nu_old <- (20 - 1) / lambda^2  
nu_com <- nrow(your_data) - length(coef(model)) - 1  
nu_obs <- (nu_com + 1)/(nu_com + 3) * nu_com * (1 - lambda)  
df <- (nu_old * nu_obs) / (nu_old + nu_obs)  
conf_low <- ate_pooled - qt(0.975, df) * se_pooled  
conf_high <- ate_pooled + qt(0.975, df) * se_pooled
`

## Multiple comparisons
Determine the specific relationships you want to investigate AND/OR Use VanDerWiele to test for multiple comparisons.
