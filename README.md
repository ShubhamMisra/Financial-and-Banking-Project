# 💳 Finance & Banking Analytics Dashboard

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-4479A1?style=for-the-badge&logo=sqlite&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white)

---

## 📌 Project Overview

An end-to-end data analytics project built on a realistic Finance & Banking dataset — covering the full analyst workflow from **raw data ingestion** through **cleaning**, **SQL validation**, **transformation**, and **interactive Power BI visualization**.

The project simulates a real-world bank's transaction environment with intentionally injected data quality issues, referential integrity violations, and business rule conflicts — designed to mirror what analysts encounter in production systems.

---

## 🎯 Objectives

- Build a complete ETL pipeline from raw CSV files to a clean, queryable database
- Identify and resolve 6 categories of data quality issues across 6 tables
- Validate referential integrity and business rules using SQL
- Deliver actionable insights through a 5-page interactive Power BI dashboard

---

## 🗂️ Dataset

| Table | Rows | Description |
|---|---|---|
| `transactions_fact` | 5,516 | Core fact table — all bank transactions |
| `customers_dim` | 1,040 | Customer profiles and segments |
| `products_dim` | 266 | Financial products and services |
| `geography_dim` | 110 | Location and regional data |
| `reps_lookup` | 77 | Relationship managers |
| `customer_rep_bridge` | 1,196 | Many-to-many customer-rep assignments |

**Time range:** January 2024 – February 2026 (26 months)
**Total transaction value:** $11.06M+

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python (pandas, numpy) | Data ingestion, cleaning, transformation |
| Regular Expressions (re) | ID normalization, format standardization |
| SQLite | Relational database, SQL validation |
| Power BI | Interactive dashboard and visualization |
| DAX | Calculated measures and KPIs |
| VS Code + Jupyter Notebook | Development environment |

---

## 📁 Project Structure

```
Finance-Banking-Analytics/
│
├── data/
│   ├── raw/                          # Original raw CSV files
│   │   ├── 01_transactions_fact.csv
│   │   ├── 02_customers_dim.csv
│   │   ├── 03_products_dim.csv
│   │   ├── 04_geography_dim.csv
│   │   ├── 05_reps_lookup.csv
│   │   └── 06_customer_rep_bridge.csv
│   │
│   └── clean/                        # Cleaned CSV files
│       ├── 01_transactions_fact_CLEAN.csv
│       ├── 02_customers_dim_CLEAN.csv
│       ├── 03_products_dim_CLEAN.csv
│       ├── 04_geography_dim_CLEAN.csv
│       ├── 05_reps_lookup_CLEAN.csv
│       └── 06_customer_rep_bridge_CLEAN.csv
│
├── notebooks/
│   ├── 01_data_ingestion.ipynb       # Load and inspect raw data
│   ├── 02_data_cleaning.ipynb        # Full cleaning pipeline
│   ├── 03_sql_validation.ipynb       # Referential integrity checks
│   └── 04_transformation.ipynb       # RFM, cohort, rolling averages
│
├── scripts/
│   ├── stage3_clean.py               # Reusable cleaning script
│   └── stage7_visualize.py           # Python visualization script
│
├── database/
│   └── bank.db                       # SQLite database
│
├── dashboard/
│   └── Finance_Banking_Dashboard.pbix # Power BI dashboard file
│
└── README.md
```

---

## 🔧 Data Quality Issues Resolved

### Missing Values (10–15% of rows affected)
| Column | Issue | Resolution |
|---|---|---|
| `transaction_date` | 161 missing dates | Imputed from `process_start_date` |
| `amount` | 215 missing values | Flagged with `amount_missing` column |
| `channel` | 221 missing values | Filled with `"Unknown"` category |
| `duration_sec` | 193 missing values | Filled with median value |
| `join_date` | 62 missing dates | Flagged with `join_date_missing` column |
| `age` | 60+ missing values | Filled with median after outlier removal |

### Duplicate Records (3–5%)
| Issue | Count | Resolution |
|---|---|---|
| Safe duplicate transaction IDs | 108 IDs | Dropped extra copy (keep first) |
| Conflicting duplicate transaction IDs | 8 IDs | Flagged with `needs_review = True` |
| Exact duplicate customer rows | 20 rows | Dropped duplicates |
| Near-duplicate customer names | Various | Standardized casing/spacing |

