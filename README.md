# Customer Shopping Trend Analysis
 
An end-to-end retail analytics project on a 3,900-row consumer shopping dataset. Raw data is cleaned and feature-engineered in Python/pandas, then explored in SQL Server through 15 business-framed queries covering revenue drivers, product performance, customer segmentation, and behavioural patterns. The project ships with a cleaned dataset, a written report, and a presentation deck.
 
## Project workflow
 
```
Raw CSV  ──►  Python / pandas cleaning  ──►  cleaned CSV  ──►  SQL Server EDA  ──►  report + deck
 (3,900 rows)     (impute, standardize,        (source of         (15 queries,
                   feature-engineer)            truth)             4 themes)
```
 
## Dataset
 
`customer_shopping_behavior.csv` — 3,900 rows, 18 columns of retail transaction data spanning customer demographics (age, gender, location), purchase details (item, category, amount, season), and behaviour fields (subscription status, discount usage, payment method, purchase frequency, review rating).
 
## Phase 1 — Data cleaning & feature engineering (Python)
 
Handled in `Customer_Shopping_Trend_Analysis.ipynb`:
 
- **Missing values.** `review_rating` was the only column with nulls — 37 of 3,900 rows (~0.9%). Imputed each using the **median rating within the row's own category**, rather than a single global median, so filled values stay consistent with how each category tends to be rated.
- **Column standardization.** All 18 headers normalized to lowercase `snake_case` (e.g. `Purchase Amount (USD)` → `purchase_amount_usd`) so they're safe to reference directly in both pandas and SQL without quoting.
- **Feature engineering:**
  - `age_group` — continuous age bucketed into four labelled cohorts (`young_adult`, `adult`, `middle_aged`, `senior`) via `pd.cut` for interpretable cohort comparison.
  - `purchase_frequency_days` — the categorical `frequency_of_purchases` label (`Weekly`, `Monthly`, `Annually`, …) mapped to a numeric days-between-purchases value, making frequency directly rankable and comparable.
- **Redundancy check.** `discount_applied` and `promo_code_used` were confirmed identical across all 3,900 rows, so the redundant `promo_code_used` was dropped.
The cleaned frame is exported to `cleaned_customer_trend.csv`, the single source of truth for the SQL phase.
 
## Phase 2 — Exploratory analysis (SQL Server)
 
`customer_shopping_trend_analysis.sql` contains 15 queries organised into four themes. Each query is preceded by the business question it answers and a note on why that framing was chosen.
 
**1. Revenue & spending patterns**
Revenue split by gender; discount-users who still spend above average; spend by shipping type; revenue contribution per age group; top locations by *average* revenue per customer (controlling for population size).
 
**2. Product & category performance**
Top products by review rating; products with the highest discount rate; top 3 products within each category (`ROW_NUMBER() ... PARTITION BY`); leading category per season (`RANK()`).
 
**3. Customer segmentation & loyalty**
Subscriber vs non-subscriber spend; New/Returning/Loyal segmentation by purchase history; subscription rates among repeat buyers; purchase-frequency vs spend relationship.
 
**4. Behavioural & cross-factor analysis**
Discount usage vs review rating; average purchase amount by payment method.
 
## SQL techniques demonstrated
 
Aggregation and conditional aggregation (`SUM(CASE WHEN …)`), subqueries, CTEs, window functions (`ROW_NUMBER`, `RANK` with `PARTITION BY`), and per-group ranking — with an emphasis on framing each query around a decision a business would actually make.
 
## Repository contents
 
| File | What it is |
| :--- | :--- |
| `Customer_Shopping_Trend_Analysis.ipynb` | Phase 1 — Python cleaning & feature engineering |
| `customer_shopping_trend_analysis.sql` | Phase 2 — 15-query SQL Server EDA |
| `customer_shopping_trend.csv` | Dataset |
| `Report_CSTA.pdf` | Written analysis report |
| `Consumer_Shopping_Trends_Presentation.pptx` | Summary presentation deck |
| `LICENSE` | MIT |
 
## How to reproduce
 
1. Run the notebook top to bottom to reproduce the cleaning and export `cleaned_customer_trend.csv`.
2. Load that CSV into a SQL Server table named `customer` (column names already match the script).
3. Run `customer_shopping_trend_analysis.sql` in SSMS — queries are independent and can be run section by section.
> Built on Microsoft SQL Server (T-SQL) and Python 3 (pandas).
 
## Tech stack
 
Python · pandas · Microsoft SQL Server · T-SQL · Jupyter
