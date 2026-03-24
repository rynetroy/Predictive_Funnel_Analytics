# Predictive Funnel Analysis: Session-Level Conversion & Revenue Model

A propensity-scored hurdle model for e-commerce conversion optimization, built on 2M+ sessions across 5 relational tables.

**Author:** Troy Dela Rosa | 

---

## The Business Problem

E-commerce platforms lose revenue to funnel leakage — 94.8% of sessions abandon without purchasing. This project builds a two-stage predictive system that identifies which sessions are at risk of abandoning and estimates the revenue impact of each session.

## The Solution

**Stage 1 — Classification (Propensity Model):** XGBoost classifier scores every session with a conversion probability based on behavioral ratios, customer demographics, and campaign context.

**Stage 2 — Regression (Revenue Estimator):** Segment-based estimator using each customer's historical average spend predicts basket value for converted sessions.

**Combined — Hurdle Model:** Expected Revenue = P(conversion) × Predicted Basket Value


---

## Dataset

This project uses the [Marketing & E-Commerce Analytics Dataset](https://www.kaggle.com/) from Kaggle.

**To reproduce this project**, download the dataset from Kaggle and place the following 5 CSV files in the root project directory:

- `customers.csv` (100,000 rows)
- `events.csv` (2,000,000+ rows)
- `transactions.csv` (103,127 rows)
- `products.csv` (2,000 rows)
- `campaigns.csv` (50 rows)

> **Note:** The raw CSV files are not included in this repository due to file size limitations. The `events.csv` file alone is 175MB+. Download the dataset directly from Kaggle to run the notebook end-to-end.

| Table | Rows | Description |
|-------|------|-------------|
| customers | 100,000 | Demographics, loyalty tier, acquisition channel |
| events | 2,000,000+ | Clickstream data (views, clicks, cart adds, purchases, bounces) |
| transactions | 103,127 | Purchase records with amounts and refund flags |
| products | 2,000 | Category, brand, price, premium flag |
| campaigns | 50 | Channel, objective, target segment, expected uplift |

## Final Features (25)

After leakage removal, encoding, and feature engineering:

**Behavioral (4):**
`session_duration_sec`, `event_velocity`, `cart_to_view_ratio`, `click_to_view_ratio`

**Customer & Campaign (2):**
`age`, `expected_uplift`

**One-Hot Encoded (19):**
`loyalty_tier` (3), `acquisition_channel` (4), `device_type` (3), `channel` (5), `objective` (4)


## Pipeline Architecture

```
5 Raw CSVs
    │
    ▼
Session Aggregation (2M+ events → 2M sessions)
    │
    ▼
Feature Engineering (ratios, velocity, duration)
    │
    ▼
Merge (customer + campaign + time-aware historical spend via merge_asof)
    │
    ▼
Leakage Removal (13 features dropped across 4 rounds)
    │
    ▼
Train/Val/Test Split (60/20/20, stratified, BEFORE encoding)
    │
    ▼
Impute + OneHotEncode (fit on train only)

```

## Leakage Prevention

- `event_purchase` never used as a feature — it directly encodes the target
- All raw event counts dropped — they reconstruct the funnel outcome
- Historical spend computed via `merge_asof` using only prior transactions
- `OneHotEncoder` and `SimpleImputer` fit exclusively on training data
- `train_test_split` performed before any encoding or imputation
- 8 automated integrity tests validate the pipeline on every run

---


## Author

**Troy Dela Rosa**
- LinkedIn: [linkedin.com/in/troy-dela-rosa-4058115a](https://linkedin.com/in/troy-dela-rosa-4058115a)
- Course: Supervised Machine Learning, RRC Polytech
- Series: *Crafting the Narrative* on LinkedIn
