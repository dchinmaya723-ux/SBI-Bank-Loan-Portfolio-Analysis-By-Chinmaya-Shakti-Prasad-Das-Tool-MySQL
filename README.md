# 🏦 SBI Bank — Loan Portfolio Analysis (SQL Project)

**Author:** Chinmaay Shakti Prasad Das  
**Tool:** MySQL  
**Domain:** Banking & Financial Analytics  
**Focus:** Credit Risk · Customer Segmentation · Portfolio Health

---

## 📌 Project Overview

This project simulates a **real-world banking loan portfolio analysis** for State Bank of India (SBI) using structured SQL queries in MySQL. It covers everything from raw database design and data seeding to advanced analytics using window functions, CASE logic, and multi-table JOINs — the kind of queries that power actual bank dashboards and credit risk reports.

The goal is to analyze a loan book of **60 customers and 70 loans** across multiple dimensions: product mix, NPA (Non-Performing Assets), customer risk profiling, geographic exposure, and year-wise disbursement growth.

---

## 🗄️ Database Schema

The project uses a single database (`SBI`) with two core tables:

### 🧑 `Customers`
Stores demographic and financial profile of each borrower.

| Column | Type | Description |
|---|---|---|
| `customer_id` | INT (PK) | Unique customer identifier |
| `name` | VARCHAR(100) | Full name |
| `city` | VARCHAR(100) | Customer's city |
| `income` | INT | Annual income in INR |
| `age` | INT | Age of the customer |
| `occupation` | VARCHAR(50) | Salaried / Self-Employed / Business |
| `credit_score` | INT | Credit score (range: 300–900) |

### 💳 `Loans`
Stores loan-level details linked to each customer.

| Column | Type | Description |
|---|---|---|
| `loan_id` | INT (PK) | Unique loan identifier |
| `customer_id` | INT (FK) | Links to Customers table |
| `loan_type` | VARCHAR(50) | Home / Personal / Auto / Education / Business |
| `loan_amount` | DECIMAL(15,2) | Loan amount in INR |
| `interest_rate` | DECIMAL(5,2) | Annual interest rate (%) |
| `tenure_months` | INT | Loan repayment period in months |
| `status` | VARCHAR(20) | Active / Closed / NPA |
| `loan_date` | DATE | Date of disbursement |
| `emi_amount` | DECIMAL(10,2) | Monthly EMI in INR |

---

## 📂 Project Structure

```
SBI_Analysis/
│
├── SBI_Analysis.sql          # Full SQL script (schema + data + all queries)
├── SBI_Analysis_Dataset.xlsx # Source dataset with analytical sheets & dashboard
└── README.md                 # Project documentation
```

---

## 📊 Dataset Summary

- **60 Customers** across 20+ Indian cities (Mumbai, Delhi, Hyderabad, Bangalore, Chennai, etc.)
- **70 Loans** spanning 5 loan types with dates ranging from 2015 to 2023
- **Loan Status Mix:** Active, Closed, and NPA (Non-Performing Assets)
- **Occupation Types:** Salaried, Business, Self-Employed
- **Credit Score Range:** 410 (very poor) to 860 (excellent)

---

## 🔍 SQL Analysis — Query Breakdown

The project is organized into **6 analytical sections**, with 16 queries total.

---

### Section 1 — Core Portfolio Analysis

**Q1. Total Portfolio Exposure (Gross Loan Book)**  
Calculates the total loan book value in Crore (₹Cr), total loan count, and unique customer count. This is the headline number for any bank's portfolio report.

**Q2. Portfolio Mix by Loan Type**  
Breaks down the loan book by product (Home, Personal, Business, Auto, Education), showing loan count, exposure in ₹Cr, portfolio share %, and average interest rate per product. Uses `SUM() OVER()` window function to calculate portfolio percentage.

**Q3. City-wise Loan Exposure — Geographic Risk**  
Joins Customers and Loans to identify which cities hold the highest exposure. Useful for detecting geographic concentration risk in the portfolio.

---

### Section 2 — NPA & Credit Risk Analysis

**Q4. NPA Summary — Headline Risk Metrics**  
Groups the loan book by status (Active / Closed / NPA) and calculates exposure and share of the total book. This is a standard regulatory/board-level metric.

**Q5. NPA Rate by Loan Type**  
Identifies which loan product has the highest default rate using conditional aggregation (`CASE WHEN status = 'NPA'`). Answers: *Is Personal Loan riskier than Home Loan?*

**Q6. NPA by Credit Score Band**  
Segments customers into four credit bands (Excellent / Good / Fair / Poor) and computes NPA rates per band. Validates the hypothesis that lower credit scores correlate with higher defaults.

