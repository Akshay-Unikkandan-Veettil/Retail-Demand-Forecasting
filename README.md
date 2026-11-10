# Retail Demand Forecasting & Safety Stock Optimisation

Replacing an Excel moving-average forecast with statistical and ML models, then translating the accuracy gain into inventory and cost decisions.

**Stack:** Python (pandas, statsmodels, Prophet, XGBoost, scikit-learn) · Plotly

---

## Business Problem
Planning relied on a 3-month moving average. It ignored seasonality and trend, which caused:
- Stockouts in peak months (November runs **84% above** average)
- Systematic under-forecasting (**-20.1% bias**)
- Oversized safety stock to compensate, costing **$929,918/year** to hold

**Question:** How much accuracy can better forecasting deliver, and what is it worth in inventory cost?

## Results
| Metric | Naive 3-Month MA | Prophet | Change |
|---|---|---|---|
| MAPE | 50.8% | 15.79% | -68% |
| MAE | $17,760/month | $9,922/month | -44% |
| Bias | -20.1% | -6.96% | -65% |
| Safety stock | 16,172 units | 7,996 units | -50% |
| Annual holding cost | $929,918 | $459,772 | **$470,146 saved** |

Against the **best** Excel method (Linear Trend + Seasonal, 19.0% MAPE), Prophet still cuts MAPE by ~3 points. That Excel model has the lowest MAE, a reminder that a well-built spreadsheet method is a strong baseline.

**Cost assumptions:** 95% service level (Z = 1.65), ~4-day average lead time (from order to ship dates), $230 average unit value, 25% annual holding rate.

## Full Model Scorecard
| Rank | Model | Type | MAPE | MAE | Bias |
|---|---|---|---|---|---|
| 1 | Prophet (with US holidays) | ML | 15.79% | $9,922 | -6.96% |
| 2 | Holt-Winters | Statistical | 17.40% | $10,490 | -5.82% |
| 3 | Linear Trend + Seasonal | Excel | 19.00% | $8,139 | -4.20% |
| 4 | XGBoost | ML | 25.95% | $15,100 | -25.95% |
| 5 | Seasonal Index | Excel | 26.74% | $9,703 | -10.92% |
| 6 | SARIMA (1,1,1)(1,1,1)12 | Statistical | 27.05% | $13,699 | +8.52% |
| 7 | Year-over-Year Growth | Excel | 27.73% | $11,933 | -6.73% |
| 8 | Exponential Smoothing | Excel | 46.71% | $17,981 | -11.66% |
| 9 | Weighted MA (50/30/20) | Excel | 48.66% | $18,900 | -8.70% |
| 10 | Naive 3-Month MA | Excel | 50.80% | $17,760 | -20.10% |

## What the Analysis Covers
1. **Data preparation:** 9,789 orders (2015-2018), date parsing, lead-time calculation
2. **Demand analysis:** category, sub-category and regional demand; seasonal decomposition
3. **ABC/XYZ segmentation:** 17 sub-categories by revenue share and demand variability, with a model choice per segment
4. **Demand sensing features:** US holidays, Q4, month-end, day-of-week
5. **Forecasting:** 6 Excel-style baselines vs Holt-Winters, SARIMA, Prophet, XGBoost
6. **SKU-level forecasting:** Phones and Chairs; Croston's method for intermittent items
7. **Safety stock optimisation:** inventory and cost impact by model
8. **S&OP scenarios:** pessimistic / base / optimistic (+/-15%) demand plans

## Key Takeaways for Planners
- **Use Prophet or Holt-Winters for monthly planning.** Both beat every Excel method on MAPE with low bias.
- **Resize safety stock** to the better forecast; it halves required buffer stock.
- **Plan November separately.** Actual demand ($117,938) beat even the optimistic scenario ($103,000). Peaks need promotion data, not just history.
- **Forecast intermittent SKUs differently.** Items like Copiers (CV 1.63) need Croston's method; SKU-level error (Phones 35.96% MAPE) is far higher than aggregate.
- **XGBoost underperformed** on only 36 months of monthly data; it needs longer, daily, SKU-level history.

## Data
Kaggle Superstore Sales dataset: 9,789 orders, 18 columns, 2015-2018 (Technology, Furniture, Office Supplies). Included as `dataset.csv`.

## How to Run
```bash
pip install pandas numpy matplotlib seaborn scikit-learn statsmodels prophet xgboost plotly notebook
jupyter notebook "fmcg_forecasting (1).ipynb"
```
Then run Kernel > Restart & Run All. Full step-by-step method is in `Methodology`.
