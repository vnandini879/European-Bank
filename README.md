# European Bank — Customer Churn Analysis

Exploratory analysis and Power BI dashboard built on a 10,000-customer dataset from a European bank, identifying who churns, why, and where the bank should focus retention efforts.

---

## Project Overview

This project analyzes customer churn for a European retail bank operating across **France, Germany, and Spain**. The goal is to identify the strongest behavioral and demographic drivers of churn and present them through an interactive Power BI dashboard that business stakeholders can explore directly.

## Dataset

| | |
|---|---|
| **Source file** | `European_Bank.csv` |
| **Rows** | 10,000 customers |
| **Snapshot** | 2025 (single-period, not a time series) |

| Column | Description |
|---|---|
| `CustomerId`, `Surname` | Customer identifiers |
| `CreditScore` | 350–850 |
| `Geography` | France / Germany / Spain |
| `Gender` | Female / Male |
| `Age` | 18–92 |
| `Tenure` | Years with the bank (0–10) |
| `Balance` | Account balance |
| `NumOfProducts` | Number of bank products held (1–4) |
| `HasCrCard` | Has a credit card (Yes/No) |
| `IsActiveMember` | Active vs inactive membership |
| `EstimatedSalary` | Estimated annual salary |
| `Exited` | **Target** — 1 = churned, 0 = retained |

## Key Insights

- **Overall churn rate: 20.4%** (7,963 retained, 2,037 churned)
- **Products held is the strongest churn driver** — customers with 3–4 products churn at 83–100%, while 2-product holders churn at just 7.6%
- **Germany churns at ~32%**, roughly double France and Spain (~16% each); **German female customers churn worst of all, at 37.6%**
- **Age follows a hump, not a slope** — churn peaks in the 46–65 range (48–51%) rather than rising steadily with age
- **Inactive members churn nearly 2x more** than active members (26.9% vs 14.3%)
- **Credit score and tenure have minimal effect** — churn stays in a narrow 17–24% band across all bins of both
- 36% of customers carry a **zero account balance**, and this segment churns *less* than balance-holding customers (13.8% vs 24.1%)

## Dashboard

Built in Power BI, across three pages:

1. **Overview** — KPI summary, churn by geography, churn split, geography × gender heatmap
2. **Churn Drivers** — credit score, age, products held, activity status, balance segment, and tenure vs. churn rate
3. **Customer Explorer** — filterable customer-level table with slicers for geography, gender, activity status, and products held

## DAX Measures

All measures below are created via **Modeling → New Measure**, referencing the main table (`'European_Bank'`).

```DAX
Total Customers = COUNTROWS('European_Bank')

Churned Customers = CALCULATE([Total Customers], 'European_Bank'[Exited] = 1)

Churn Rate = DIVIDE([Churned Customers], [Total Customers], 0)

Retained Customers = [Total Customers] - [Churned Customers]

Retention Rate = 1 - [Churn Rate]

Avg Credit Score = AVERAGE('European_Bank'[CreditScore])

Avg Balance = AVERAGE('European_Bank'[Balance])

Avg Salary = AVERAGE('European_Bank'[EstimatedSalary])

Avg Tenure = AVERAGE('European_Bank'[Tenure])

Active Member Rate = 
DIVIDE(
    CALCULATE([Total Customers], 'European_Bank'[IsActiveMember] = 1),
    [Total Customers]
)

Churn Rate - Active Members = 
CALCULATE([Churn Rate], 'European_Bank'[IsActiveMember] = 1)

Churn Rate - Inactive Members = 
CALCULATE([Churn Rate], 'European_Bank'[IsActiveMember] = 0)
```

| Measure | What it shows |
|---|---|
| `Total Customers` | Row count of the dataset (10,000) |
| `Churned Customers` | Count where `Exited = 1` |
| `Churn Rate` | % of customers who churned — used in `Format → Percentage` |
| `Retained Customers` | Customers minus churned customers |
| `Retention Rate` | Inverse of churn rate |
| `Avg Credit Score` | Average `CreditScore` across the filtered context |
| `Avg Balance` | Average account balance across the filtered context |
| `Avg Salary` | Average `EstimatedSalary` across the filtered context |
| `Avg Tenure` | Average years with the bank |
| `Active Member Rate` | % of customers flagged as active members |
| `Churn Rate - Active Members` | Churn rate filtered to active members only |
| `Churn Rate - Inactive Members` | Churn rate filtered to inactive members only |

> Format `Churn Rate`, `Retention Rate`, and `Active Member Rate` as **Percentage** in the field properties pane after creating them.

## Power Query (M) — Conditional Columns

These run in **Power Query Editor** (Power Query M, not DAX) and feed the model before the measures above are created.

```
// Churn Status
if [Exited] = 1 then "Churned" else "Retained"

// Active Status
if [IsActiveMember] = 1 then "Active" else "Inactive"

// Has Credit Card
if [HasCrCard] = 1 then "Yes" else "No"

// Age Band
if [Age] <= 25 then "18-25"
else if [Age] <= 35 then "26-35"
else if [Age] <= 45 then "36-45"
else if [Age] <= 55 then "46-55"
else if [Age] <= 65 then "56-65"
else "66+"

// Balance Tier (quartiles computed on non-zero balances only)
if [Balance] = 0 then "Zero Balance"
else if [Balance] <= 100182 then "Low Balance"
else if [Balance] <= 119840 then "Medium Balance"
else if [Balance] <= 139512 then "High Balance"
else "Very High Balance"
```

## How to Reproduce

1. Open Power BI Desktop and load `European_Bank.csv` via **Get Data → Text/CSV**
2. In Power Query, add the conditional columns: `Churn Status`, `Active Status`, `Has Credit Card`, `Age Band`, `Balance Tier`
3. Create the DAX measures (`Churn Rate`, `Retention Rate`, `Avg Balance`, etc. — see `/docs` or project notes)
4. Build the three report pages and apply consistent conditional-formatting color logic across visuals
5. Publish to the Power BI Service or export as PDF/PPTX for sharing

## Tech Stack

- **Power BI Desktop** — data modeling, DAX measures, dashboard
- **Power Query (M)** — data cleaning and feature engineering

**Overview**
![image alt](https://github.com/vnandini879/European-Bank/blob/aa2902d5ee25a66acb232d62a0f0ec9ec797e963/Overview.png)

**Churn Drivers**
![image alt](https://github.com/vnandini879/European-Bank/blob/e996cde5017dfa3a09ed8c27d66ae1fefd557a11/Churn%20Drivers.png)

**Taable View**
![image alt](
---

### Author
**Nandini Verma**
*Data and Business Analyst*
*[GitHub](https://github.com/vnandini879)*
*[LinkedIn](https://www.linkedin.com/in/Analyst-kashishagrawal/)*
- 


