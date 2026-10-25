# SWYNEX-Model-Improvement
# Task 3: Improving and Validating the Selected Model

Part of Drug Review Sentiment and Side-Effect Mining (UCI Drug Review).

> Disclaimer: For research and education only. This is not medical advice and not pharmacovigilance.

# Objective

Take the best model from Task 2 (Linear SVM or Logistic Regression on TF-IDF features), improve it with feature engineering, hyperparameter tuning and decision-threshold tuning, then validate whether the improvement is real by comparing it with the Task 2 baseline.

# Problem

3-class sentiment classification (negative / neutral / positive) of patient drug reviews. Labels are derived from the 1-10 rating: 1-4 negative, 5-6 neutral, 7-10 positive. Ratings are a noisy proxy for sentiment, which limits the achievable score.

# Metrics

- Macro-F1 is the main metric because the classes are imbalanced and accuracy would reward predicting "positive".
- Also reported: accuracy, balanced accuracy, macro precision and recall, weighted-F1, macro ROC-AUC (one-vs-rest), Matthews correlation, per-class F1/precision/recall, and confusion matrices.
- 
# Reading the results

- The written findings state whether the improvement is statistically meaningful, small and not conclusive, or absent, based on the bootstrap interval and cross-validation. If there is no real gain, the simpler baseline should be kept, and that is a valid finding.
- The neutral class (ratings 5-6) is usually the hardest, because its wording overlaps with both other classes. Most of the change, good or bad, tends to show up there.
- The class offsets are evaluated on the test set only. The cross-validation compares the models without them.

# Limitations

- Labels come from ratings, a noisy proxy for sentiment, and reviews are self-selected.
- The side-effect lexicon covers only the listed terms. Negation uses a simple 3-word window.
- A mentioned symptom does not prove the drug caused it.
- Results depend on the sample size (`MAX_ROWS`) and the random split.

# Files in this task

- `task3_improve_and_validate.py`: the full code
- `README_task3.md`: this file

# Dataset

UCI Drug Review Dataset (Drugs.com), Gräßer et al., 2018. Columns used: `drugName`, `review`, `rating`. Source: https://archive.ics.uci.edu/dataset/462/drug+review+dataset+drugs+com
