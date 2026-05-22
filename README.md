# 🛒 VyapaarSetu — E-Commerce Customer Behavior Analysis

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10-blue?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/Pandas-Data%20Analysis-150458?style=for-the-badge&logo=pandas&logoColor=white"/>
  <img src="https://img.shields.io/badge/Matplotlib-Visualization-11557c?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Seaborn-Visualization-4c8cbf?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Jupyter-Notebook-F37626?style=for-the-badge&logo=jupyter&logoColor=white"/>
  <img src="https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge"/>
</p>

---
   
## 🏢 About the Company           
     
**VyapaarSetu Pvt. Ltd.** *(व्यापारसेतु)* is a regional D2C e-commerce startup headquartered in **Indore, Madhya Pradesh**. Founded in January 2023, VyapaarSetu bridges local MP customers with quality products across 4 categories, delivering to 5 major MP cities.

| Detail | Info |     
|---|---|
| 📍 HQ | Indore, Madhya Pradesh |     
| 🗓️ Founded | January 2023 |
| 🛍️ Categories | Electronics, Fashion, Beauty, Sports |
| 🏙️ Cities Served | Indore, Bhopal, Jabalpur, Gwalior, Ujjain |
| 📦 Total Transactions | 21,345 |
| 👥 Unique Customers | 1,232 |

---

## 📌 Project Overview

This project performs an **end-to-end data analysis** on VyapaarSetu's FY 2023 transaction data using Python. The goal was to uncover customer behavior patterns, evaluate product and discount performance, and generate actionable business recommendations for the company's 2024 growth strategy.

> **"This is not just a coding exercise — every number here tells a business story."**

---

## 🎯 Objectives

- Clean and prepare real-world messy transaction data
- Analyze customer purchasing behavior across cities, age groups, and genders
- Evaluate product category and individual product performance
- Understand discount impact on revenue and quantity
- Identify high-value, medium-value, and low-value customer segments
- Analyze order sources and payment method trends
- Generate data-backed business recommendations

---

## 📁 Project Structure

```
VyapaarSetu-Analysis/
│
├── 📓 VyapaarSetu_Analysis.ipynb     ← Main Jupyter Notebook
├── 📄 vyapaarsetu_transactions.csv   ← Raw dataset (21,345 rows)
├── 📄 vyapaarsetu_clean.csv          ← Cleaned dataset
├── 📁 visuals/                       ← All saved charts (PNG)
└── 📄 README.md                      ← You are here
```

---

## 📊 Dataset Overview

| Parameter | Value |
|---|---|
| Total Rows | 21,345 |
| Total Columns | 24 |
| Unique Customers | 1,232 |
| Product Categories | 4 |
| Total Products | 28 |
| Cities | 5 MP Cities |
| Time Period | Jan 2023 – Dec 2023 |

### 🗑️ Data Quality Issues Handled

The raw dataset contained intentional real-world data quality problems:

| Issue | Rows Affected | Fix Applied |
|---|---|---|
| Duplicate rows | ~200 | `drop_duplicates()` |
| Mixed date formats (YYYY-MM-DD & DD/MM/YYYY) | ~150 | Custom date parser |
| Negative quantity values | ~80 | Removed invalid rows |
| Zero unit price | ~60 | Removed invalid rows |
| Rating values > 5.0 | ~100 | Set to `NaN` |
| Wrong return delivery charge | ~70 | Fixed by order status logic |
| Null `customer_name` | ~400 | Smart `ffill().bfill()` by `customer_id` |
| Null `product_name` | ~120 | Mapped from `product_id` lookup |
| Null `payment_method` | ~90 | City-wise mode fill |
| Null `city` | ~100 | Global mode fill |

---

## 🧹 Data Cleaning Highlights

### ⭐ Smart customer_name Recovery
Instead of blindly filling nulls, names were recovered by matching the same `customer_id` across other orders using forward and backward fill — only labeling truly nameless customers as `'Unknown Customer'`.

```python
df = df.sort_values(['customer_id', 'order_date'])
df['customer_name'] = df.groupby('customer_id')['customer_name']\
                        .transform(lambda x: x.ffill().bfill())
df['customer_name'] = df['customer_name'].fillna('Unknown Customer')
```

### ⭐ Smart product_name Recovery
Instead of dropping rows, product names were recovered using a `product_id → product_name` dictionary — resulting in **zero data loss**.

```python
product_map = df.dropna(subset=['product_name'])\
                .drop_duplicates('product_id')\
                .set_index('product_id')['product_name']\
                .to_dict()
df['product_name'] = df.apply(
    lambda row: product_map.get(row['product_id'], row['product_name'])
    if pd.isnull(row['product_name']) else row['product_name'], axis=1
)
```

### ⭐ City-wise Mode for payment_method
Payment behavior differs by city (UPI dominant in Indore, COD dominant in smaller cities) — so city-wise mode was used instead of global mode.

