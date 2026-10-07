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
![Optimized Star Schema Model](assets/data_model_screenshot.png)

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
### 📊 Executive Summary KPI Overview
* **Total Intake Volume:** **300** total customer complaints evaluated across the active operational window.
* **Active Operational Backlog:** **86** complaints currently flag an active `Open` status, highlighting a critical resource bottleneck.
* **Turnaround Efficiency:** The organization maintains a baseline average of **8 days** to officially resolve an incoming ticket.
* **Quality Baseline:** The historical average shelf life for cataloged portfolio products sits at **38 days**.

---

### 🔍 Core Analytical Deep Dives

| Focus Area | Key Metrics & Data Assertions | Systemic Drivers |
| :--- | :--- | :--- |
| **Product Risks** | **Hummus Pack** (**36** logs), **Vegan Wrap** (**29**), **Greek Salad** (**28**). | These top 3 SKUs drive the bulk of total feedback. |
| **Severity Splitting** | **High Priority** tickets make up **35%** (**104** cases); **Critical** demands **19%**. | Over half of the backlog requires expedited handling. |
| **Text Mining** | **Negative sentiment** dominates at **48%** (**143** records). | Recurrent terms: `"vacuum seal"`, `"product freshness"`, `"spoilage"`. |
| **Risk Anchors** | **Allergic Reactions** & **Foreign Objects** generate the lowest user ratings. | Severe regulatory and liability vectors. |

---

## 💡 Key Conclusions & Action Items
* **Fix the Packaging Seals:** Text mining heavily indicates that early food spoilage stems directly from sealing mechanics. Engineering updates must immediately prioritize packaging integrity on the wrap and sandwich production lines.
* **Triage the Backlog:** Operations should deploy automated routing rules to isolate the **86 open cases**, immediately prioritizing **Critical / High** severity issues tied to high-volume products like the Hummus Pack.
* **Initiate Supplier Quality Audits:** Because foreign object and allergen complaints present severe regulatory liabilities, immediate on-site quality control checks are recommended for raw ingredient suppliers, specifically targeting leaf greens and proteins.
