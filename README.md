# SWYNEX-Model-Improvement
# Task 3: Improving and Validating the Selected Model
Part of *Drug Review Sentiment and Side-Effect Mining (UCI Drug Review)*. 

> Disclaimer: For research and education only. This is not medical advice and not pharmacovigilance.

# Objective

Take the best model from Task 2 (a Linear SVM or Logistic Regression on TF-IDF features), improve it with feature engineering, hyperparameter tuning and decision-threshold tuning, and then validate whether the improvement is real by comparing it with the Task 2 baseline.

# Problem

The task is 3-class sentiment classification (negative, neutral, positive) of patient drug reviews. Labels are derived from the 1-10 rating: 1-4 is negative, 5-6 is neutral, and 7-10 is positive. Ratings are only a noisy proxy for sentiment, which limits the score any model can reach.

# Approach

The work starts by reusing the Task 2 setup. The same cleaning, the same stratified 80/20 split and the same random seed are applied, so the baseline and the improved model are compared on identical data. The baseline is the Task 2 model: word TF-IDF with 1-2 grams on the raw text and a tuned classifier.

The first improvement is negation marking. Words that follow "not", "no", "never" and similar terms are rewritten as `NOT_word`, so a phrase like "not helpful" is no longer confused with "helpful". The second is a character-level TF-IDF (2-5 grams, word-bounded), which copes with misspellings and informal wording common in patient text. The third is a small set of text-statistic and side-effect features: review length, exclamation count, capital-letter ratio, number of negators, and the number of reported and negated side effects taken from the project lexicon. These tie the task back to the side-effect mining goal.

To see which change actually helps, the features are added stage by stage in an ablation. The best stage is chosen by cross-validation on the training set only, never on the test set, so the test data stays unseen.

The improved model is then tuned with a grid search over the regularisation strength C and the word n-gram range, scored on macro-F1. The baseline's C is tuned too, so the comparison is fair. Finally, class-score offsets are added to the decision scores, chosen on out-of-fold training predictions. This mainly helps the hard neutral class, and the offsets are applied only if they gain at least 0.002 macro-F1.

# Metrics

Macro-F1 is the main metric, because the classes are imbalanced and plain accuracy would reward a model that mostly predicts "positive". The results also report accuracy, balanced accuracy, macro precision and recall, weighted-F1, macro ROC-AUC (one-vs-rest), the Matthews correlation coefficient, per-class F1, precision and recall, and confusion matrices.

# Validation

Several checks test whether the improvement is real rather than luck. Paired 5-fold cross-validation with a paired t-test shows whether the improved model wins consistently across folds. A paired bootstrap on the test set gives a 95% confidence interval for the macro-F1 difference, which shows whether the gain is larger than sampling noise. The McNemar test checks whether the improved model fixes more errors than it introduces. An unseen-drug split, grouped by drug name, shows whether the gain holds on drugs the model never saw in training. A learning curve shows whether more data would still help and whether the gain depends on data size. Finally, an error analysis lists example reviews that were fixed or broken by the improvement.

# Outputs

The script saves everything to the `results_task3/` folder and also zips it as `task3_results.zip`. The folder contains `baseline_vs_improved.csv` with the test-set metrics for both models and the change, and `per_class_comparison.csv` with per-class precision, recall and F1 before and after. It also holds `feature_ablation.csv` with the cross-validation and test macro-F1 for each feature stage, `paired_cv.csv` with the fold-by-fold scores for both models, and `learning_curve.csv` with macro-F1 against training-set size. The written findings, generated from the actual results, are in `task3_findings.md`. The improved pipeline, class offsets and label names are saved in `improved_model.joblib`. Three charts are included: `confusion_before_after.png`, `ablation_and_per_class.png` and `learning_curve.png`.

# Reading the results

The written findings state whether the improvement is statistically meaningful, small and not conclusive, or absent, based on the bootstrap interval and the cross-validation. If there is no real gain, the simpler baseline should be kept, and that is a valid finding. The neutral class (ratings 5-6) is usually the hardest, because its wording overlaps with both other classes, so most of the change, good or bad, tends to show up there. The class offsets are evaluated on the test set only, while the cross-validation compares the models without them.

# Limitations

Labels come from ratings, which are a noisy proxy for sentiment, and reviews are self-selected. The side-effect lexicon covers only the listed terms, and negation uses a simple 3-word window. A mentioned symptom does not prove the drug caused it. Results also depend on the sample size (`MAX_ROWS`) and the random split.

# Files in this task

`task3_improve_and_validate.py` holds the full code, and `README_task3.md` is this file.

# Dataset

UCI Drug Review Dataset (Drugs.com), Gräßer et al., 2018. The columns used are `drugName`, `review` and `rating`. Source: https://archive.ics.uci.edu/dataset/462/drug+review+dataset+drugs+com
