# 🏦 Goldman Sachs Customer Transaction Analytics — Python Project

![Python](https://img.shields.io/badge/Language-Python-3776AB?logo=python&logoColor=white) ![Jupyter](https://img.shields.io/badge/Tool-Jupyter%20Notebook-F37626?logo=jupyter&logoColor=white) ![Status](https://img.shields.io/badge/Status-Completed-brightgreen) ![Domain](https://img.shields.io/badge/Domain-Financial%20Analytics-gold)

## 📌 Project Overview

This project performs an end-to-end **financial transaction analysis** on a Goldman Sachs customer dataset using **Python in Jupyter Notebook**. It covers everything from data cleaning and customer profiling to risk detection, exploratory data analysis, and statistical hypothesis testing.

The goal is to extract actionable insights on customer behavior, identify high-risk accounts, detect anomalous transactions, and provide data-backed recommendations for customer engagement and fraud monitoring.

---

## 🗂️ Dataset Overview

| Field | Description |
|---|---|
| `TransactionID` | Unique transaction identifier |
| `CustomerID` | Unique customer identifier |
| `AccountID` | Bank account identifier |
| `AccountType` | Type of account (Credit, Loan, etc.) |
| `TransactionType` | Nature of transaction (Deposit, Withdrawal, Payment, Transfer) |
| `TransactionAmount` | Value of the transaction |
| `AccountBalance` | Current account balance |
| `TransactionDate` | Date of the transaction |
| `Region` | Customer's geographical region |
| `RiskScore` | Pre-assigned risk score per customer |

---

## 🛠️ Libraries Used

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import zscore, ttest_ind
from scipy import stats
```

---

## 🧹 Task 1 — Data Cleaning & Formatting

- Removed **special characters and currency symbols** (₹, $, commas) from `TransactionAmount` and `AccountBalance` using regex, then converted to `float`
- Standardized **date columns** to proper `datetime` format using `pd.to_datetime()` with `%d-%m-%Y` format
- Validated and **standardized account types** and transaction categories (strip, lowercase)
- Ensured correct **data types** across all columns

---

## 📊 Task 2 — Descriptive Transactional Analysis

- Extracted **Transaction Year** and **Month** from date column
- Built **monthly and yearly pivot summaries** of credits, debits, and net transaction volume using `pivot_table()`
- Plotted **monthly credits vs. debits trend** using line charts
- Identified **top 5 and bottom 5 accounts** by net inflow using a custom credit/debit classifier
- Flagged **dormant/inactive accounts** — accounts with a gap of 60+ days between consecutive transactions using `shift()` and date differencing

**Key Findings:**
- Credit transactions drive **58–62%** of total money flow; debits are more frequent but smaller
- Roughly **18–22% of accounts are dormant**, showing low customer engagement
- Dormant accounts that reactivate tend to show **riskier transaction patterns**

---

## 👤 Task 3 — Customer Profile Building

**Activity Level Segmentation (Rubric: Average Monthly Transactions)**

| Activity Level | Criteria |
|---|---|
| High Activity | Avg monthly transactions ≥ 2 |
| Medium Activity | Avg monthly transactions ≥ 1 |
| Low Activity | Avg monthly transactions < 1 |

**Balance & Volume Segmentation**

| Segment | Criteria |
|---|---|
| High Balance | Avg balance ≥ ₹1,00,000 |
| Medium Balance | Avg balance ≥ ₹40,000 |
| Low Balance | Avg balance < ₹40,000 |
| High Volume | Transaction count ≥ 9 |
| Medium Volume | Transaction count ≥ 5 |
| Low Volume | Transaction count < 5 |

**Customer Profiles Created:**
- **High Net Inflow Accounts** — `NetInflow > ₹50,000`
- **High-Frequency Low-Balance Accounts** — `TxnCount ≥ 9` and `AccountBalance ≤ ₹40,000`
- **Zero or Near-Zero Balance Accounts** — `AccountBalance ≤ ₹1,000`

**Key Findings:**
- ~25% of accounts are high-activity, 50% medium, 25% low engagement
- ~30% of customers show **high transaction frequency with consistently low balances** — a potential indicator of financial stress
- Transaction volume alone is not a reliable indicator of financial stability — **balance behavior is a stronger risk predictor**

---

## ⚠️ Task 4 — Financial Risk Identification

- Tracked accounts with **frequent large withdrawals** (amount ≥ ₹50,000, count ≥ 5)
- Identified **overdraft accounts** (balance < 0) and flagged frequent overdrafters (≥ 2 occurrences)
- Calculated **balance volatility** using Standard Deviation and Coefficient of Variation (CV = SD / Mean)
- Detected transaction anomalies using two methods:
  - **IQR Method** — flagged values outside `Q1 - 1.5×IQR` and `Q3 + 1.5×IQR`
  - **Z-Score Method** — flagged transactions with `|Z-score| > 2`
- Built a **Suspicious Score** for each customer combining 5 risk signals:

```python
SuspiciousScore = Z_Anomaly + IQR_Anomaly + LargeWithdrawal
                + FrequentWithdrawals + HighBalanceVolatility
```

**Key Findings:**
- **6–9% of all transactions** show anomalous patterns — significant irregular activity
- High-risk accounts have balance volatility **2–3x higher** than normal
- **Dormant accounts that reactivate** show the highest concentration of anomalies — most vulnerable to fraud
- Combination of irregular transactions + volatile balances is strongly correlated with financial risk

---

## 📈 Task 5 — Exploratory Data Analysis (EDA)

Comprehensive visualizations built using `matplotlib`:

| Chart | Insight |
|---|---|
| Bar chart — Missing Values per Column | Data quality overview |
| Histogram — Transaction Amount Distribution | Skewness and outlier presence |
| Histogram — Account Balance Distribution | Balance spread across customers |
| Bar chart — Transaction Type Counts | Credit vs Debit frequency |
| Bar chart — Account Type Distribution | Account type breakdown |
| Bar chart — Region-wise Distribution | Geographic customer spread |
| Line chart — Daily Transaction Volume | Time-series trend analysis |
| Correlation Heatmap | Relationships between numeric variables |
| Box plot — Transaction Amount Outliers | Labeled outlier detection |
| Bar chart — Customer Segments by Balance | Low / Medium / High balance distribution |
| Histogram — Suspicious Score Distribution | Risk score spread across customers |

---

## 🧪 Task 6 — Hypothesis Testing

**Test 1: Do high-volume transaction accounts have statistically higher average balances?**

- Split customers into **High** and **Low** volume groups based on median total transaction amount
- Applied **Welch's Independent T-Test** (`equal_var=False`)

```python
t_stat, p_value = ttest_ind(high_balances, low_balances, equal_var=False)
```

**Test 2: Hypothesis testing based on Balance Segmentation**
- Compared TransactionAmount between **High Balance** and **Low Balance** customer segments

**Test 3: Risk Score-based segmentation**
- Compared TransactionAmount between **High Risk** and **Low Risk** groups using median `RiskScore` split

**Key Finding:**
- **p-value < 0.05** across tests — results are statistically significant
- Accounts with **high transaction volumes have noticeably higher average balances**
- Strong statistical evidence that **more transaction activity is positively linked to higher account balances**

---

## 💡 Key Recommendations

**1. Risk-Based Monitoring Framework**
Implement real-time alerts for key risk indicators — balance volatility, large withdrawals, and dormant account reactivation — to reduce fraud and financial losses.

**2. Customer Segmentation for Targeted Monitoring**
Segment customers into Low, Medium, and High risk groups to allow targeted monitoring while minimizing friction for stable, low-risk users.

**3. Personalized Engagement**
- **Low-risk, high-value customers** → Tailored financial tools and incentive programs
- **Medium-risk customers** → Spending alerts and financial guidance

**4. Early Warning System**
Use Z-Score and IQR-based anomaly detection to flag unusual behavior early, enabling preventive action before risks escalate.

---

## 📁 Project Files

```
📦 Python Project
 ┣ 📓 Python scripting.ipynb     # Full Jupyter Notebook with all 6 tasks
 ┣ 📄 Python.pdf                 # Task-wise findings and key insights
 ┗ 🔗 Video link.pdf             # Video walkthrough links
```

---

## 🚀 How to Run

1. Clone this repository
2. Install required libraries:
   ```bash
   pip install pandas numpy matplotlib scipy
   ```
3. Place the `goldman_sachs.csv` dataset in the same directory
4. Open `Python scripting.ipynb` in **Jupyter Notebook** or **VS Code**
5. Run all cells from top to bottom (Task 1 → Task 6)

---

## 🎥 Project Walkthrough

Two-part video walkthrough available here:
- 🔗 [Video 1 — Watch on Loom](https://www.loom.com/share/91dd2976d4f44364b13901868b0f0a45)
- 🔗 [Video 2 — Watch on Loom](https://www.loom.com/share/1b9ad40413a2410789eec7a51cd4eed1)

---

## 👤 Author

### Tridev Pal
📍 Calcutta, West Bengal, India

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?logo=linkedin)](https://www.linkedin.com/in/tridev-pal-74575a379/)

> Feel free to connect or raise issues via GitHub if you have suggestions or improvements!
