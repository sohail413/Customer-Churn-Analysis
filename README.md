# Telco Customer Churn Analysis

A data analysis project exploring customer churn behavior for a telecom
company, using exploratory data analysis (EDA) to uncover the key
factors driving customer attrition and inform retention strategy.

## 📌 Project Overview

Customer churn is one of the most critical metrics for subscription and
service-based businesses. This project analyzes the **IBM Telco
Customer Churn dataset** to understand *who* is churning and *why*,
using data cleaning and exploratory data analysis techniques.

## 📁 Repository Structure

```
├── Telco_customer_churn.xlsx   # Raw dataset
├── data_cleaning.py            # Data cleaning & preprocessing script
├── eda_analysis.py             # Exploratory data analysis & insights
├── cleaned_churn.csv           # Cleaned dataset (generated after running data_cleaning.py)
└── README.md                   # Project documentation
```

## 📊 Dataset

The dataset contains **7,043 customer records** with **33 features**,
including demographic information, account/contract details,
subscribed services, billing information, and churn status.

Key columns include:
- **Demographics**: Gender, Senior Citizen, Partner, Dependents
- **Account Info**: Tenure, Contract, Payment Method, Paperless Billing
- **Services**: Phone Service, Internet Service, Online Security,
  Tech Support, Streaming TV/Movies, etc.
- **Billing**: Monthly Charges, Total Charges, CLTV
- **Target**: Churn Label / Churn Value / Churn Score / Churn Reason

## 🧹 Data Cleaning (`data_cleaning.py`)

The cleaning script goes beyond basic null-handling — it validates
structure, checks cross-column logical consistency, and documents the
reasoning behind every fix. Steps performed:

1. **Initial inspection** — shape, dtypes, `.info()`, and summary
   statistics on the raw 7,043-row / 33-column dataset.
2. **Structural checks** — confirmed **0 exact duplicate rows** and
   **0 duplicate CustomerIDs**.
3. **Categorical validation** — printed unique values for every
   categorical column to catch typos/inconsistent labels, and replaced
   stray empty/whitespace-only strings with `NaN`. Flagged `Country`
   and `State` as constant (single-market extract), so they get
   dropped later.
4. **Fixed `Total Charges`**:
   - Converted from text to numeric (`errors='coerce'`).
   - Investigated *why* the 11 resulting missing values occurred
     before imputing — all 11 belong to customers with **0 months
     tenure** (brand-new customers not yet billed).
   - Set those 11 values to **$0** instead of blindly filling with
     the median, since a median fill would overstate charges for a
     customer who hasn't been billed yet.
   - Flagged (not modified) any rows where `Total Charges` <
     `Monthly Charges` despite tenure > 0, for downstream awareness.
5. **Handled `Churn Reason` nulls** — verified the 5,174 missing
   values line up exactly with non-churned customers (`Churn Label ==
   'No'`), then filled with `"Not Applicable"` instead of a vague
   `"unknown"`.
6. **Cross-column consistency checks**:
   - `Churn Label` vs. `Churn Value` agreement (Yes ↔ 1, No ↔ 0).
   - Dependent-service logic — e.g. `Online Security`, `Tech Support`,
     `Streaming TV`, etc. should read `"No internet service"` whenever
     `Internet Service == "No"`.
   - `Multiple Lines` should read `"No phone service"` whenever
     `Phone Service == "No"`.
7. **Outlier detection** — IQR method applied to `Tenure Months`,
   `Monthly Charges`, `Total Charges`, `Churn Score`, and `CLTV`.
   No extreme outliers found; all values fall within plausible
   business ranges.
8. **Dropped irrelevant/redundant columns**: `CustomerID`, `Count`,
   `Country`, `State` (constant), `Zip Code`, `Lat Long`, `Latitude`,
   `Longitude`.
9. **Feature engineering**:
   - `Churn Category` — buckets `Churn Score` into **Low / Moderate /
     High** risk tiers.
   - `Tenure Group` — buckets `Tenure Months` into lifecycle stages
     (**0-1 yr, 1-2 yrs, 2-4 yrs, 4+ yrs**) for easier grouping in EDA.
10. **Final validation** — confirmed **0 remaining nulls** and **0
    duplicates** before exporting to `cleaned_churn.csv` (7,043 rows ×
    27 columns).

## 📈 Exploratory Data Analysis (`eda_analysis.py`)

The EDA script visualizes and analyzes churn patterns across multiple
dimensions, including contract type, internet service, payment
method, tenure, monthly charges, tech support, and dependents.

### Key Insights

| Finding | Detail |
|---|---|
| **Overall churn rate** | ~26.5% of customers have churned |
| **Contract type matters most** | Month-to-month churn ≈ 43% vs. one-year ≈ 11% vs. two-year ≈ 3% |
| **Fiber optic risk** | Fiber optic customers churn ≈ 42%, vs. DSL ≈ 19%, vs. no internet ≈ 7% |
| **Tech support reduces churn** | No tech support churn ≈ 42% vs. with tech support ≈ 15% |
| **Payment method** | Electronic check payers churn ≈ 45%, vs. ≈ 15-17% for automatic payment methods |
| **Tenure** | Churned customers average ~18 months tenure vs. ~38 months for retained customers |
| **Pricing** | Churned customers pay ~$74/month on average vs. ~$61/month for retained customers |
| **Dependents** | Customers without dependents churn more than those with dependents |

### Business Recommendation

Retention efforts should prioritize **month-to-month, fiber-optic
customers who pay via electronic check and lack tech support** — this
segment shows the highest combined churn risk. Encouraging longer
contract terms, autopay enrollment, and tech support bundling could
meaningfully reduce churn.

## 🛠️ Tech Stack

- **Python 3**
- **pandas** / **numpy** — data manipulation
- **matplotlib** / **seaborn** — data visualization

## ▶️ How to Run

```bash
pip install pandas numpy matplotlib seaborn openpyxl

# Step 1: Clean the raw data
python data_cleaning.py

# Step 2: Run exploratory analysis
python eda_analysis.py
```

