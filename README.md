# 📊 Customer Complaints Data Analysis Dashboard

## 🚀 Introduction
Welcome to the **Customer Complaints Analysis** project. This project turns raw, disconnected customer feedback from Excel logs into a dynamic, highly interactive Power BI business intelligence dashboard. 

The goal of this analysis is to identify product quality flaws, evaluate internal investigation performance, analyze customer sentiment, and highlight areas where operational updates can improve brand loyalty.

All visual assets, model structures, and data flows are documented directly below.

---

## 📋 Project Background
In consumer goods and retail, quick resolution times and quality control are critical. This dashboard was developed to solve a core business problem: **How do we efficiently track, triage, and solve growing customer issues?**

By building a centralized semantic model and interactive dashboard, this project answers five critical business questions:
1. **Which specific products** trigger the highest volume of consumer complaints?
2. **What are the most common complaint types** and how do they correlate with customer dissatisfaction?
3. **How severe** are incoming tickets, and what percentage remain unaddressed (`Open` status)?
4. **Are we meeting SLA targets** for resolving issues across different product categories?
5. **What underlying keywords and sentiment shifts** are driven by consumer remarks?

---

## 🛠️ Tools & Tech Stack
To build the end-to-end business intelligence pipeline, I used the following tools:
* **Excel:** The foundational source data container storing raw complaint entries, dates, remarks, and satisfaction ratings.
* **Power Query:** Utilized to clean text columns, manage data types, and filter out null values.
* **Power BI Desktop & DAX:** Used to engineer an optimized star-schema data model and construct custom advanced business logic metrics.
* **AI Cognitive Services (Sentiment Analysis):** Leveraged Power BI's native AI insights to extract core sentiment metrics and key phrases from text logs.
* **Word Cloud Custom Visual:** Integrated to visually highlight high-frequency complaint phrases directly on the canvas.

---

## 🗂️ Data Architecture & Modeling
To maximize performance and query efficiency, I transformed the flat Excel table into an optimized **Star Schema** data model. 

### Data Model Layout
![Data Model Schema](assets/data_model_screenshot.png)

### Schema Relationships
* `Complaints` (Fact Table) ─── `1:*` ─── `DateTable` (Dimension Table)
* `Complaints` (Fact Table) ─── `1:*` ─── `SatisfactionSortOrder` (Dimension Table)

### Custom Structural Tables
To achieve accurate calendar drill-downs and custom sorting behaviors, the following DAX calculated tables were implemented:

* **Date Table (`DateTable`):** Automatically bounds itself to the minimum complaint date and maximum resolution date to ensure continuous timeline reporting.
  ```dax
  DateTable = 
  ADDCOLUMNS(
      CALENDAR(
          MIN('Complaints'[Complaint Date]),
          MAX('Complaints'[Resolution Date])
      ),
      "Year", YEAR([Date]),
      "Month", FORMAT([Date], "MMMM"),
      "Quarter", "Q" & QUARTER([Date]),
      "Weekday", FORMAT([Date], "dddd")
  )
  ```

* **Satisfaction Sort Order (`SatisfactionSortOrder`):** Fixes the default alphabetical sorting flaw in native charts by mapping strings to explicit indexes.
  ```dax
  SatisfactionSortOrder = 
  DATATABLE(
      "Satisfaction Rating", STRING,
      "SortOrder", INTEGER,
      {
          {"Very Satisfied", 1},
          {"Satisfied", 2},
          {"Neutral", 3},
          {"Dissatisfied", 4},
          {"Very Dissatisfied", 5}
      }
  )
  ```

---

## 📐 DAX Measures Reference
All business logic functions are neatly contained within a dedicated **`Measure Table`** for clean organization.

| Measure Name | DAX Code / Expression | Description |
| :--- | :--- | :--- |
| **Total Complaints** | `COUNTROWS('Complaints')` | Calculates absolute volume of consumer issues logged. |
| **Open Complaints** | `CALCULATE(COUNTROWS('Complaints'), 'Complaints'[Investigation Status] = "Open")` | Tracks active, unresolved tickets requiring immediate attention. |
| **Complaints %** | `VAR _Complaints = CALCULATE([Total Complaints], ALL(Complaints)) RETURN DIVIDE([Total Complaints], _Complaints)` | Computes percentage distribution across dynamically filtered categories. |
| **Avg Satisfaction Score** | `AVERAGE('Complaints'[SatisfactionScore])` | Evaluates overall post-resolution customer experience (Scale 1-5). |
| **Avg Days to Resolve** | `AVERAGEX(FILTER('Complaints', NOT ISBLANK('Complaints'[Resolution Date])), DATEDIFF('Complaints'[Complaint Date], 'Complaints'[Resolution Date], DAY))` | Tracks operational speed by calculating turnaround time for closed tickets. |
| **Avg Shelf Life (Days)** | `AVERAGEX(SUMMARIZE('Complaints', 'Complaints'[Product Name], "ShelfLife", AVERAGEX(FILTER('Complaints', 'Complaints'[Manufacturing Date] <> BLANK() && 'Complaints'[Expiration Date] <> BLANK()), DATEDIFF('Complaints'[Manufacturing Date], 'Complaints'[Expiration Date], DAY))), [ShelfLife])` | Advanced metric analyzing the lifecycle gap between manufacturing and expiration dates per item. |
| **Severity** | `SELECTEDVALUE(Complaints[Severity Level])` | Dynamically captures and returns the current user-selected context for filter flags. |

---

## 📊 Dashboard Visual Insights

![Customer Complaints Dashboard Preview](assets/dashboard_screenshot.png)

### Executive Summary KPI Overview
* **Total Intake Volume:** **300** total customer complaints were evaluated across the operational window.
* **Active Operational Backlog:** **86** complaints are currently flagged with an active `Open` status, representing a key area for resource allocation.
* **Turnaround Efficiency:** The organization maintains a solid baseline, taking an average of **8 days** to officially resolve a customer issue.
* **Quality Control Baseline:** The average shelf life for cataloged products sits at **38 days**.

### Core Analytical Deep Dives
1. **Product Performance Risks:** The **Hummus & Veggies Pack** sits as the most volatile item, accounting for **36** independent complaints. It is followed closely by the **Vegan Wrap** (**29**) and **Greek Salad** (**28**).
2. **Severity Log Distribution:** Issue categorization shows that **High** priority tickets form the largest single block at **35%** (**104** complaints). **Critical** issues require immediate attention, accounting for **19%** of absolute logs.
3. **Sentiment & Text Mining:** By applying text processing models, the canvas dynamically visualizes sentiment mapping. **Negative sentiment** captures the heavy majority at **48%** (**143** records). Key phrase extraction highlights repeating operational failure points such as `"vacuum seal"`, `"product freshness"`, and `"spoilage"`.
4. **Dissatisfaction Drivers:** A combination chart breaks down complaint types cross-referenced with customer ratings. **Allergic reactions** and **Foreign objects in food** predictably map to the highest concentration of `Very Dissatisfied` and `Dissatisfied` responses.

---

## 💡 Key Conclusions & Action Items
* **Fix the Packaging Seals:** Key phrase analysis heavily indicates that packaging failures are creating systemic food spoilage issues early. Engineering updates should prioritize the sealing mechanics of the wrap and sandwich lines.
* **Triage the Backlog:** With **86 open cases**, operations should implement automated routing rules targeting the **Critical / High** severity issues impacting high-volume products like the Hummus Pack.
* **Supplier Quality Check:** Allergic reactions and foreign object complaints represent severe regulatory liabilities. Immediate audits are recommended for raw ingredient suppliers, specifically targeting leaf greens and proteins.


