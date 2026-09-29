# Predicting Public Confidence in Institutions with Machine Learning (R)

Classification project in R. It predicts whether a survey respondent has **high or low confidence** in
the **government**, **trade unions** and the **women's movement** from 30 attitude and demographic
variables in the World Values Survey (20,000 respondents, 58 countries). I compared six models, tuned
the weakest ensemble, and tested a neural network's stability across survey waves.

![ROC curves – confidence in government](figures/roc_cgovernment.png)

## Key results

**1. Ensembles beat single models, but the signal is weak.** Test-set AUC for five classifiers:

| Model | Government | Unions | Women's Mvt |
|---|---|---|---|
| Decision Tree | 0.575 | 0.500 | 0.500 |
| Naive Bayes | 0.611 | 0.585 | 0.574 |
| Bagging | 0.607 | 0.595 | 0.569 |
| **Boosting** | **0.627** | 0.608 | **0.603** |
| **Random Forest** | 0.626 | **0.609** | 0.594 |

Boosting (mean AUC 0.613) and Random Forest (0.610) were the most reliable. The pruned decision tree
collapsed to a single node for two targets because no split generalised.

**2. Accuracy alone was misleading.** On the women's movement target the collapsed tree scored the
*highest* F1 (0.74) by predicting "High" for everyone. I used AUC, recall and confusion matrices to choose
models instead of a single metric.

**3. Political attitudes drive confidence.** Across all ensembles, importance of politics, support for
army rule, democratic attitudes, age and life satisfaction were the top predictors. Child-rearing values
and club memberships contributed almost nothing and could be dropped.

**4. Improving the weakest ensemble.** I cut Bagging from 30 to 16 features (the union of the top Random
Forest features) and raised it from 25 to 100 trees. F1 improved on all three targets (government
0.433 → 0.488). AUC stayed about the same, which showed the limit was the data, not the model.

**5. A country-specific neural network did much better.** An ANN trained only on South Africa (the
largest country sample) with 11 selected, standardised features reached **F1 = 0.83 and AUC = 0.80**,
against ~0.60 AUC for the global models. Scored separately by survey wave, performance *rose* from
Wave 5 (AUC 0.73) to Wave 6 (AUC 0.81), so the model held up over time.

## Approach

1. **Exploration**: class balance (imbalance ratios 1.15–1.45), missingness from WVS negative codes
   (up to 26%), distributions and country/wave coverage.
2. **Pre-processing**: negative codes → `NA`, rows with missing labels dropped, median imputation for
   predictors (robust to skew), identifiers converted to factors → 14,112 clean rows.
3. **Modelling**: 70/30 train/test split; Decision Tree (`rpart`, CV-pruned), Naive Bayes (`e1071`),
   Bagging and AdaBoost (`adabag`), Random Forest (`randomForest`). Country, wave and the other targets
   were excluded as predictors to prevent leakage.
4. **Evaluation**: confusion matrices, accuracy, precision, recall, F1, ROC curves and AUC (`ROCR`).
5. **Improvement and extension**: feature selection plus tuning for Bagging; single-hidden-layer ANN
   (`nnet`, 5 units, weight decay 0.01) with scaling fitted on training data only.

## Tech stack

R · rpart · e1071 · adabag · randomForest · nnet · ROCR · ggplot2

## Limitations

- Median imputation ignores relationships between variables. Multiple imputation might do better.
- The ANN test sets per wave are small (n ≈ 45), so wave-to-wave differences are indicative only.
- No class re-weighting or threshold tuning was applied. That is the next thing I'd try for the
  low-recall unions target.

## Data

World Values Survey extract (binary-coded confidence variables), from
[worldvaluessurvey.org](https://www.worldvaluessurvey.org/). Raw data is not included in this repo.

## Context

Individual project for a university data analytics unit (Monash University, 2026).
