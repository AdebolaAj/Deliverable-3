# MSCS 634 — Deliverable 3

This deliverable compares classifiers, identifies clusters in image-derived measurements, and mines association rules for the Wisconsin Diagnostic Breast Cancer dataset. It uses 569 observations with 30 measurements and a recorded benign or malignant diagnosis. [Dataset documentation](https://doi.org/10.24432/C5DW2B)

## Run the analysis

From this folder, install the dependencies with `python -m pip install -r requirements.txt` and run the notebook top to bottom. The bundled CSV supplies the data. The notebook saves three CSV tables and three figures. The completed run was verified with Python 3.13, scikit-learn 1.9.1, and mlxtend 0.25.0. [Analysis notebook](analysis.ipynb)

## Classification and tuning

The stratified split contains 455 training and 114 holdout observations, including 42 malignant cases in the holdout. Malignant is the positive class. Logistic regression standardizes predictors in a pipeline. The tree search compares 48 combinations of depth, minimum leaf size, and class weights using malignant F1 on five training folds.

| Model | Holdout accuracy | Malignant recall | Malignant F1 | ROC AUC | Average precision | Missed malignant cases |
|---|---:|---:|---:|---:|---:|---:|
| Logistic regression | **96.49%** | **92.86%** | **0.951** | **0.996** | **0.994** | **3** |
| Default decision tree | 92.98% | 90.48% | 0.905 | 0.925 | 0.854 | 4 |
| Tuned decision tree | 90.35% | 83.33% | 0.864 | 0.908 | 0.912 | 7 |

The tuned tree used balanced class weights, depth 5, and at least 5 observations per leaf. Mean training CV F1 rose from 0.896 to 0.938. In the holdout, the tuned tree had lower F1 and higher average precision than the default tree. Logistic regression led at the default threshold and on both ranking metrics. [Classification metrics](classification_results.csv) · [Confusion matrices and performance curves](classification_performance.png)

## Clustering

K-means compared cluster counts from 2 to 8 on standardized training measurements. Two clusters had the highest training silhouette (**0.343**). The holdout silhouette was **0.317**, and the adjusted Rand index against diagnosis was **0.617**. The higher-radius cluster contained 33 malignant and 3 benign observations. The other cluster contained 9 malignant and 69 benign observations. These groups describe measurement profiles with overlapping diagnoses. [Cluster-selection metrics](clustering_results.csv) · [Silhouette, PCA, and diagnosis by cluster](clustering_summary.png)

## Association rules

Apriori found **13** qualifying malignancy rules using training medians and minimum training support of 12%, confidence of 60%, and lift of 1.20. The highest-lift training rule combined high mean concavity and high mean radius. On the holdout, **40 of 46** matches were malignant, giving **35.09% support**, **86.96% confidence**, and **2.36 lift**. Malignant prevalence was 36.84% across all holdout observations. High concavity alone matched more observations and had lower confidence (**42 of 62**, or **67.74%**). The rules can guide checks for overlapping measurements and help identify interactions to test in future models. [Full rule table](association_rules.csv) · [Training and holdout lift comparison](association_rule_lift.png)

## Scope and citation

These results describe a historical benchmark of breast mass images. A single holdout gives limited precision for error counts and rule estimates, and the available fields do not support demographic subgroup analysis. External imaging data would help assess how well the patterns transfer.

Wolberg, W., Mangasarian, O., Street, N., & Street, W. (1993). *Breast Cancer Wisconsin (Diagnostic)* [Dataset]. UCI Machine Learning Repository. [Dataset citation](https://doi.org/10.24432/C5DW2B)
