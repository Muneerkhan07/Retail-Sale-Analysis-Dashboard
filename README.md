# 🛍️ Retail Sales Performance Dashboard

### 📊 Complete Data Analytics Project using Python, SQL & Power BI

---

## 📖 Project Overview
The **Retail Sales Performance Dashboard** is a complete end-to-end **data analytics project** designed to evaluate the sales and profit performance of a retail business.  
The goal is to derive key insights such as top-performing regions, profitable categories, loyal customers, and the correlation between sales and profit.

This project demonstrates the full analytics workflow — from raw data processing in **Python**, database management in **SQL**, and business intelligence visualization in **Power BI**.

---

## 🎯 Objective
To analyze retail sales data and uncover insights on business performance, profitability, and customer trends using a combination of data engineering and visualization tools.

---

## ⚙️ Tech Stack
| Tool / Technology | Purpose |
|--------------------|----------|
| **Python (Pandas, SQLAlchemy)** | Data cleaning, transformation & loading |
| **SQLite / MySQL** | Data storage & SQL querying |
| **Power BI** | Dashboard creation & visualization |
| **CSV (Retail_Sales.csv)** | Raw data source |

---
## 📊 Dashboard KPIs

| Metric | Description | Value |
|--------|--------------|-------|
| 💰 **Total Sales** | Overall revenue generated | **₹124.5M** |
| 📈 **Total Profit** | Net profit earned after cost | **₹18.6M** |
| 💹 **Avg Profit Margin %** | Ratio of profit to total sales | **14.9%** |
| 🔗 **Sales–Profit Correlation** | Indicates strength of relationship | **0.86 (Strong Positive)** |


---

## 🧩 Project Workflow

### 🔹 Step 1: Data Preparation (Python)
- Loaded raw dataset `retail_sales.csv`
- Cleaned null values and formatted the `Date` column
- Derived new columns:  
  - `Month` (month name extracted from date)  
  - `Profit_Percentage` = (Profit / Sales) * 100
- Connected Python to SQL database via SQLAlchemy
- Exported cleaned data into `retail_sales.db`


python
import pandas as pd
from sqlalchemy import create_engine

# Load dataset
df = pd.read_csv('retail_sales.csv')

# Clean and preprocess
df['Date'] = pd.to_datetime(df['Date'])
df['Month'] = df['Date'].dt.month_name()
df['Profit_Percentage'] = (df['Profit'] / df['Sales']) * 100

# Load to SQL
engine = create_engine('sqlite:///retail_sales.db')
df.to_sql('sales_data', con=engine, if_exists='replace', index=False)


### 🔹 Step 2: SQL Analysis (SQL)
-Performed SQL queries on SQLite database to generate summarized data views.
#SQL Query
-- Total Sales per Region
SELECT Region, SUM(Sales) AS Total_Sales 
FROM sales_data 
GROUP BY Region;

-- Top 5 Product Categories
SELECT Category, SUM(Sales) AS Total_Sales 
FROM sales_data 
GROUP BY Category 
ORDER BY Total_Sales DESC 
LIMIT 5;

-- Monthly Profit Trend
SELECT Month, SUM(Profit) AS Total_Profit 
FROM sales_data 
GROUP BY Month;

-- Top 5 Customers
SELECT Customer_Name, SUM(Sales) AS Total_Sales, SUM(Profit) AS Total_Profit
FROM sales_data
GROUP BY Customer_Name
ORDER BY Total_Sales DESC 
LIMIT 5;

### 🔹 Step 3: DAX Functions Used (Power Bi)
-Connected Power BI to the SQL database (retail_sales.db)
and built an interactive dashboard for analysis.
-DAX Functions used
-- Total Sales
Total Sales = SUM('sales_data'[Sales])

-- Total Profit
Total Profit = SUM('sales_data'[Profit])

-- Profit Percentage
Profit % = DIVIDE(SUM('sales_data'[Profit]), SUM('sales_data'[Sales])) * 100

-- Average Profit Margin %
Avg Profit Margin % = AVERAGE('sales_data'[Profit_Percentage])

-- Category Rank
Category Rank =
RANKX(
    ALL('sales_data'[Category]),
    [Total Sales],
    ,
    DESC,
    DENSE
)

-- Top 5 Category Flag
Top 5 Category Flag = IF([Category Rank] <= 5, 1, 0)

-- Customer Rank
Customer Rank =
RANKX(
    ALL('sales_data'[Customer_Name]),
    [Total Sales],
    ,
    DESC,
    DENSE
)

-- Top 5 Customer Flag
Top 5 Customer Flag = IF([Customer Rank] <= 5, 1, 0)

-- Sales-Profit Correlation
Sales_Profit_Correlation =
VAR AvgX = AVERAGE('sales_data'[Sales])
VAR AvgY = AVERAGE('sales_data'[Profit])
VAR Num =
    SUMX('sales_data',
        ('sales_data'[Sales] - AvgX) *
        ('sales_data'[Profit] - AvgY)
    )
VAR Den =
    SQRT(
        SUMX('sales_data', ('sales_data'[Sales] - AvgX)^2) *
        SUMX('sales_data', ('sales_data'[Profit] - AvgY)^2)
    )
RETURN
ROUND(DIVIDE(Num, Den), 2)


## 📸 Dashboard Preview

**Retail Sales Performance Dashboard**:![Sales Dashboard ](https://github.com/user-attachments/assets/fb7a4ef0-436b-45b7-99f9-231f3ab82d90)



