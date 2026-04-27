# Walmart Retail Analytics
## Predicting Store-Department Sales and Identifying Underperforming Products

![Pipeline Architecture](./pipeline.gif)

## Interactive Architecture Diagram

[![View Architecture Pipeline](https://img.shields.io/badge/View-Interactive%20Pipeline-%2300C7B7?style=for-the-badge&logo=netlify)](https://eloquent-beignet-eb72db.netlify.app/)

**Tools:** PySpark | Machine Learning Models (Random Forrest, Decision Tree, XGBoost, Regression Models) | Python | Google Colab | Matplotlib | Seaborn

**Dataset:** Walmart Store Sales Forecasting (Kaggle) | 420,000+ weekly records | 45 stores | 99 departments | 2010 to 2012

**Result:** XGBoost achieved RMSE of 3,317, outperforming 4 competing models and enabling three actionable business strategies

---

## The Problem

Retailers live and die by inventory accuracy. Too much stock ties up capital and creates waste. Too little means empty shelves, lost revenue, and damaged customer trust.

For a retailer the size of Walmart, with 45 stores and 99 departments each behaving differently based on store type, location, seasonality, and local demographics, manual forecasting is not just impractical. It is impossible.

This project answers two questions:

1. Can we accurately predict weekly department-level sales across all stores?
2. Can those predictions drive real operational decisions in workforce planning, supply chain, and merchandising?

---

## The Data

Three source files joined into a single analytics table using PySpark:

| File | Rows | Description |
|---|---|---|
| train.csv | 421,570 | Weekly sales by Store, Dept, Date with holiday flag |
| features.csv | 8,190 | Economic and promotional context: temperature, fuel price, CPI, unemployment, markdown promotions |
| stores.csv | 45 | Store type (A, B, C) and size in square feet |

---

## The Pipeline

The project runs end to end through five stages:

**Stage 1: Data Ingestion**
Three raw CSV files loaded into PySpark DataFrames and joined into a single master table (master_df) with 421,570 rows and 17 columns.

**Stage 2: Data Cleaning**
Duplicate columns from the join resolved programmatically. Null markdown values imputed with 0.0 (absence of a promotion is itself a signal). Numeric columns cast to correct types. Schema validated before proceeding.

**Stage 3: Feature Engineering**
Three categories of features engineered to give the model the context it needs:

- Calendar features: Year, Month, WeekOfYear to capture seasonality patterns
- Lag features: Weekly_Sales_lag1 and lag2 to capture sales momentum
- Rolling average: 4-week moving average (MA4) to smooth noise and detect trends

**Stage 4: Modeling**
Five regression models trained and compared using time-based train/validation split (80/20) to prevent data leakage:

| Model | RMSE | MAE |
|---|---|---|
| XGBoost | 3,317 | 1,588 |
| Linear Regression | 3,798 | 2,004 |
| GBT Regressor | 4,269 | 1,932 |
| Decision Tree | 5,020 | 2,092 |
| Random Forest | 5,283 | 2,255 |

XGBoost won by a clear margin. The top three predictive features were previous week sales (lag1), 4-week moving average, and holiday indicator. Macro-economic variables like CPI and fuel price had minimal predictive power at the weekly store level.

**Stage 5: Business Intelligence**
Model outputs translated into three operational strategies:

- Workforce Surge Plan: Store-level staffing recommendations for the holiday season based on projected demand using a $5,000 weekly revenue per FTE benchmark
- Supply Chain Risk Matrix: Stores plotted by physical size versus projected demand to identify restocking bottlenecks requiring daily delivery
- Hyper-Localized Assortment: Top 3 performing departments identified per store to guide localized shelf-space decisions

---

## Key Findings

Sales are driven by momentum and seasonality, not macroeconomics. Last week's sales are the single strongest predictor of this week's sales. CPI, fuel price, and unemployment rate have negligible impact on weekly store-level fluctuations.

XGBoost outperforms linear baselines by 13% on RMSE. The model captures complex non-linear seasonal spikes that linear regression cannot.

Holiday lift is not uniform. Stores like Store 13 and Store 10 require 80+ temporary staff during peak season. Stores like Store 33 show minimal holiday lift and should maintain standard headcount. Blanket hiring policies waste payroll.

Small stores with high sales are supply chain bottlenecks. These stores physically cannot hold enough safety stock for a week. They require daily cross-docking rather than weekly bulk replenishment.

---

## Business Impact

| Area | Insight | Recommendation |
|---|---|---|
| Workforce | Holiday surge varies massively by store | Tiered hiring: 82 temps for high-surge stores, standard headcount for flat-demand stores |
| Supply Chain | Small stores with high volume cannot buffer inventory | Daily cross-docking for red-zone stores; hub stores hold safety stock |
| Merchandising | Top departments vary by store location | Localize shelf-space allocation based on store-specific buying patterns |

---

## Project Structure

```
walmart-retail-analytics/
│
├── walmart_retail_analytics.ipynb       # Full PySpark pipeline: ingestion, cleaning, modeling, BI
├── README.md                            # This file
├── pipeline_illustration.html           # Animated illustration of events flow
├── data/
│   └── (Download from Kaggle: Walmart Store Sales Forecasting)
│
└── outputs/
    ├── model_leaderboard.png
    ├── feature_importance.png
    ├── workforce_surge.png
    ├── supply_chain_risk_matrix.png
    └── hyper_localized_assortment.png
```

---

## How to Run

1. Download the dataset from Kaggle: https://www.kaggle.com/c/walmart-recruiting-store-sales-forecasting
2. Upload train.csv, features.csv, and stores.csv to your Google Drive
3. Open walmart_retail_analytics.ipynb in Google Colab
4. Set the path to your data files in Google Drive correctly in Colab code
5. Run all cells top to bottom

Required packages are installed automatically in the first cell via pip.

---

## Limitations and Assumptions

The workforce heuristic of $5,000 weekly revenue per FTE is derived from Walmart's reported annual revenue per employee and is used as an approximation for staffing recommendations. Store size is assumed constant throughout the analysis period. Null markdown values are treated as zero promotional activity. For future forecasting, lag features are unavailable so structural store attributes serve as proxies.

---

## Authors

**Mohnish Arora** | Model development, hyperparameter tuning, feature importance analysis, results comparison

Sakshi Khose | Data cleaning, preprocessing pipeline, initial visualizations

Prajwal Sapkale | Supply chain risk matrix, workforce surge analysis, business implications

Rajasee Thakre | Problem definition, dataset documentation, introduction

University of Arizona, Eller College of Management | MIS 584 Big Data Analytics | December 2025
