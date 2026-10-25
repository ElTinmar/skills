---
name: statistical-pitfalls
description: Use only when the user explicitly asks for a statistical-pitfalls review or invokes /statistical-pitfalls; never load it on your own initiative. Checks design, dependence, circularity, causal, modelling and inference errors.
---

# Statistical pitfalls

Run this review before fitting anything. Write the answers down (in the reply or a notes file) BEFORE any result is computed, so choices cannot be steered by outcomes. If an answer is unknown, ask the user rather than assuming. Sections follow the order of an analysis: steps 1–7 before fitting, 8–9 while fitting, 10–11 after.

## 1. Question
- State the question as an estimate or comparison (what difference, in what, between what).
- Classify each question as planned or exploratory now, before seeing results.
- Name the primary outcome now; later outcome switching must be disclosed.
- What effect size would matter in practice? Set n or a stopping rule in advance: checking significance as data arrive and stopping at p < 0.05 inflates false positives unless a sequential design is used.

## 2. Design and dependence
- Experimental unit: the level at which treatment was independently applied. n is counted at this level; treating observations that share a unit as independent is pseudoreplication.
- Write the hierarchy explicitly (e.g. measurements < subject < cluster < group) and mark what is nested vs crossed.
- Which factors vary within units (repeated measures)?
- Autocorrelation: consecutive measurements in time, and neighbouring locations in space, are correlated even without a cluster label.
- Batches (day, site, instrument, operator): does each contain all groups? If not, flag confounding.
- Drift in conditions or equipment over time: is it aligned with the order groups were run?
- Position effects (edges, gradients, instrument location): randomize positions.
- Order, carryover and time-on-task (habituation, fatigue, learning) in within-unit designs: counterbalance or model order.
- Blinding: were measurement, scoring, exclusions and parameter tuning done blind to group?
- Sampling: does the sample represent the population of interest? Conclusions extend only to the populations, conditions and predictor ranges sampled (no extrapolation).

