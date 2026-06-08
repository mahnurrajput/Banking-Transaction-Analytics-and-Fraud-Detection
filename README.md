# Banking Transaction Analytics & Fraud Detection
### Data Warehouse and Business Intelligence System for Banking CRM

> An enterprise-grade analytics pipeline built on a real financial transactions dataset — spanning 13M+ transactions, 5 source files, and a decade of banking behavior.

---

## Overview

This project delivers a fully integrated Banking CRM analytics system built on data warehousing principles. It processes a multi-million record financial dataset through a structured ETL pipeline, stores it in a Star Schema data warehouse, and surfaces insights through interactive Power BI dashboards — with dedicated fraud detection logic and anomaly rules layered throughout.

The system is designed to support two organizational functions simultaneously: **Customer Relationship Management** (spending patterns, segmentation, high-value customer identification) and **Risk & Fraud Management** (fraud trends, merchant risk, card-testing detection, geographic anomalies).

🎓 **Data Warehousing & Business Intelligence Project** | FAST NUCES – 2025

---

## Repository Access

> The complete technical implementation — ETL scripts, SQL schema, indexing strategies, DAX measures, and detailed reports — is maintained in a **private repository**.

📧 `mahnoornaveed2405@gmail.com`

---

## Dataset

The project is built on a real-world financial transactions dataset covering the **2010s decade**, sourced from Kaggle. It comprises five source files:

| File | Contents |
|---|---|
| `transactions_data.csv` | Transaction records — amounts, timestamps, merchant details, error codes, fraud labels |
| `cards_data.csv` | Card-level data — type, credit limit, activation date, linked customer |
| `users_data.csv` | Customer demographics — income, credit score, account details |
| `mcc_codes.json` | Industry-standard Merchant Category Codes with descriptions |
| `train_fraud_labels.json` | Binary fraud labels for supervised detection (fraud / legitimate) |

**Scale:** 13M+ transactions · 571M+ total transaction value · 13K labeled fraud cases · 5M+ chip transactions

---

## Data Warehouse Architecture

### Star Schema

**Fact Table — `fact_transactions`**

Central table capturing one row per transaction, linked to all dimensions:

| Column | Description |
|---|---|
| `transaction_id` | Primary key |
| `date_key` | FK → dim_date |
| `customer_key` | FK → dim_customer |
| `card_key` | FK → dim_card |
| `merchant_key` | FK → dim_merchant |
| `mcc_key` | FK → dim_mcc |
| `amount` | Transaction value |
| `fraud_label` | Binary fraud indicator (from labeled dataset) |
| `anomaly_flag` | Derived flag from ETL rules |

**Dimension Tables**

- `dim_customer` — demographics, income band, credit score tier
- `dim_card` — card type, credit limit, chip-enabled flag
- `dim_merchant` — merchant ID, name, city, state
- `dim_mcc` — MCC code and business category description
- `dim_date` — year, quarter, month, day, season

The schema is fully documented in the repository under `/docs/star_schema.png`.

---

## ETL Pipeline

**Orchestration:** Apache Airflow | **Processing:** Python & SQL

### Extract
- Ingested multi-million record CSV and JSON source files
- Handled multiple schema formats across transaction, card, user, and reference data

### Transform
- Null handling and data cleansing across all source files
- Surrogate key generation for all dimension tables
- Derivation of `anomaly_flag` using:
  - Velocity checks (high transaction frequency in short windows)
  - High-value spike detection relative to customer baseline
  - Geographic inconsistency flags
- Customer profile enrichment (income band, credit score tier)
- Integration of fraud labels from `train_fraud_labels.json`

### Load
- Sequential dimension table population to maintain referential integrity
- Bulk insert of structured transactions into `fact_transactions`
- Post-load indexing for query performance

---

## Power BI Dashboard — Key Visuals

The dashboard was designed to serve both executive-level summaries and analyst-level drill-downs.

### KPI Cards
Top-line exposure metrics: total transactions, total amount, fraud transaction count, and chip transaction volume. Establishes scale before any deeper analysis.

### Fraud by Chip vs Non-Chip (Donut Chart)
Non-chip transactions account for the overwhelming majority of fraud — a clear confirmation that chip adoption directly reduces fraud exposure. Approximately 40% of transactions are chip-based, yet chip fraud is minimal.