**Q7. NPA by Occupation**  
Compares default rates across Salaried, Business, and Self-Employed customers. Also shows average credit score per segment to understand borrower quality.

---

### Section 3 — Customer Segmentation

**Q8. Income Band Segmentation**  
Groups customers into income brackets (High / Mid / Low / Very Low) and shows loan exposure, average interest rate, and NPA rate per segment. Useful for targeted product design.

**Q9. Loan-to-Income Ratio (LTI) per Customer**  
Calculates each customer's total debt relative to annual income and assigns a risk category:
- `LTI > 10` → Very High Risk
- `LTI > 5` → High Risk
- `LTI > 3` → Moderate Risk
- `LTI ≤ 3` → Low Risk

**Q10. Top 10 Largest Borrowers**  
Uses `ROW_NUMBER() OVER (ORDER BY ...)` to rank customers by total loan exposure. Highlights single-name concentration risk in the portfolio.

---

### Section 4 — Advanced Analytics

**Q11. EMI Burden Ratio per Customer**  
Computes each active borrower's total monthly EMI as a percentage of their monthly income. Flags customers as:
- `> 60%` → Over-leveraged
- `> 40%` → Stressed
- `≤ 40%` → Healthy

**Q12. Year-wise Loan Disbursement Trend**  
Tracks yearly loan disbursements from 2015–2023, showing loans disbursed, total amount, average ticket size, and average interest rate. Useful for business growth analysis.

**Q13. Customer Ranking by City — DENSE_RANK()**  
Uses `DENSE_RANK() OVER (PARTITION BY city ORDER BY ...)` to find the top 2 borrowers in each city. Demonstrates partitioned window ranking.

**Q14. Running Total of Loan Disbursements (Cumulative Book)**  
Uses `SUM() OVER (ORDER BY year ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW)` to build a cumulative loan book, alongside `LAG()` to compute year-on-year growth %.

**Q15. High-Risk Customer Watchlist**  
Identifies NPA customers and flags them as CRITICAL / HIGH RISK / WATCH based on a combination of:
- Credit score < 550
- LTI > 5
- Loan status = NPA

This is a classic Credit Risk Dashboard query used in real banking systems.

---

### Section 5 — Executive Summary

**Q16. Portfolio Health Scorecard**  
A single-query executive dashboard showing: Gross Book (₹Cr), Total Loans, Total Customers, Average Ticket Size, Average Yield %, Gross NPA %, Active Book, and Closed Book — the kind of summary that goes into a CEO/Board presentation.

---

## 🛠️ SQL Concepts Used

| Concept | Queries |
|---|---|
| `JOIN` (INNER) | Q3, Q6, Q7, Q8, Q9, Q10, Q11, Q13 |
| `GROUP BY` + Aggregations | All sections |
| `CASE WHEN` (Conditional Logic) | Q6, Q7, Q8, Q9, Q11, Q15, Q16 |
| Window Functions (`SUM OVER`, `ROW_NUMBER`, `DENSE_RANK`, `LAG`) | Q2, Q3, Q4, Q10, Q13, Q14 |
| Subqueries | Q10, Q13, Q14 |
| `ROUND`, `YEAR()`, `COUNT(DISTINCT)` | Throughout |
| NPA Rate Calculation | Q4, Q5, Q6, Q7, Q8 |

---

## 💡 Key Business Insights (Sample)

- **Personal Loans** carry the highest NPA rate among all loan types
- Customers with **credit scores below 550** are significantly more likely to default
- **Self-Employed** borrowers show higher NPA rates compared to Salaried customers
- Several customers have **LTI ratios above 10x**, indicating very high over-leverage
- The loan book shows consistent **year-on-year growth** from 2015 to 2023

---

## ▶️ How to Run

```sql
-- Step 1: Run the full script in MySQL Workbench or any MySQL client
SOURCE SBI_Analysis.sql;

-- Step 2: Switch to the SBI database
USE SBI;

-- Step 3: Run any individual query (Q1–Q16) to explore the analysis
```

**Requirements:** MySQL 8.0+ (window functions require MySQL 8.0 or later)

---

## 📁 Excel Dashboard

The companion file `SBI_Analysis_Dataset.xlsx` contains pre-built analytical sheets including:
- Portfolio Overview KPIs
- NPA Summary & NPA by Loan Type
- Credit Score Band Analysis
- City-wise Geographic Exposure
- Credit Risk Watchlist
- Interactive Portfolio Dashboard

---

*This project is built for learning and portfolio demonstration purposes using a simulated banking dataset.*
