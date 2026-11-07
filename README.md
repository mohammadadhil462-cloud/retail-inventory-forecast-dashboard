# Retail Inventory & Forecast Dashboard (Power BI)

An interactive, three-page Power BI dashboard that analyses **73,100 daily retail inventory records (Jan 2022 – Jan 2024)** across 5 stores and 20 products. It answers three questions a retail operations team cares about: *how is revenue performing, how good is our demand forecast, and is our inventory healthy?*

![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-Measures-blue)
![Status](https://img.shields.io/badge/status-complete-brightgreen)

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Business Questions](#business-questions)
3. [Dataset](#dataset)
4. [Dashboard Pages](#dashboard-pages)
5. [Key Insights](#key-insights)
6. [KPIs & Measures](#kpis--measures)
7. [Interactivity](#interactivity)
8. [Design Notes](#design-notes)
9. [How to Open the Dashboard](#how-to-open-the-dashboard)
10. [Repository Structure](#repository-structure)
11. [Skills Demonstrated](#skills-demonstrated)
12. [Possible Next Steps](#possible-next-steps)
13. [Author](#author)

---

## Project Overview

Retailers lose money in two opposite ways: **stockouts** (lost sales) and **overstock** (tied-up cash). Both usually trace back to the demand forecast. This dashboard puts revenue, forecast quality and inventory health in one place so the connection between them is easy to see.

- **Tool:** Microsoft Power BI Desktop
- **Language:** DAX (calculated measures)
- **Data grain:** one row per store, per product, per day
- **Period covered:** January 2022 – January 2024
- **Scale:** 73,100 rows · 5 stores · 20 products · 4 regions · 5 product categories

## Business Questions

| # | Question | Answered on |
|---|----------|-------------|
| 1 | How much revenue are we generating, and how does it trend over time, by category and by region? | Overview |
| 2 | How accurate is the demand forecast, and does it systematically over- or under-predict? | Forecast Performance |
| 3 | How often are we at risk of stocking out or holding too much stock, and which stores are worst? | Inventory Health |

## Dataset

A single fact table, `retail_store_inventory`, loaded into the data model. Columns used by the report:

| Column | Description |
|--------|-------------|
| `Date` | Daily record date |
| `Year`, `Month name` | Date parts used for slicing and time-series axes |
| `Store ID` | Store identifier (S001 – S005) |
| `Product ID` | Product identifier (20 products) |
| `Category` | Product category |
| `Region` | Sales region |
| `Units Sold` | Actual units sold that day |
| `Demand Forecast` | Forecast units for that day |
| `Inventory Level` | Stock on hand |
| `Stock Status` | Derived flag: **Healthy**, **Overstock** or **Stockout Risk** |

> The raw data is not included in this repository. All data and measures are embedded in the `.pbix` file.

## Dashboard Pages

All pages are 1920 × 1080 with a shared **Year** slicer on the left.

### 1. Overview

*Headline: "Revenue of ~495M is spread evenly: each category ~20%, each region ~25%."*

| Visual | Type | Fields |
|--------|------|--------|
| Total Revenue | KPI card | `Total Revenue` |
| Total Units Sold | KPI card | `Total Units Sold` |
| Forecast Accuracy % | KPI card | `Forecast Accuracy %` |
| Stockout Rate % | KPI card | `Stockout Rate %` |
| Monthly Revenue Trend | Line chart | `Month name`, `Total Revenue` |
| Revenue by Category | Clustered bar | `Category`, `Total Revenue` |
| Revenue by Region | Clustered column | `Region`, `Total Revenue` |
| Year | Slicer | `Year` |

### 2. Forecast Performance

*Headline: "Forecast is ~94% accurate but over-predicts demand by about 4%."*

| Visual | Type | Fields |
|--------|------|--------|
| Total Forecast | KPI card | `Total Forecast` |
| Forecast Accuracy % | KPI card | `Forecast Accuracy %` |
| Forecast Bias % | KPI card | `Forecast Bias %` |
| Forecast VS Actual Units | Line chart | `Month name`, `Total Forecast`, `Total Units Sold` |
| Forecast Bias by Category | Clustered bar | `Category`, `Forecast Bias %` |
| Forecast vs Actual by Product | Scatter plot | `Demand Forecast` vs `Units Sold`, one point per `Product ID` |
| Forecast Accuracy by Category and Region | Matrix | `Category` × `Region`, `Forecast Accuracy %` |
| Year | Slicer | `Year` |

### 3. Inventory Health

*Headline: "Stock is lean at about 2 days of supply, and orders don't respond to demand or stock levels."*

| Visual | Type | Fields |
|--------|------|--------|
| Stockout Rate % | KPI card | `Stockout Rate %` |
| Overstock Rate % | KPI card | `Overstock Rate %` |
| Days Of Supply | KPI card | `Days Of Supply` |
| AVG inventory | KPI card | `AVG inventory` |
| Stock Status Split | Donut chart | `Stock Status` (Healthy / Overstock / Stockout Risk) |
| Inventory vs Orders Over Time | Line chart | `Month name`, `AVG inventory`, `AVG Units Ordered` |
| Stockout Rate % by Store | Clustered bar | `Store ID`, `Stockout Rate %` |
| Rows at Risk of Stockout | Table (filtered on `Stock Status`) | `Date`, `Store ID`, `Product ID`, `Inventory Level`, `Demand Forecast`, `Stock Status` |
| Year | Slicer | `Year` |

## Key Insights

These are the findings called out in the dashboard headlines.

- **Revenue is balanced.** About 495M in total, split roughly 20% per category and 25% per region, so no single category or region carries the business.
- **The forecast is good but biased.** Accuracy is about 94%, yet it over-predicts demand by about 4%, which tends to push inventory up.
- **Inventory is lean on average.** Around 2 days of supply on hand.
- **Stockout risk is limited.** Only 3.5% of records are flagged as at risk of a stockout.
- **Overstock is the bigger issue.** 47.9% of records hold more than twice the forecast demand.
- **Ordering looks disconnected from demand.** Order quantities don't visibly respond to demand or to stock levels, which points to a replenishment-policy opportunity rather than a forecasting one.

## KPIs & Measures

All measures live on the `retail_store_inventory` table.

| Measure | What it tells you |
|---------|-------------------|
| `Total Revenue` | Revenue for the current filter selection |
| `Total Units Sold` | Actual units sold |
| `Total Forecast` | Forecast units for the same selection |
| `Forecast Accuracy %` | How closely the forecast matches actual sales |
| `Forecast Bias %` | Direction and size of forecast error (positive = over-forecasting) |
| `Stockout Rate %` | Share of records flagged **Stockout Risk** |
| `Overstock Rate %` | Share of records flagged **Overstock** |
| `Days Of Supply` | How long current stock would last at the demand rate |
| `AVG inventory` | Average inventory level |
| `AVG Units Ordered` | Average units ordered |

To see the DAX behind each measure, open the file in Power BI Desktop and use **Model view** or **Report view → Data pane → expand `retail_store_inventory`**.

## Interactivity

- **Year slicer** on every page filters all visuals on that page.
- **Cross-filtering:** selecting a category, region, store or stock status in one visual filters the others on the page.
- **Tooltips:** enhanced tooltips are enabled across the report.
- **Data export:** summarized data export is allowed from visuals.

## Design Notes

- Canvas size **1920 × 1080**, fit-to-page.
- Dark gradient page backgrounds with white headline text and grey sub-headlines.
- Base theme: **Fluent 2** (Power BI built-in).
- Semantic colours on the Inventory page (green, orange and red) to separate healthy stock from risk states.
- Each page opens with an **insight-style headline** (the takeaway) and a one-line data caption, rather than a generic title.

## How to Open the Dashboard

1. Install **[Power BI Desktop](https://powerbi.microsoft.com/desktop/)** (free, Windows).
2. Download `Retail_Dashboard_v6_final.pbix` from this repository.
3. Double-click the file to open it. The data is embedded, so no refresh or credentials are required.
4. Use the page tabs at the bottom to move between **Overview**, **Forecast Performance** and **Inventory Health**.

## Repository Structure

```
.
├── Retail_Dashboard_v6_final.pbix   # The Power BI report (data + model + visuals)
├── README.md                        # Project documentation
└── images/                          # Dashboard screenshots used in this README
    ├── overview.png
    ├── forecast-performance.png
    └── inventory-health.png
```

## Screenshots

### Overview
![Overview page](images/overview.png)

### Forecast Performance
![Forecast Performance page](images/forecast-performance.png)

### Inventory Health
![Inventory Health page](images/inventory-health.png)

## Skills Demonstrated

- Data modelling and cleaning in Power BI
- Writing **DAX measures** (accuracy, bias, stockout/overstock rates, days of supply)
- KPI design and data storytelling with insight-led headlines
- Dashboard layout, theming and consistent visual design
- Retail analytics: demand forecasting evaluation and inventory health

## Possible Next Steps

- Add a replenishment view comparing orders against demand and stock to quantify the ordering gap
- Add store-by-product drill-through pages for the at-risk rows
- Add a target line for forecast accuracy and stockout rate
- Publish to Power BI Service with scheduled refresh

## Author

**Mohammad Adhil** · Data Analyst
MBA in Business Analytics · Skills: SQL, Python, Excel, Power BI

- GitHub: `<your-github-username>`
- LinkedIn: `<your-linkedin-url>`

Feedback and suggestions are welcome. Feel free to open an issue.