### Top Fraud Regions (Bar Charts)
- **States:** Texas (TX), Maryland (MD), and Michigan (MI) lead in fraud volume
- **Cities:** Abilene and Online transactions are highest-risk
- Blank/missing state values flagged as a data quality gap requiring remediation

### High-Risk Merchants & MCC Categories (Bar Charts)
- Merchant IDs **60569**, **27092**, and **48919** appear repeatedly in fraud records
- Top fraud MCC categories: Department Stores, Wholesale Clubs, Discount Stores, Money Transfer Services

### Yearly Fraud Trends (Line Chart)
Fraud peaked in **2010** and again in **2015–2017**. Non-chip fraud dominates across all years; chip fraud has remained consistently low since adoption.

### Customer Profile Scatter Plots
- Fraud appears across all income and credit score bands — not isolated to any single segment
- Higher density in **mid-income, mid-credit-score** customers
- High credit limit customers are also targeted, suggesting criminals deliberately pursue premium cards

### Error Description Analysis (Bar Chart)
"Insufficient Funds," "Bad PIN," and "Bad Card Number" dominate error logs — consistent with **card-testing behavior**, where criminals make small repeated attempts to validate stolen credentials before executing larger fraudulent transactions.

### Slicers & Filters
Year, month, quarter, city, state, MCC code, and card type slicers enable interactive forensic exploration of any fraud pattern.

---

## DAX Measures & Performance Metrics

All analytical KPIs are computed via DAX measures rather than pre-aggregated columns, ensuring accurate cross-filter behavior:

| Measure | What It Computes |
|---|---|
| Yearly Fraud % | Fraud transactions as a percentage of all transactions by year |
| Fraud Rate by Region | State-level fraud rate vs national average |
| Chip Adoption Rate | Share of chip transactions in total volume |
| Avg Credit Limit (Fraud) | Average credit limit of fraud-affected customers |
| Monthly Fraud Aggregation | Month-over-month fraud frequency for seasonal analysis |

---

## Key Findings

- Non-chip transactions are the primary fraud vector — enforcing chip-only at high-risk merchants would materially reduce exposure
- TX, MD, and MI require prioritized fraud controls
- Merchant IDs 60569 and 27092 are persistent fraud hotspots warranting audit and enhanced verification
- Repeated "Bad PIN" and "Insufficient Funds" errors indicate systematic card-testing attacks — rate limiting on failed attempts would disrupt this behavior
- Fraud is not income-correlated; mid-tier customers are not inherently safer than high-value ones
- Holiday months show a measurable increase in fraud frequency

---

## Recommendations

1. **Enforce chip-only transactions** at high-fraud MCC categories (Department Stores, Money Transfers)
2. **Audit flagged merchants** — IDs 60569, 27092, 48919 — with enhanced transaction monitoring
3. **Prioritize fraud controls** in TX, MD, and MI based on regional fraud rate deviation from national average
4. **Implement failed-attempt throttling** to disrupt card-testing patterns (Bad PIN, Insufficient Funds sequences)
5. **Fix data quality gaps** — missing merchant state/city fields degrade geographic fraud detection accuracy
6. **Extend to ML-based detection** — the labeled dataset is directly usable for training Decision Tree, Random Forest, or XGBoost fraud classifiers using MCC, credit score, amount, and merchant ID as features

---

## Technology Stack

| Layer | Technology |
|---|---|
| Data Warehouse | Star Schema (PostgreSQL) |
| Data Processing | Python, SQL |
| BI & Dashboards | Power BI (DAX) |
| Development Environment | VS Code, pgAdmin |

---

## Project Outcomes

This project demonstrates end-to-end applied competency in:

- Dimensional modeling and Star Schema design
- ETL pipeline development with multi-source integration
- OLAP-style analytical query design
- Fraud rule engineering and anomaly derivation
- DAX measure development for cross-filtered KPIs
- BI dashboard design for both executive and analyst audiences

---

## Contact

**Mahnoor Naveed**
📧 [mahnoornaveed2405@gmail.com](mailto:mahnoornaveed2405@gmail.com)
🔗 [LinkedIn](https://www.linkedin.com/in/mahnoorrrajput/)

---

*For access to ETL scripts, schema diagrams, DAX documentation, or dashboard files, please reach out directly.*
