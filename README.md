# India-UPI-Transaction-Intelligence-Fraud-Risk-Analysis

-----

## Business Problem

India's UPI ecosystem processes billions of transactions monthly across banks, devices, states, and merchant categories. As digital payment volumes grow, fintech companies face critical operational challenges — fraud detection, transaction failures, network reliability, and understanding customer behavior across demographics.

This project addresses those challenges by analyzing transaction-level UPI data to answer real business questions that payment companies, banks, and fraud teams face every day.

-----

## Project Overview

| Detail | Info |
|---|---|
| Domain | Fintech / Digital Payments |
| Focus | Fraud Analytics + Payment Intelligence |
| Geography | India (10 major states) |
| Dataset | UPI Transaction Records |
| Tools | Excel, SQL, Python, Power BI |

-----

## Business Questions Solved
### Transaction Analysis
- What are the peak transaction hours and how should infrastructure scale around them?
- Which days drive the most payment volume — weekdays or weekends?
- What is the average transaction value across the platform?

### Payment Analysis
- Which merchant categories generate maximum transactions?
- Which banks process the highest UPI transaction volumes?
- Which states contribute most to India's digital payment activity?

### Fraud Analysis
- Which merchant categories carry the highest fraud risk?
- Does fraud rate vary significantly by device type or network?
- Are failed transactions correlated with fraudulent activity?

### Technology Analysis
- Which device platforms dominate UPI usage?
- Does network type (3G / 4G / 5G / WiFi) impact transaction success rates?
- How does mobile payment behavior shift between weekdays and weekends?

-----
## Key Business Findings

### Payment Volume & Value
The platform processed over **₹327.9 million** in total transaction value across **250,000+ transactions**, with an average transaction value of **₹1,312**. Maharashtra leads all states in both transaction volume (~37,000 transactions) and total UPI amount, followed by Uttar Pradesh and Karnataka.

### Payment Success & Failure
The overall payment success rate stands at **95.05%**, with a failure rate of approximately **6%**. The majority of failures are concentrated on 4G networks, which also carry the highest transaction share — pointing to infrastructure bottlenecks during peak load rather than device or user-side issues.

### Fraud Intelligence
**480 fraudulent transactions** were detected, representing a fraud rate of **0.015%**. Grocery and Food merchant categories account for the highest fraud cases (~96 and ~74 respectively), likely driven by their high transaction frequency. Fraud is disproportionately concentrated on Android devices (~362 cases), which reflects the platform's dominant user base but also signals a need for stronger Android-side transaction monitoring.

### Peak Hours & Timing
Transaction volume peaks at **19:00 (7 PM)** with 21,232 transactions recorded at that hour — the single busiest period across the entire dataset. Activity remains elevated from 17:00 to 20:00, creating a clear evening rush window. Weekday and weekend volumes are nearly identical (1,312 vs 1,310), indicating that UPI usage has become a consistent daily habit rather than a discretionary weekend activity.

### Banking Distribution
**SBI dominates** sender bank activity with ~63,000 transactions, nearly 1.7× more than HDFC (~38,000) and more than double ICICI (~30,000). IndusInd, Axis, PNB, and Yes Bank cluster tightly between 24,000–26,000, suggesting competitive parity among mid-tier banks on UPI.

### Customer Demographics
The **26–35 age group** is the largest UPI user segment (87,432 users), followed by 36–45 (62,870) and 18–25 (62,349). Users above 46 represent a smaller but growing share, indicating UPI adoption is expanding beyond its original young-urban demographic.

-----

## Dashboard

The Excel dashboard provides a single-screen view of the entire analysis with interactive slicers for:
- Sender state
- Weekday / Weekend
- Device type (Android / iOS / Web)
- Network type (3G / 4G / 5G / WiFi)
- Transaction status (Success / Failed)

  **Dashboard sections:**
- KPI row — Total Transaction Value, Avg Value, Total Transactions, Successful Transactions, Fraud Transactions, Top Sender Bank
- Transaction Volume by State (bar chart)
- Merchant Fraud Analysis (horizontal bar — color coded by risk tier)
- Hourly Transaction Trends (line chart with peak annotation)
- Payment Success Rate (donut chart)
- Customer Age Group Distribution (donut chart)
- UPI Transaction Value by State (India choropleth map)
- Weekend vs Weekday Transactions (KPI comparison)
- Top Sender Banks (horizontal bar)
- Network Type — Success vs Failed (stacked bar)
- Fraud by Device Platform (horizontal bar)

  -----
  ## Project Structure

