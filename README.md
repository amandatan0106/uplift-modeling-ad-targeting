# Uplift Modeling & Incremental Ad Targeting
### Creative Gaming Case Study | UC San Diego — February 2026
>  Fully reproducible with synthetic data — all code runs end to end without the original dataset.
---

## Overview

This project applies a **Two-Model uplift framework** to estimate individual-level incremental lift from in-app advertising for a mobile gaming company. The core question: which users actually convert *because* of an ad — rather than buying regardless?

Standard propensity scoring ranks users by their likelihood to convert. But this conflates users who would buy anyway (zero incremental value from the ad) with users who genuinely respond to being shown the ad. Uplift modeling separates these groups by estimating the *causal* effect of the ad on each individual.

---

## The Business Problem

Creative Gaming ran a randomized experiment to measure the effect of in-app advertising on campaign purchases. With ~120,000 users across treatment (ad shown) and organic control (no ad) groups, the goal was to identify which users to target in future campaigns to maximize incremental revenue — not just conversion volume.

---

## Methodology

### Two-Model Uplift Framework

For each model type, two separate models are trained:
- **Control model**: trained on users who saw no ad → predicts P(convert | no ad)
- **Treatment model**: trained on users randomly assigned to see the ad → predicts P(convert | ad)

**Uplift score** = P(convert | ad) − P(convert | no ad)

Users are then ranked by uplift score and targeted from highest to lowest incremental lift.

### Models Evaluated

| Model | Notes |
|---|---|
| Logistic Regression | Baseline; most interpretable |
| Neural Network (MLP) | Grid searched over layer sizes and learning rates |
| Random Forest | Grid searched over depth and estimator count |
| XGBoost | Grid searched over depth, learning rate, and subsample |

All models tuned via 5-fold cross-validation on the training split.

### Evaluation

Models are evaluated using:
- **Qini curves**: cumulative incremental response as a function of users targeted
- **Profit framework**: net revenue at each targeting threshold (accounting for ad cost)
- **Comparison to propensity baseline**: same models, ranked by P(convert|ad) instead of uplift score

---

## Key Results

Uplift-based targeting **consistently outperforms propensity-based targeting** across all four models:

| Targeting Method | Incremental Response (Top 50% of Users) |
|---|---|
| XGBoost — Uplift | Highest |
| Random Forest — Uplift | High |
| Logistic Regression — Uplift | Moderate |
| XGBoost — Propensity | ~40% lower than uplift |

**Recommendation:** Target the top 40–50% of users by XGBoost uplift score. Beyond this threshold, incremental response drops sharply and ad spend no longer covers cost. Avoid showing ads to the bottom 30% of users ("sleeping dogs" — may reduce conversion when shown an ad).

---

## Repository Structure

```
uplift-modeling-creative-gaming/
│
├── uplift_modeling_creative_gaming.ipynb   # Main notebook
└── README.md
```

> **Note:** Raw data is not included (proprietary case dataset). The notebook documents the full methodology, hyperparameter grids, evaluation framework, and results.

---

## Tools & Libraries

`Python` · `Polars` · `Scikit-learn` · `XGBoost` · `NumPy` · `Matplotlib`

---

## Author

**Amanda Tan**
M.S. Business Analytics — UC San Diego, Rady School of Management
[LinkedIn](http://www.linkedin.com/in/aamandatan)