```python
df['payment_method'] = df.groupby('city')['payment_method']\
                         .transform(lambda x: x.fillna(x.mode()[0]))
```

---

## 📈 Key Findings & Insights

### 💰 Revenue
- Total platform revenue for FY 2023 reported in **Crores**
- **Electronics** (₹161.55L) and **Fashion** (₹155.31L) are the top revenue-generating categories
- **Sports** (₹77.15L) and **Beauty** (₹31.77L) contribute significantly less

### 🏙️ City Performance
- **Indore** leads in total transactions (35% share) — being the company HQ and commercial capital of MP
- **Bhopal** follows as the second largest market (30%)

### 👥 Customer Behavior
- **Middle-aged customers (31–50)** are the biggest revenue drivers (₹182.08L)
- **Top 20% of customers** contribute a disproportionately large share of total revenue — classic Pareto effect
- **Repeat customers** generate higher average order value compared to one-time buyers
- Customers with **declining spending** were identified for re-engagement campaigns

### 💳 Payment Trends
- **UPI** is the most popular payment method (6,981 transactions)
- **COD** dominates in smaller cities like Gwalior and Ujjain
- **Wallet** has the lowest adoption (2,402 transactions)

### 🏷️ Discount Analysis
- **1–10% discount range** is the sweet spot — highest customer engagement without margin erosion
- **20%+ discounts** do NOT increase quantity sold and significantly reduce revenue
- Customers spend **₹400+ more on average** when no discount is applied
- **"High discounts increase quantity but reduce overall revenue — indicating an inefficient pricing strategy"**

### 📅 Seasonal Trends
- **April, August, and December** are peak revenue months
- **July, September, and October** are the weakest performing months
- Clear **weekday vs weekend** pattern visible in order volumes

### 📦 Order Source
- **App** drives the highest revenue (₹143.41L) and most transactions (7,239)
- **Friend Referral** has the lowest volume but highest loyalty potential
- **Instagram & Facebook Ads** bring moderate revenue — need ROI evaluation

### ⭐ Product Complaints
- **Cotton Kurta** (303), **boAt Earphones** (276), and **Silk Saree** (229) have the highest complaint volumes
- Complaint-heavy products are strong candidates for quality review

---

## 🔍 Analysis Sections

| Section | Questions Answered |
|---|---|
| 📊 Basic EDA | Revenue overview, city performance, popular categories, demographics |
| 📅 Time Analysis | Monthly trends, seasonal patterns, weekday vs weekend |
| 🏷️ Discount Analysis | Discount effectiveness on quantity and revenue, optimal discount range |
| 👥 Customer Analysis | High/medium/low value segmentation, repeat vs one-time buyers, declining customers |
| 📦 Product Analysis | Category revenue, complaint analysis, gender-wise product preferences |
| 💳 Payment Analysis | Payment method usage, order status by payment method |
| 🌐 Source Analysis | Revenue and transactions by order source |

---

## 🛠️ Tools & Libraries

```python
import pandas as pd        # Data manipulation & cleaning
import numpy as np         # Numerical operations
import matplotlib.pyplot   # Data visualization
import seaborn as sns      # Statistical visualization
```

---

## 💡 Business Recommendations

Based on the analysis, here are 5 actionable recommendations for VyapaarSetu's 2024 strategy:

1. **🎯 Focus retention on Middle-aged segment** — They drive the most revenue; loyalty programs and personalized offers will maximize LTV
2. **🏷️ Cap discounts at 10%** — Higher discounts hurt revenue without meaningfully increasing volume; 1–10% is the optimal range
3. **📱 Invest more in App experience** — App drives 35%+ of all revenue; features like push notifications and app-exclusive deals will boost this further
4. **⚠️ Review Cotton Kurta & boAt Earphones quality** — Highest complaint volumes need immediate quality control attention
5. **🌆 Expand to Tier-2 cities** — COD infrastructure is already in place; targeted campaigns in Jabalpur and Gwalior can unlock new growth

---

## 🚀 How to Run

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/VyapaarSetu-Analysis.git

# 2. Navigate to project folder
cd VyapaarSetu-Analysis

# 3. Install required libraries
pip install pandas numpy matplotlib seaborn jupyter

# 4. Launch Jupyter Notebook
jupyter notebook VyapaarSetu_Analysis.ipynb
```

---

## 👤 Author

**Pushpkar Roy**    
Aspiring Data Analyst | SQL • Power BI • Excel • Python  

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/pushpkar-roy/)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=flat-square&logo=github)](https://www.linkedin.com/in/pushpkar-roy)

---

## 📜 License

This project is open source and available under the [MIT License](LICENSE).

---

<p align="center">
  <i>Built with ❤️ from Indore, Madhya Pradesh 🇮🇳</i>
</p>
