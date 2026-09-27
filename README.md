# Bayesian Networks vs. Logistic Regression for Churn Prediction

## Overview

This project investigates whether increasing model complexity improves
customer churn prediction — and whether any predictive gain justifies the
additional complexity and loss of interpretability.

Using the Kaggle Telco Customer Churn dataset, five classification approaches
were compared:

- Logistic Regression
- Naive Bayes
- Naive Bayes using the Markov Blanket
- Tree-Augmented Naive Bayes (TAN)
- General Bayesian Network

The analysis combines predictive performance, calibration, statistical testing,
data-quality validation, and feature-selection analysis.

---

## Business Question

Bayesian Networks provide a visual representation of relationships between
customer attributes, which can be attractive for stakeholder communication.

However, this added structural complexity is only useful if it produces a
meaningful improvement in predictive performance.

The key question was:

> Does increasing model complexity actually improve churn prediction,
> and does any improvement justify the additional complexity?

---

## Dataset

**Source:** Kaggle — Telco Customer Churn (blastchar/IBM)

- Customers analyzed: **7,043**
- Churn rate: **26.5%**
- Churned customers: **1,869**

Before modeling, a data-quality issue was identified:

11 of 7,043 records (0.16%) contained blank `TotalCharges` values stored as
whitespace. All 11 customers had `tenure = 0`, indicating they were brand-new
customers who had not yet been billed.

These values were corrected using domain logic:

`TotalCharges = 0`

rather than median imputation.

---

## Models Compared

### 1. Logistic Regression

A linear baseline using one-hot encoded features.

### 2. Naive Bayes

Assumes features are conditionally independent given the churn outcome.

### 3. Naive Bayes — Markov Blanket

Naive Bayes retrained using only the variables identified by the General
Bayesian Network's Markov Blanket.

### 4. Tree-Augmented Naive Bayes (TAN)

Allows each feature to have one additional parent besides the churn variable.

### 5. General Bayesian Network

A freely learned network structure using HillClimbSearch, allowing up to
three parents per node.

---

## Key Results

| Model | ROC-AUC | Brier Score |
|---|---:|---:|
| Logistic Regression | **0.845** | **0.136** |
| TAN | 0.828 | 0.178 |
| Naive Bayes (Markov Blanket) | 0.826 | 0.165 |
| General Bayesian Network | 0.824 | 0.145 |
| Naive Bayes (Full) | 0.815 | 0.226 |

Logistic Regression achieved the highest ROC-AUC, PR-AUC, and best calibration
among the tested approaches.

---

## Statistical Validation

The difference in ROC-AUC was tested using a paired bootstrap procedure with
1,000 resamples on the same 2,113-customer held-out test set.

The null hypothesis was:

> H0: There is no significant ROC-AUC difference between Logistic Regression
> and each Bayesian Network variant.

### Results

| Comparison | Δ ROC-AUC | 95% CI |
|---|---:|---|
| Logistic Regression vs. TAN | +0.017 | [0.010, 0.024] |
| Logistic Regression vs. Naive Bayes (MB) | +0.019 | [0.010, 0.027] |
| Logistic Regression vs. General BN | +0.021 | [0.011, 0.030] |

All three confidence intervals excluded zero.

This indicates that the observed ROC-AUC advantage of Logistic Regression was
statistically supported in these comparisons.

---

## Markov Blanket Feature Selection

The General Bayesian Network provided one useful result even though its
predictive performance did not exceed Logistic Regression.

Its learned structure reduced the churn-relevant feature set from:

**19 features → 5 features**

This represents a **73.7% feature reduction**.

The five variables were:

- Contract
- InternetService
- PaperlessBilling
- TechSupport
- tenure

Naive Bayes retrained on these five variables achieved similar predictive
performance to its full-feature version.

The analysis also found that `gender` was excluded from the learned structure.

---

## Business Insights

Several churn patterns were identified in the analysis.

### Contract Type

| Contract | Churn Rate |
|---|---:|
| Month-to-month | 42.7% |
| One year | 11.3% |
| Two year | 2.8% |

Month-to-month customers showed substantially higher churn than customers
with longer-term contracts.

### Internet Service

| Internet Service | Churn Rate |
|---|---:|
| Fiber optic | 41.9% |
| DSL | 19.0% |
| No internet | 7.4% |

The analysis identifies fiber-optic customers as another segment worth
investigating for potential price, competition, or service-quality factors.

Churn was also concentrated among customers in the early months of tenure,
highlighting the onboarding period as an important retention window.

---

## Main Finding

Increasing model complexity did not improve predictive performance in this
dataset.

Logistic Regression provided the strongest combination of predictive
performance and interpretability.

The Bayesian Network analysis nevertheless added value through Markov Blanket
feature selection, reducing the feature set from 19 variables to 5 without
a meaningful loss in predictive performance for the corresponding Naive Bayes
model.

---

## Practical Application

The analysis supports using Logistic Regression as the primary churn model
for this dataset.

The five Markov Blanket variables can also be used as a compact feature set
for retention analysis:

- Contract
- InternetService
- PaperlessBilling
- TechSupport
- tenure

The Bayesian Network structure itself is not treated as a causal model.
Network edges represent statistical dependencies learned from the data, not
confirmed causal relationships.

---

## Limitations

Several limitations should be considered:

- HillClimbSearch finds a local optimum, so different random seeds or scoring
  methods may produce different network structures.
- Numeric variables were quantile-binned for Bayesian models but kept
  continuous for Logistic Regression.
- The comparison is therefore not perfectly format-neutral between model
  families.
- Bayesian Network edges represent statistical dependency, not causation.
- Findings are specific to this dataset and the fixed random seed used in the
  analysis.
- A dataset with stronger feature interdependence could produce different
  results.

---

## Technical Stack

- Python
- scikit-learn
- pgmpy
- Pandas
- NumPy
- Statistical testing
- Logistic Regression
- Naive Bayes
- Tree-Augmented Naive Bayes
- Bayesian Networks
- Bootstrap testing
- Feature selection
- Excel

---

## Project Files

### Excel Analysis

Contains live-formula calculations, model metrics, data-quality checks,
statistical test results, and dimensionality-reduction analysis.

### PDF Dashboard

A three-page visual analysis covering:

- Model comparison
- Statistical testing
- Structural complexity
- Markov Blanket feature reduction
- Churn patterns
- Business recommendations

### Executive Summary

A concise three-page summary of the research question, methodology,
statistical validation, findings, applications, and limitations.

---

## Project Structure

```text
bayesian-churn-classification/
│
├── README.md
│
├── analysis/
│   └── bayesian_churn_classification.xlsx
│
├── dashboard/
│   └── bayesian_churn_dashboard.pdf
│
└── report/
    └── bayesian_churn_executive_summary.pdf