### Inconsistent Formatting
| Column | Issue | Resolution |
|---|---|---|
| `amount` | `$1,200.00`, `1200`, `1,200` mixed | Stripped `$` and `,`, converted to float |
| `transaction_date` | 3 date formats mixed | Parsed with `format='mixed'` |
| `customer_id` | `CUST-00123`, `C00123`, `00123` mixed | Normalized with regex function |
| `status` | `Active`, `active`, `ACTIVE`, `Active ` | `.str.strip().str.title()` |
| `active_flag` | `True/False`, `YES/NO`, `1/0` mixed | Standardized to `Yes/No` |
| `phone` | 3 phone formats mixed | Standardized to `000-0000-000` |

### Outliers & Anomalies
| Issue | Count | Resolution |
|---|---|---|
| `age = 999` (sentinel error) | 15 rows | Replaced with median |
| Negative age values | 10 rows | Replaced with median |
| Negative amounts (`-99999`) | 40 rows | Flagged with `flag_negative_amount` |
| Extreme outliers (>5x p99) | 32 rows | Flagged with `flag_extreme_outlier` |
| Future-dated transactions (2027) | 10 rows | Flagged for review |

---

## ✅ Business Rule Violations Detected

| Rule | Violations Found |
|---|---|
| `amount = 0` on `status = Completed` | 14 rows |
| `process_end_date` before `process_start_date` | 12 rows |
| Customer `age < 18` on adult-verification segments | 13 rows |
| Duplicate `transaction_id` with different amounts | 8 IDs |
| Orphaned customer FKs (no match in dimension) | 237 rows |
| Orphaned product FKs (no match in dimension) | 381 rows |
| Transactions referencing inactive products | 855 rows |

---

## 🔗 Data Model (Star Schema)

```
                    customers_dim
                    PK: customer_id
                          │
                          │ 1
                          │
geography_dim ────────────┼──────────── transactions_fact (FACT)
PK: location_id      *   │   *         FK: customer_id
                          │             FK: product_id
products_dim ─────────────┤             FK: location_id
PK: product_id            │             FK: rep_id
                          │
reps_lookup ──────────────┘
PK: rep_id
    │
    │ (many-to-many via bridge)
    │
customer_rep_bridge
FK: customer_id, rep_id
```

---

## 📊 Power BI Dashboard

### 5 Dashboard Pages

#### Page 1 — Main Dashboard
- 8 KPI Cards: Total Revenue, Total Profit, Total Transactions, Success Rate, Churn Rate, Avg Transaction Value, Active Customers, Active Products
- Monthly Revenue Trend (line chart)
- Revenue by Channel (bar chart)
- Revenue by Region (bar chart)
- Customer by Segment (pie chart)
- Revenue by State (filled map)

#### Page 2 — Customer Analysis
- Total Active Customers card
- Churn Rate card
- Customers by Segment (pie chart)
- Revenue by Customer Segment (bar chart)
- Customer Distribution by State (filled map)
- New Customers per Month (line chart)

#### Page 3 — Product Performance
- Total Profit card
- Revenue by Category (pie chart)
- Top 10 Products by Revenue (bar chart)
- Product Performance Details (table)

#### Page 4 — Geographic Analysis
- Revenue by State (filled map)
- Revenue by Region (bar chart)
- Top 10 Cities by Transaction Volume (bar chart)
- Transaction Count: Region vs Channel (matrix heatmap)

#### Page 5 — Rep Performance
- Avg Revenue per Rep card
- Top 10 Reps by Revenue (bar chart)
- Revenue by Team (bar chart)
- Rep Performance Details (table)

---

## 📐 DAX Measures

```dax
-- Total Revenue (excluding negatives)
Total Revenue =
CALCULATE(
    SUM(transactions_fact[amount]),
    transactions_fact[amount] > 0
)

-- Success Rate
Success Rate =
DIVIDE(
    COUNTROWS(FILTER(transactions_fact, transactions_fact[status] = "Completed")),
    COUNT(transactions_fact[transaction_id])
) * 100

-- Churn Rate
Churn Rate % =
DIVIDE(
    COUNTROWS(FILTER(customers_dim, customers_dim[status] = "Inactive")),
    COUNTROWS(customers_dim)
) * 100

-- Total Profit
Total Profit =
SUMX(
    transactions_fact,
    RELATED(products_dim[unit_price]) - RELATED(products_dim[cost])
)

-- Avg Revenue per Rep
Avg Revenue Per Rep =
DIVIDE(
    [Total Revenue],
    DISTINCTCOUNT(transactions_fact[rep_id])
)

-- Active Products
Active Products =
COUNTROWS(
    FILTER(products_dim, products_dim[active_flag] = "Yes")
)
```