```
UPI_Transaction_Intelligence/
│
├── data/
│   └── upi_transactions.csv
│
├── excel/
│   └── upi_dashboard.xlsx
│
├── sql/
│   └── upi_queries.sql
│
├── python/
│   └── eda.ipynb
│
├── powerbi/
│   └── upi_dashboard.pbix
│
├── reports/
│   └── project_summary.pdf
│
└── README.md

-----

## SQL Analysis (Key Queries)

```sql
-- Total transactions and value
SELECT COUNT(*) AS total_transactions,
       SUM(amount) AS total_value,
       AVG(amount) AS avg_value
FROM upi_transactions;

-- Peak transaction hours
SELECT HOUR(transaction_time) AS hour,
       COUNT(*) AS transaction_count
FROM upi_transactions
GROUP BY hour
ORDER BY transaction_count DESC;

-- Fraud by merchant category
SELECT merchant_category,
       COUNT(*) AS fraud_cases,
       ROUND(COUNT(*) * 100.0 / (SELECT COUNT(*) FROM upi_transactions), 4) AS fraud_rate
FROM upi_transactions
WHERE fraud_flag = 1
GROUP BY merchant_category
ORDER BY fraud_cases DESC;

-- State-wise transaction volume
SELECT sender_state,
       COUNT(*) AS total_transactions,
       SUM(amount) AS total_value
FROM upi_transactions
GROUP BY sender_state
ORDER BY total_value DESC;

-- Bank performance
SELECT sender_bank,
       COUNT(*) AS total_transactions,
       SUM(CASE WHEN transaction_status = 'SUCCESS' THEN 1 ELSE 0 END) AS successful,
       ROUND(AVG(CASE WHEN transaction_status = 'SUCCESS' THEN 1.0 ELSE 0 END) * 100, 2) AS success_rate
FROM upi_transactions
GROUP BY sender_bank
ORDER BY total_transactions DESC;

-- Network type vs success rate
SELECT network_type,
       transaction_status,
       COUNT(*) AS count
FROM upi_transactions
GROUP BY network_type, transaction_status
ORDER BY network_type;
```

---

## Python EDA Highlights

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

df = pd.read_csv("upi_transactions.csv")

# Feature engineering
df['hour'] = pd.to_datetime(df['transaction_time']).dt.hour
df['day_type'] = pd.to_datetime(df['transaction_time']).dt.dayofweek.apply(
    lambda x: 'Weekend' if x >= 5 else 'Weekday'
)
df['fraud_rate'] = df.groupby('merchant_category')['fraud_flag'].transform('mean')
```

Charts produced: hourly trend, merchant fraud distribution, fraud vs non-fraud countplot, state heatmap, bank volume bar, device usage pie, weekend vs weekday boxplot, network stacked chart, correlation heatmap.

---

## Business Recommendations

**1. Fraud Monitoring** — Deploy real-time fraud scoring for Grocery and Food category transactions, particularly on Android. These two categories alone account for ~35% of all detected fraud cases.

**2. Peak Hour Infrastructure** — Scale payment gateway capacity between 17:00–20:00 daily. The 19:00 peak (21,232 transactions) represents a ~55% surge above the daily average.

**3. Network Reliability** — Investigate 4G failure patterns. Despite being the dominant network, 4G carries a disproportionate share of failed transactions, suggesting load-balancing or timeout configuration issues during peak hours.

**4. Bank Partnerships** — SBI, HDFC, and ICICI collectively process ~50% of all transactions. Priority integration and API reliability agreements with these three banks would protect the majority of platform volume.

**5. Age Segment Strategy** — The 26–35 cohort (largest segment) and the growing 46–55 cohort represent different UX and trust requirements. Tailoring onboarding and fraud alert communication by age segment would improve both adoption and retention.

--

## Skills Demonstrated

- Data cleaning and preparation
- Business problem framing
- KPI definition and metric design
- SQL querying and aggregation
- Exploratory data analysis
- Dashboard design and layout
- Fraud pattern analysis
- Geographic data visualization
- Business storytelling from data

---

## Author

**Bhuvaneshwari L**  
Aspiring Data Analyst | Fintech & Business Intelligence  
[www.linkedin.com/in/bhuvaneshwaril}


## Disclaimer

The dataset used in this project is publicly available on Kaggle
for educational and portfolio purposes. All analysis is performed
for learning and demonstration only.

---

*This project was built as a portfolio demonstration of end-to-end data analytics on real-world fintech data.*




