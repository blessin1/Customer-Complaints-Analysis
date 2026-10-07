# 📊 Customer Complaints Data Analysis Dashboard

## 🚀 Introduction
This project turns raw, disconnected customer feedback from Excel logs into a dynamic, highly interactive Power BI business intelligence dashboard. 

The goal of this analysis is to identify product quality flaws, evaluate internal investigation performance, analyze customer sentiment, and highlight areas where operational updates can improve brand loyalty.


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
![Data Model Schema](data_model_screenshot.png)

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
## 🧠 Text Analytics

A key part of this project was combining **structured complaint information with unstructured customer feedback**.

Power BI's sentiment analysis capabilities were used to analyse complaint descriptions and identify sentiment patterns.

Key phrases were also extracted from complaint text and incorporated into a **word cloud** to surface recurring themes.

The analytical workflow was:

```text
Complaint Description
        ↓
Sentiment Analysis
        ↓
Key Phrase Extraction
        ↓
Sentiment Visual + Word Cloud
        ↓
Customer Experience Insights
```

This provides an additional layer of analysis beyond simply counting complaints.

---
## 📊 Key Insights

### Complaint Volume

The dataset contains **300 customer complaints**, providing the basis for the dashboard's product, operational, satisfaction, and sentiment analysis.

### Open Complaint Backlog

**86 complaints** are currently marked as `Open`, highlighting the volume of cases that remain unresolved within the dataset.

### Resolution Time

The average resolution time is **8 days** for complaints with a recorded resolution date.

### Product Complaint Concentration

The products with the highest complaint volumes include:

- **Hummus & Veggies Pack** — 36 complaints
- **Vegan Wrap** — 29 complaints
- **Greek Salad** — 28 complaints

These products provide useful starting points for deeper quality and customer-experience investigation.

### Severity

**High severity** complaints account for **104 cases**, approximately **35%** of the dataset.

**Critical** complaints account for approximately **19%** of the total complaint volume.

### Customer Sentiment

Negative sentiment represents the largest sentiment category, with **143 complaints**, approximately **48%** of the dataset.

### Recurring Complaint Themes

Key phrase analysis highlights recurring terms such as:

- `vacuum seal`
- `product freshness`
- `spoilage`

These recurring themes can be used as signals for further investigation into packaging, freshness, and product-quality processes.

### Dissatisfaction Drivers

Complaint categories including **allergic reactions** and **foreign objects in food** show high concentrations of dissatisfied customer responses.

These categories can be examined further alongside severity, investigation status, product, and supplier information.

---

## 💡 Recommendations

The dashboard findings suggest several areas for further investigation:

### 1. Investigate Packaging-Related Complaints

Recurring phrases around vacuum sealing, freshness, and spoilage suggest that packaging-related complaints deserve additional quality-control investigation.

### 2. Review the Open Complaint Backlog

The **86 open complaints** could be further prioritised using severity, complaint age, product, and sentiment.

### 3. Investigate High-Risk Complaint Categories

Allergic reactions and foreign-object complaints should be reviewed alongside severity and investigation status because of their potential quality and food-safety implications.

### 4. Monitor High-Volume Products

Products with consistently high complaint volumes can be monitored over time to determine whether the pattern is isolated or recurring.

---