---

## 💡 Key Business Insights

```
📈 Revenue & Performance
├── Total Revenue:        $11.06M across 26 months
├── Success Rate:         81.95% — strong transaction completion
├── Avg Transaction:      $1.31K per transaction
└── Total Profit:         $613.99M (financial product margins)

👥 Customer Insights
├── Active Customers:     638 (out of 1,040 total)
├── Churn Rate:           17.50% ⚠️ needs attention
├── Top Segment:          Retail (38.7% of customers)
└── Fastest growing:      Small Business (26.7%)

🌍 Geographic Insights
├── Top Region:           South ($4M+ revenue)
├── Lowest Region:        West
├── Top City:             Springfield (highest transaction volume)
└── Key States:           TX, FL, NY (darkest on heatmap)

📱 Channel Insights
├── Top Channel:          Phone ($3M+)
├── Second:               Branch
└── Digital channels:     ATM, Mobile App, Online all similar

👔 Rep Performance
├── Avg Revenue/Rep:      $122.86K
├── Top Team:             Commercial Lending
├── Lowest Team:          Digital Banking
└── Top Rep:              Tyrone G (~$500K revenue)
```

---

## 🚀 How to Run

### Prerequisites
```bash
pip install pandas numpy matplotlib seaborn faker
```

### Step 1 — Generate Dataset
```bash
python generate.py
```

### Step 2 — Clean Data
```bash
python stage3_clean.py
```

### Step 3 — Load into SQL
```python
import sqlite3
import pandas as pd

conn = sqlite3.connect("bank.db")
df = pd.read_csv("clean/01_transactions_fact_CLEAN.csv")
df.to_sql("transactions", conn, if_exists="replace", index=False)
```

### Step 4 — Open Power BI Dashboard
```
Open Finance_Banking_Dashboard.pbix in Power BI Desktop
Click Refresh to load latest clean data
```

---

## 📝 Business Insights 

Finding:

   17.50% of customers are Inactive
   Industry benchmark for banking: 10-15%
   voize is 2-7% above acceptable threshold
   Dormant + Closed customers add further risk

Root Cause Analysis:

  Student segment has lowest revenue contribution
  Private Banking segment smallest (4.6% of customers)
  No early warning system for at-risk customers

Recommendations:

✅ Action 1: Implement RFM-based early warning system
   → Flag customers with recency > 90 days
   → Trigger automated re-engagement campaigns

✅ Action 2: Segment-specific retention strategy
   → Student segment: offer product upgrades at graduation
   → Dormant customers: personalized outreach within 60 days

✅ Action 3: Set churn KPI target
   → Target: reduce churn from 17.50% to below 12%
   → Review monthly on Customer Analysis dashboard

Expected Impact:

Reducing churn by 5% on 1,040 customers =
52 retained customers × $1,310 avg transaction =
$68,120 additional revenue per transaction cycle
🔴 Problem 2 — Data Quality Issues in Core Transaction System

Finding:

   221 transactions with missing channel data
   215 transactions with missing amount values
   161 missing transaction dates
   14 completed transactions with $0 amount
   232 duplicate transaction IDs detected

Root Cause Analysis:

   No input validation at point of transaction entry
   Multiple systems recording same transaction
   No automated duplicate detection in pipeline
   Missing mandatory field enforcement

Recommendations:

✅ Action 1: Implement upstream data validation
   → Enforce mandatory fields at transaction entry point
   → Amount cannot be NULL or 0 on Completed status
   → Channel must be selected before submission

✅ Action 2: Build automated duplicate detection
   → Flag transactions within 60 seconds of same amount
   → Same customer + same amount + similar timestamp = alert

✅ Action 3: Create data quality dashboard
   → Monitor missing value % weekly
   → Alert when missing values exceed 5% threshold
   → Track duplicate rate month over month

Expected Impact:

Eliminating data quality issues improves:
  Reporting accuracy by ~4%
  Regulatory compliance confidence
   Analyst time saved: 3-4 hours per report cycle
🟡 Problem 3 — Revenue Concentration Risk (South Region Dominance)

Finding:

   South region: $4M+ (largest share)
   West region:  lowest revenue
   Top city (Springfield) drives disproportionate volume
   Heavy dependence on single region = business risk

