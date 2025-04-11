# Cog_Rehab

This study investigates the relationship between specific cognitive functions (e.g., working memory, attention) and functional rehabilitation outcomes in individuals with schizophrenia enrolled in psychosocial rehabiltitation programs.  
In particular, we will investigate the _targeted_ association between 1) working memory, attention, verbal episodic memory, social cognition complaints, and general intelligence; and 2) well-being/quality of life, self-esteem, autonomy, self-stigma, adherence to treatment, insight, and recovery.  
We will use targeted maximum likelihood estimation to measure the effect of a shift in cognitive tests (e.g. one more point in digit span).  
The study design is cross-sectional and the results will have to be interpreted with caution, i.e. non-causally.

## Set of covariates
See associated DAG. 

## Missing data
Observations with missing outcome data will be removed from the analysis.
Observations with missing targeted dependent variable data (cogntive function) will also be removed. 
Missing covariate data will be addressed using multiple imputations. Therefore multiple analyses will be carried out. We anticipate 5% missing data and will use m=10-20 imputed datasets.

### Pooling Estimates
Extract and average ATEs across imputations:

`r`
ates <- sapply(tmle_results, function(x) x$estimates$ATE$psi)
ate_pooled <- mean(ates)
3. Variance Estimation
Combine within-imputation (influence function) and between-imputation variance:

r

### Within-imputation variance (average of TMLE SEs^2)
var_within <- mean(sapply(tmle_results, function(x) x$estimates$ATE$var.psi))

### Between-imputation variance
var_between <- var(ates)

### Total variance (Rubin's formula)
var_total <- var_within + var_between + (var_between / 20)
se_pooled <- sqrt(var_total)

### Confidence Intervals
Use Barnard-Rubin degrees of freedom for small samples:

r
lambda <- (var_between + var_between/20) / var_total
nu_old <- (20 - 1) / lambda^2
nu_com <- nrow(your_data) - length(coef(model)) - 1
nu_obs <- (nu_com + 1)/(nu_com + 3) * nu_com * (1 - lambda)
df <- (nu_old * nu_obs) / (nu_old + nu_obs)

conf_low <- ate_pooled - qt(0.975, df) * se_pooled
conf_high <- ate_pooled + qt(0.975, df) * se_pooled

## Multiple comparisons
Determine the specific relationships you want to investigate AND/OR Use VanDerWiele to test for multiple comparisons.
