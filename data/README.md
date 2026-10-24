Name - Marta Andrade e Sousa
Number - 20260664

Run: 20260916_124128
Model: decision_tree  params={'max_depth': 5}
Test size: 0.2  random_state: 42
============================================================

Train accuracy: 0.680
Test accuracy:  0.668
Gap (train - test): +0.012

Run: 20260916_120921
Model: logistic_regression  params={'max_iter': 1000}
Test size: 0.2  random_state: 42
============================================================

Train accuracy: 0.679
Test accuracy:  0.677
Gap (train - test): +0.002


Conclusions:
The logistic regression achieved a slightly higher test accuracy (0.677 vs. 0.668)
However, the logistic regression model demonstrates better generalization and stability, as indicated by its smaller gap between training and test accuracy (+0.002 compared to +0.012 for the decision tree)


# Week 3 - Cleaning pipeline
Model: decision_tree  params={'max_depth': 5}

Train accuracy: 0.683
Test accuracy:  0.680
Gap (train - test): +0.003
----------------------------------------------------------

Model: logistic_regression  params={'max_iter': 1000}

Train accuracy: 0.669
Test accuracy:  0.669
Gap (train - test): -0.000
----------------------------------------------------------

Conclusions:
After applying the cleaning step:
    The decision tree improved its test accuracy (from 0.668 to 0.68) and its gap dropped (from +0.012 to +0.003);
    The logistic regression decreased slightly (from 0.677 to 0.669), with a gap of approximately zero.
As a result, the decision tree now performs slightly better on the test set.

Overall, the main benefit of data cleaning is a more reliable score (the models are now trained on data where each category and value means what it is supposed to mean).

# Week 4 - Preprocessing

Model: logistic_regression  params={'max_iter': 1000}

Train accuracy: 0.678
Test accuracy:  0.674
Gap (train - test): +0.004
-----------------------------------------------------------

Model: dummy  params={}

Train accuracy: 0.549
Test accuracy:  0.550
Gap (train - test): -0.000
-----------------------------------------------------------

Model: decision_tree  params={'max_depth': 5}

Train accuracy: 0.685
Test accuracy:  0.678
Gap (train - test): +0.006
-----------------------------------------------------------

Model: random_forest  params={'n_estimators': 300}

Train accuracy: 0.722
Test accuracy:  0.644
Gap (train - test): +0.078
-----------------------------------------------------------

Conclusions

The results stayed almost the same, with the logistic regression going from 0.669 to 0.674 and the decision tree from 0.68 to 0.678. Both are above the dummy (0.55), which only predicts the most common class, so the models are learning. The random forest did worse (0.644) and had a bigger gap between train and validation (0.078), which suggests it is overfitting.
This week, 20% of the data was set aside as a test set that is not used at all (it will only be used at the end of the project to evaluate the final model). The models were trained on 75% of the remaining data and evaluated on the other 25% (validation), so they learned from fewer people than last weekand were tested on different ones. So, the main improvement this week is a more correct pipeline (no information leaking into the evaluation).

# Week 5 - Cross validation & hyperparameters 

Model: decision_tree  params={'random_state': 42}


Hyperparameters from config.yaml: {'random_state': 42}
Validation  mean = 0.610   std = 0.018

Nested cross-validation (the tuning procedure, estimated honestly):
Validation  mean = 0.672   std = 0.017

Best mean CV score:   0.679  (the maximum over all trials -- optimistic)
optimism = +0.002   -> report the honest one
Best hyperparameters: {'model__max_depth': 6, 'model__min_samples_leaf': 101, 'model__criterion': 'gini'}
-----------------------------------------------------------------------------------------------------------------------
Model: logistic_regression  params={'max_iter': 1000}

Hyperparameters from config.yaml: {'max_iter': 1000}
Validation  mean = 0.672   std = 0.013

Nested cross-validation (the tuning procedure, estimated honestly):
Validation  mean = 0.673   std = 0.016