## 3. Causal structure and covariates
- Each covariate must be justified before seeing results.
- Randomized assignment: do not significance-test baseline differences; adjust for important prognostic covariates instead.
- Never adjust for or select on a collider (caused by both treatment and outcome); selection on one is Berkson's paradox.
- Never adjust for post-treatment variables (mediators) when estimating the total effect.
- Observational data: association is not causation; consider confounders and reverse causation.
- Time-to-event: classifying subjects by something that happens during follow-up grants guaranteed event-free time (immortal time bias); use time-varying covariates or landmark analysis.
- Two variables that both trend over time correlate spuriously; detrend or model time.
- Pre/post designs: change scores and ANCOVA answer different questions (Lord's paradox); state which question is asked.
- Only the target coefficient has a causal reading; do not interpret the others (Table 2 fallacy).
- Noisy covariates adjust incompletely and noisy predictors bias slopes toward zero (regression dilution).

## 4. Measurement and derived variables
- Validity and reliability: does the measure capture the intended construct, and how repeatable is it? Unreliable outcomes dilute effects and make null results uninformative.
- Floor and ceiling effects compress or create differences and interactions.
- Do not dichotomize continuous variables (median splits).
- Ratios: avoid noisy baselines in the denominator and correlations between variables sharing a term (A/B vs B); prefer the raw variable with a covariate, and model counts over unequal exposure (duration, area, trials) with an exposure offset.
- Mean of ratios differs from ratio of means; averaging after a nonlinear transform (log, rate, ratio) differs from transforming the average. Choose deliberately.
- Interactions depend on scale (raw vs log); pick the scale for a substantive reason.
- Do not normalize to the group mean before comparing groups.
- Smoothing or filtering before testing creates autocorrelation and can shift effects; test on unsmoothed data or account for it.

## 5. Data quality and missingness
- Check raw data against metadata for label swaps, coding and pipeline errors.
- Why are data missing? Complete-case analysis is unbiased only if data are missing completely at random; otherwise use methods valid under the plausible mechanism (mixed models, imputation, sensitivity analysis). Dropout related to treatment (survivorship bias) is the common case; report missingness by group.
- Censored values (timeouts, detection limits, no event) are not ordinary values; use censored or survival models.

## 6. Selection and circularity
For every step that chooses what to analyse (units, features, time windows, regions, clusters, exclusions, outliers, model terms):
- Was it informed by the same data the test uses? If yes, it is double dipping.
- Fix: independent split, a criterion orthogonal to the test, selection inside CV folds, or a permutation test that repeats the selection.
- Never test differences between clusters on the variables used to create them.
- Do every data-dependent preprocessing step inside each training fold (no leakage).
- Stepwise or data-driven model selection invalidates ordinary p-values.
- Exclusion rules must be predefined; otherwise report with and without.

## 7. Look at the data
- Plot raw points by group and batch before any statistic.
- Check that pooled trends hold within subgroups and batches (Simpson's paradox).
- Group-level patterns do not license individual-level claims (ecological fallacy).
- Units selected for extreme baseline values need a control selected the same way (regression to the mean).
- Correlations: check for single points driving them, nonlinearity, and restricted range (which attenuates them).

## 8. Model choice
- Match the family to the outcome: continuous, count, proportion, binary, ordinal (cumulative link), censored or time-to-event; consider zero-inflation and heavy tails.
- Random effects mirror the hierarchy from step 2; model autocorrelation where present.
- Two-stage analyses (summarize per unit, then test) are valid but weight units equally whatever their number of observations; mixed models weight by precision.
- A grouping factor with very few levels (fewer than ~5) gives poor variance estimates; consider a fixed effect.
- Repeated measures: do not assume sphericity; use paired contrasts or a structured covariance.
- Unequal variances: Welch tests or a per-group variance model.
- Check parameter count against number of units; treat singular fits as a sign to simplify.
- Correlated predictors (multicollinearity) give unstable coefficients and inflated standard errors.
- Factor coding: with an interaction present, a "main effect" under treatment coding is the effect at the reference level; use sum coding or report simple effects.
- Unbalanced designs: Type I (sequential) and Type III sums of squares test different hypotheses; know which the software uses.
- Center continuous covariates when they enter interactions.
- Logistic models: a predictor that perfectly separates outcomes inflates estimates and standard errors; use penalized (Firth) or Bayesian fits.
- Do not choose tests via normality or equal-variance tests; check assumptions on residuals (step 10).

## 9. Inference
- List every planned test; define the correction family and method (Holm, Tukey, FDR).
- Post-hoc comparisons use the correct error term (between-unit effects on unit-level error).
- Test differences directly: "significant in A but not B" is not a test of A vs B (test the interaction), and overlapping or non-overlapping error bars do not establish significance.
- Mixed models: compare fixed effects with ML fits, not REML; tests of a variance component at zero need boundary-corrected or bootstrap tests.
- Permutation and bootstrap resample at the experimental unit, respecting the hierarchy.
- Many tests over time points, locations or features: use cluster-based permutation or another correction for mass-univariate testing.
- Classification: use balanced accuracy with imbalanced classes; test against chance with permutation, not a binomial test on cross-validated accuracy.
- Frequentist quantities are not probabilities about the parameter: p is P(data this extreme | H0), not P(H0 | data), and with low prior plausibility many significant results are false; a 95% confidence interval comes from a procedure that covers the true value 95% of the time, not a 95% probability that this interval contains it. That statement belongs to a Bayesian credible interval and depends on the prior.
- Rank tests (Mann–Whitney, Wilcoxon) test whether one distribution tends to give larger values, not whether medians differ, unless a pure shift is assumed.
- Non-significance is not absence: report CIs, use equivalence tests if absence is the claim.
- Low power both misses effects and inflates significant ones.
- Statistical significance is not practical importance; with large n trivial effects are significant.
- R² measures fit, not correctness or importance.
- Report effect sizes with confidence intervals, preferably in raw units; standardized effect sizes (e.g. Cohen's d) depend on which SD is used and are not comparable between within- and between-subject designs.

## 10. Validation and diagnostics (after fitting)
- Residuals, influential units, convergence.
- Robustness: does the conclusion survive reasonable alternatives (exclusions, family, covariance, scale, priors in Bayesian analyses)? Results must not depend on the random seed.
- Simulate data under the null with the real structure and run the full pipeline; the false-positive rate should match the nominal level.

## 11. Reporting
- n stated at every level, per group.
- Figures show the level the test used.
- Error bars defined (SD, SEM, CI); never SEM chosen to make variability look small.
- Every analysis tried is reported, not only the one that worked (forking paths), and exploratory findings are labelled, not presented as hypothesized (HARKing).
- Null results reported, not filed away.
- State how example traces, images or subjects were chosen; never cherry-pick the cleanest.
- Record software versions, seeds and non-default settings, and share the code.

## Output
Before results: a short block listing unit and hierarchy, covariates and their justification, derived variables, missing-data handling, selection steps and how they stay independent, model and family, planned tests and correction. After results: flag any item above that failed, and say plainly when a requested analysis is invalid and what to do instead.