Root Cause Analysis:

   Fewer relationship managers assigned to West region
   Product portfolio may not match West region demographics
   Digital Banking underperforming vs Branch/Phone channels

Recommendations:

✅ Action 1: Geographic expansion strategy
   → Increase rep assignments in West region
   → Target underperforming states (grey on heatmap)
   → Set regional revenue targets per quarter

✅ Action 2: Channel optimization by region
   → West region: push Mobile App adoption
   → Northeast: leverage existing Branch strength
   → South: maintain Phone channel dominance

✅ Action 3: Territory rebalancing
   → Redistribute rep workload from South to West
   → Hire 2-3 new reps specifically for West territory

Expected Impact:

Bringing West region to Midwest revenue levels =
~$1M additional annual revenue opportunity
🟡 Problem 4 — Underperforming Digital Banking Channel

Finding:

  Phone:      $3M+ (highest channel revenue)
  Branch:     $2.5M+
  ATM:        $1.5M
  Mobile App: $1.5M
  Online:     $1.5M (lowest digital channel)

Root Cause Analysis:

   Older customer segments prefer traditional channels
   Digital onboarding experience may be friction-heavy
   Mobile App and Online combined still < Phone alone
    Digital Banking team has lowest revenue per rep

Recommendations:

✅ Action 1: Digital channel incentive program
   → Offer 0.25% better rate for online transactions
   → Reduce fees for Mobile App users
   → Target Retail and Student segments first

✅ Action 2: Improve digital onboarding
   → Reduce steps to complete a transaction online
   → Add in-app guidance for first-time digital users
   → Track drop-off rate at each onboarding step

✅ Action 3: Shift Phone volume to Digital
   → 10% shift from Phone to Online =
     ~$300K revenue maintained digitally at lower cost

Expected Impact:

Digital channel cost is 60-80% lower than branch/phone
Moving 10% of transactions digital =
$150-200K operational cost saving annually
🟡 Problem 5 — Product Portfolio Risk (Inactive Products Still Transacted)

Finding:

├── 855 transactions reference inactive/discontinued products
├── 31 inactive products out of 260 total (12%)
├── Customers using discontinued products = compliance risk
└── No automated alert when discontinued product is transacted

Root Cause Analysis:

├── Product deactivation not synced to transaction system
├── No hard block on discontinued product transactions
└── Relationship managers unaware of product status changes

Recommendations:

✅ Action 1: Hard block on discontinued products
   → System should reject new transactions on inactive products
   → Grandfather existing contracts with sunset date

✅ Action 2: Customer migration plan
   → Identify 855 affected transactions' customers
   → Migrate them to equivalent active products
   → Assign dedicated rep for migration conversations

✅ Action 3: Product lifecycle management
   → 90-day notice before product deactivation
   → Automated customer communication on product changes

Expected Impact:

   Eliminate compliance risk from discontinued products
   Improve customer experience during product transitions
   Reduce operational overhead from legacy product support
🟢 Problem 6 — Rep Performance Inequality

Finding:

  Top rep (Tyrone G): ~$500K revenue
  Bottom reps: <$100K revenue
  5x performance gap between top and bottom reps
  Avg revenue per rep: $122.86K

Root Cause Analysis:

   No standardized sales playbook across teams
   Commercial Lending team significantly outperforms Digital Banking
   Unequal customer assignment (power customers concentrated)
   No performance coaching framework in place

Recommendations:

✅ Action 1: Knowledge transfer program
   → Top 3 reps mentor bottom 5 reps quarterly
   → Document and share best practices from Commercial Lending
   → Create standardized customer engagement playbook

✅ Action 2: Rebalance customer portfolio
   → Redistribute "power customers" more evenly
   → Ensure no single rep holds >15% of total revenue
   → Reduces key person dependency risk

✅ Action 3: Performance incentive structure
   → Tiered commission based on revenue growth %
   → Team bonuses for Commercial Lending best practices adoption
   → Monthly performance review against $122.86K avg benchmark

Expected Impact:

Lifting bottom 10 reps by 20% =
10 reps × $24,572 additional revenue =
$245,720 additional annual revenue






---

## 👤 Author

**Shubham Mishra**
Data Analyst | Python | SQL | Power BI

---

## 📄 License

This project is for portfolio and educational purposes only.
Dataset is synthetically generated — no real customer data used.