Best mean CV score:   0.678  (the maximum over all trials -- optimistic)
optimism = +0.002   -> report the honest one
Best hyperparameters: {'model__C': 0.07154217398141553}
--------------------------------------------------------------------------------------------------------------------------
Model: random_forest  params={'n_estimators': 100, 'random_state': 42}

Hyperparameters from config.yaml: {'n_estimators': 100, 'random_state': 42}
Validation  mean = 0.647   std = 0.016

Nested cross-validation (the tuning procedure, estimated honestly):
Validation  mean = 0.679   std = 0.013

Best mean CV score:   0.683  (the maximum over all trials -- optimistic)
optimism = +0.003   -> report the honest one
Best hyperparameters: {'model__n_estimators': 237, 'model__max_depth': 8, 'model__min_samples_leaf': 61, 'model__max_features': 'log2'}
-------------------------------------------------------------------

# Week 6 - Ensembles

| Model | Default: CV mean ± std (gap) | Tuned: nested CV mean ± std (gap) | Tuning score (best trial) | Chosen hyperparameters |
|---|---|---|---|---|
| Logistic regression | 0.672 ± 0.013 (+0.003) | — | — | — |
| Decision tree | 0.610 ± 0.018 (+0.085) | 0.672 ± 0.017 (+0.013) | 0.679 | max_depth=6, min_samples_leaf=101, criterion=gini |
| Random forest | 0.650 ± 0.019 (+0.082) | 0.681 ± 0.015 (+0.009) | 0.683 | max_depth=22, min_samples_leaf=42, max_features=0.5 |
| Oblique random forest | 0.647 ± 0.011 (+0.114) | 0.679 ± 0.013 (+0.013) | 0.683 | tree max_depth=6, min_samples_leaf=3, n_components=5 |
| AdaBoost | 0.676 ± 0.012 (+0.006) | 0.682 ± 0.017 (+0.004) | 0.684 | max_depth=4, n_estimators=220, learning_rate=0.025 |

Adaboost had the highest nested CV score (0.682), followed by the random forest (0.681) and the oblique random forest (0.679) The three ensembles are only 0.001-0.003 apart, and about 0.01 above logistic regression and the tuned tree. These differences are smaller than the fold-to-fold std (~0.015), so no model is clearly better than the others. Without tuning, the forests overfit (gap +0.08 for RF and +0.11 for ORF), and tuning brought the gap down to about +0.01. AdaBoost already had a small gap with the default settings (+0.006). 
The chosen hyperparameters match how each model works. The random forest chose deep trees (max_depth=22), because averaging many trees reduces variance, so each tree can be deep
. AdaBoost chose shallow trees (max_depth=4) and a small learning rate (0.025), because boosting needs weak learners and a small learning rate makes each round change the model less. The number to report is the nested CV score (0.682 for AdaBoost), not the best trial score (0.684), because the best trial is the maximum over 30 trials and is optimistic.
-------------------------------------------------------------------------------------------------------------------------

# Week 7 - boosting & metrics

## Scoreboard (selection metric: ROC-AUC, 5-fold CV, nested CV for tuned models)
| Model | Untuned | Tuned (nested) mean ± std | Floor | Tuned hyperparameters |
|---|---|---|---|---|
| Gradient boosting | 0.727 | 0.727 ± 0.014 | 0.500 | n_estimators=426, learning_rate=0.012, max_depth=3, min_samples_leaf=2, subsample=0.77 |
| HistGradientBoosting | 0.717 | 0.727 ± 0.016 | 0.500 | max_iter=82, learning_rate=0.11, max_leaf_nodes=4, min_samples_leaf=130, l2_regularization=0.0007 |
| AdaBoost | 0.723 | 0.727 ± 0.015 | 0.500 | max_depth=4, n_estimators=55, learning_rate=0.17 |
| Oblique random forest | 0.685 | 0.726 ± 0.015 | 0.500 | tree max_depth=12, min_samples_leaf=87, n_components=8 |
| Random forest | 0.683 | 0.726 ± 0.013 | 0.500 | max_depth=9, min_samples_leaf=42, max_features=0.5 |
| Logistic regression | 0.719 | — | 0.500 | — |
| Decision tree | 0.604 | 0.717 ± 0.017 | 0.500 | max_depth=16, min_samples_leaf=88, criterion=gini |
| Naive Bayes | 0.685 | 0.705 ± 0.012 | 0.500 | var_smoothing=0.091 |
| Dummy | 0.500 | — | 0.500 | — |

