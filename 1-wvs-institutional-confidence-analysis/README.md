# What Drives Trust in Institutions? — South Korea vs the World

Statistical analysis in R of **100,000 World Values Survey responses from 58 countries**, asking how
South Korea's confidence in institutions (parliament, courts, press, armed forces, civil service, unions)
differs from the rest of the world, what predicts it, and how it has changed over time.

(<img width="875" height="540" alt="2a_dumbbell" src="https://github.com/user-attachments/assets/2a10c826-4d31-4cfa-80d6-4a285d250ff1" />
)

## Key findings

- **South Korea is statistically distinct.** Welch's t-tests show significant differences (p < 0.05) on most
  attitudes. Koreans place more weight on respect for authority (+0.81) and on religion (+0.51), and report
  lower life satisfaction (−0.40) and less political participation (petitions −0.28, boycotts −0.26).
- **Trust is hard to predict.** Stepwise regression models explain **under 7% of the variance** in
  institutional confidence (best: parliament, adj. R² ≈ 0.068). Demographics and values alone don't explain
  trust.
- **Cultural values are the strongest drivers.** Respect for authority predicts higher confidence in courts,
  parliament and the armed forces, and belief in hard work predicts confidence in the press and in
  environmental organisations.
- **Korea is changing faster than the rest of the world.** Wave × Group interaction models show Korean
  confidence in the armed forces, civil service, courts and parliament rising faster over time than
  elsewhere, while respect for authority is declining faster.

## Approach

| Step | What I did | Why |
|---|---|---|
| Cleaning | Recoded WVS non-response codes (−1 to −5) and invalid zeros to `NA` using per-variable rules (e.g. keeping `0` where it means "not a member") | Survey codes look numeric but aren't real answers |
| Profiling | Missingness, distribution and outlier checks across 36 variables | Up to 26% missing in some variables, which shapes later model choices |
| Group comparison | Welch's t-test + Cohen's d; correlation screen for multicollinearity | Unequal group sizes (2,339 vs 97,661) and variances |
| Prediction | Stepwise / forward regression per institution; standardised coefficients compared across countries in a heatmap | Find the few predictors that matter out of ~30 |
| Change over time | ANOVA / Kruskal-Wallis across survey waves; Wave × Group interaction regression | Test whether Korea's trends differ from the global trend |

## Selected figures

| | |
|---|---|
| ![](figures/2c_heatmap_predictors.png) | ![](figures/3a_direction_magnitude.png) |
| Standardised predictor effects by institution | Where Korea's trends diverge over time |

## Tech stack

R · dplyr · tidyr · ggplot2 · corrplot · MASS (`stepAIC`)

## Limitations

- Ordinal Likert responses were treated as numeric.
- Stepwise selection can overfit and give unstable variable choices on large datasets. The low R² values
  suggest trust depends on factors outside this survey.

## Data

World Values Survey (Waves 1–7), available after registration at
[worldvaluessurvey.org](https://www.worldvaluessurvey.org/). Raw data is not included in this repo.

## Context

Individual project for a university data analytics unit (Monash University, 2026).
