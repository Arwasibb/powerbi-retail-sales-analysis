# 📊 Retail Sales & Campaign Performance Analytics Dashboard

An interactive, two-page Power BI executive solution built to analyze SAR 16M+ in retail transaction volume and evaluate the financial efficiency of seasonal marketing campaigns.

---

## 🎥 Interactive Dashboard Demo

https://github.com/user-attachments/assets/abe73ea6-9c46-487c-8c4a-cf463c18e52e

---

## 🎯 Business Context & Problem Statement
Retail organizations frequently run seasonal promotions, but tracking whether discount-driven revenue translates to true profitability is a major challenge. 

This project addresses this gap by delivering a two-tier reporting structure:
* **Executive Summary:** High-level strategic overview for C-suite decision-makers to track sales trends, profit margins, and campaign impact.
* **Transaction Audit (Details):** Granular operational log enabling line-item investigation across stores, invoice IDs, and specific product categories.

---

## 🛠️ Tech Stack & Key Features
* **Business Intelligence Tool:** Microsoft Power BI Desktop
* **Data Modeling:** Star Schema architecture connecting transaction data with dimension lookup tables.
* **DAX Engineering:** Dynamic context switching using `SWITCH`, `KEEPFILTERS`, and `ALL` for percentage benchmarking.
* **Data Sources:** Full-year 2025 Saudi Retail Sales Dataset (16M+ SAR Net Sales).

---

## 📐 Data Architecture (Star Schema)
The underlying dataset is modeled using a clean **Star Schema** to ensure fast query performance and seamless dynamic filtering:
* **`Fact_Sales`**: Central transactional table storing Net Sales, Gross Sales, Net Profit, Invoice IDs, and Campaign types.
* **`Dim_Date`**: Calendar dimension table enabling month-over-month trend analysis and promotional window tagging.

---

## 💡 Key DAX Logic: Multi-Filter Margin Benchmark
To calculate the true profit margin contribution when filtering by **Campaign**, **Month**, or **Both** simultaneously against the full-year baseline, the following dynamic measure was engineered:

```dax
Profit Margin (switch) = 
VAR CurrentFilteredProfit = SUM(Fact_Sales[Net_Profit_SAR])

-- Calculates full-year baseline profit by stripping campaign and calendar filters
VAR TotalOverallYearProfit = 
    CALCULATE(
        SUM(Fact_Sales[Net_Profit_SAR]), 
        ALL(Fact_Sales[Campaign_Type]),
        ALL(Dim_Date[Month]),
        ALL(Dim_Date[Month_Num])
    )

RETURN
    SWITCH(
        TRUE(),
        -- Evaluates active slicers for campaign or month context
        ISFILTERED(Fact_Sales[Campaign_Type]) || ISFILTERED(Dim_Date[Month]) || ISFILTERED(Dim_Date[Month_Num]),
        DIVIDE(CurrentFilteredProfit, TotalOverallYearProfit, 0),

        -- Default full-year benchmark
        1
    )