All models are well above the floor of 0.5, so they all learn something from the data. The five ensembles end up almost the same after tuning (0.726–0.727), and the differences between them are much smaller than the std, so none of them is really better. Tuning helped the trees and forests a lot (the decision tree went from 0.604 to 0.717) because without limits they overfit, but it changed almost nothing for gradient boosting, because its default settings already use small trees. Naive Bayes is the weakest model, probably because it assumes the features are independent, and here they are not (for example age and age_cat).

## Selection metric
I used ROC-AUC as the selection metric. It measures how well the model ranks people from low to high risk, which is what a risk score like COMPAS is used for. It also doesn't depend on the 0.5 threshold, so choosing the threshold can be a separate decision later.

# Dataset -- COMPAS Recidivism (ProPublica)

## The problem

In 2016, ProPublica investigated COMPAS, a risk-assessment algorithm
actually used by courts in Broward County, Florida, to help inform
bail and sentencing decisions. COMPAS scores a defendant's likelihood
of reoffending on a 1-10 scale; judges could see that score when
deciding, among other things, whether someone should be released
before trial. ProPublica obtained COMPAS's scores for thousands of
defendants and matched them against what actually happened over the
following two years, then published the data.

This dataset is that data: each row is one defendant, with their
demographics and criminal history at the time of screening, COMPAS's
own risk score for them, and whether they were actually rearrested
within two years.

**Your task:** predict `two_year_recid` -- will this person be
rearrested within two years? -- from the case facts. Once you have a
model, the more interesting question is the one ProPublica actually
asked: is it equally accurate for everyone, or does it get things
wrong more often, in a particular direction, for some groups than
others? `race` is deliberately excluded from the model's own inputs
(see `config.yaml` and `src/preprocessing.py`) so it can be used
afterward purely to check this, in `src/evaluate.py`.

Before any of that: look at the data first. It comes from a real
system with real data-entry and record-keeping quirks -- don't assume
every column is clean or consistent just because it loads without
error.

## Data dictionary

| column | type | description | notable values |
|--------|------|--------------|------------------|
| `id` | identifier | internal record id | not a model feature |
| `sex` | categorical | defendant's sex | `Male`, `Female` |
| `age` | numeric | defendant's age (years) at screening | |
| `age_cat` | categorical | age bucket | `Less than 25`, `25 - 45`, `Greater than 45` |
| `race` | categorical | defendant's race, as recorded | `African-American`, `Caucasian`, `Hispanic`, `Asian`, `Native American`, `Other`; excluded from model features, used only to audit fairness |
| `juv_fel_count` | numeric | number of prior juvenile felony offenses | |
| `juv_misd_count` | numeric | number of prior juvenile misdemeanor offenses | |
| `juv_other_count` | numeric | number of other prior juvenile offenses | |
| `juvenile_total` | numeric | total juvenile offenses | |
| `priors_count` | numeric | number of prior adult offenses | |
| `prior_offenses` | numeric | number of prior offenses | |
| `age_in_months` | numeric | age expressed in months | |
| `c_charge_degree` | categorical | degree of the current charge | `F` (felony), `M` (misdemeanor) |
| `decile_score` | numeric | COMPAS's own risk score | 1 (lowest risk) to 10 (highest risk); excluded from model features, used only for comparison |
| `score_text` | categorical | COMPAS's own risk category | `Low`, `Medium`, `High`; excluded from model features, used only for comparison |
| `two_year_recid` | binary | **target** -- was this person rearrested within two years? | `0` = no, `1` = yes |

Source: derived from [propublica/compas-analysis](https://github.com/propublica/compas-analysis) (the data behind the "Machine Bias" investigation). Personally-identifying columns (name, date of birth, case numbers, charge descriptions) were removed.

