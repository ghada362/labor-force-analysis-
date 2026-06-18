# Egypt Labor Market Insights

An end-to-end data platform analyzing Egypt's labor market, built on CAPMAS 2024 national labor force survey data. The project covers the complete BI lifecycle — from raw survey data to interactive dashboards, automated reporting, and an AI-powered assistant.

## 📊 Project Overview

The Egyptian Labour Force survey (CAPMAS 2024) generated approximately **297,000 individual records** across 12 normalized OLTP tables. These records were scaled using the **Annual Weight** field to represent Egypt's full population of **106 million**.

The original OLTP structure was optimized for data entry, not analytics — leading to key pain-points:
- Complex multi-table joins required for every report
- Poor BI query performance
- No historical tracking or aggregation layer
- Difficult Power BI integration

This project solves that by building a complete **Data Warehouse (OLAP)** that transforms and consolidates the data into a reporting-optimized model.

## 🏗️ Architecture & Pipeline

```
CAPMAS 2024 Survey  →  Data Cleaning  →  ERD & SQL Modeling  →  Data Warehouse
                                                                       │
        ┌──────────────────────────────────────────────────────────┤
        ▼                  ▼                  ▼                     ▼
      SSRS             Power BI            Tableau            RAG System
   (Reports)          (Dashboards)      (Visualizations)    (n8n + Docker + VS)
                                                                       │
                                                    ┌──────────────────┤
                                                    ▼                  ▼
                                              Power Apps Form      Website
```

### Data Foundation
- **Source:** CAPMAS 2024 national labor force survey
- **Scale:** ~297,000 individual records → scaled to 106M population via Annual Weight
- **Cleaning:** Python (pandas) & Excel — missing value imputation, outlier detection, type fixing, module merging
- **Modeling:** ERD design + relational modeling in SQL Server using Red Gate tools

### Data Warehouse
Built in three stages:

**Stage 1** — Staging Area + Full Load


OLTP Source → Staging Area → Transform & Clean → DWH Target
Data isolation (DWH never reads directly from OLTP), transformation, and error handling before loading
One-time full load: 296K records across 12 source tables


**Stage 2** — Audit Columns + Incremental Load


Audit columns (CreatedDate, ModifiedDate, Last_Load_Date) added to every staging table
Incremental load fetches only new rows (WHERE CreatedDate > Last_Load_Date) instead of reloading everything
7 stored procedures: incremental staging (SP 1–4), Dim_Location load via NOT EXISTS (SP 5), MERGE on the fact table (SP 6), and ETL control updates (SP 7)


**Stage 3**— Automation via SQL Server Agent


The full 4-step pipeline (incremental staging → dimension load → fact MERGE → control update) runs automatically on a schedule
Results: 0 duplicate records, 100% upsert accuracy, fully automated pipeline

### ETL Strategy
- **Incremental Load:** `CreatedDate` audit columns + `ETL_Control` table to track load batches
- **Loading Methods:**
  - Dimensions: `NOT EXISTS` logic to prevent duplicates
  - Fact table: `MERGE` (upsert) strategy
  - Designed with SCD Type 1 & 2 readiness
- **Data Quality — 3-Layer Defense:** incremental filtering, MERGE logic, and unique indexes to guarantee data integrity
- **Automation:** SQL Server Agent jobs schedule and run the full ETL pipeline automatically

### Reporting & BI
| Tool | Purpose |
|------|---------|
| **SSRS** | Pixel-perfect paginated reports — labor force stats by governorate, salary distribution, COVID-19 impact, health insurance coverage, and more |
| **Power BI** | 16 interactive dashboards across 5 thematic groups (Employment & Labor Market, Income & Economic Profile, Education & Training, Demographics & Geography, Social Protection & Safety Net) — built with Arabic field support and weighted DAX measures |
| **Tableau** | Advanced analytical dashboards: Survey Coverage & Representativeness, Social Vulnerability Index, Income & Employment Statement |

### Smart Layer
- **RAG System:** built with **n8n** (workflow automation), **Docker** (containerized deployment), and **Visual Studio** (backend API & RAG logic) — enables natural language querying of the labor market data
- **Power Apps Form:** mobile-responsive data entry form connected to the SQL Server backend
- **Public Website:** exposes key labor market findings and links to BI dashboards and the RAG assistant

## 🛠️ Tech Stack

`Python` · `Excel` · `SQL Server` · `Red Gate Tools` · `SSRS` · `Power BI` · `DAX` · `Tableau` · `n8n` · `Docker` · `Power Apps` · `Visual Studio`

## 📁 Repository Structure

```
├── data-cleaning/        # Python & Excel cleaning scripts
├── database/             # ERD, SQL Server schema, Red Gate scripts
├── data-warehouse/       # Star schema, stored procedures, ETL scripts
├── ssrs-reports/         # SSRS report definitions (.rdl)
├── power-bi/             # Power BI dashboards (.pbix)


```

## 👥 Team

This is a graduation project built by a 5-member team, with dashboards distributed across team members and grouped into five thematic categories.

## 📄 License

This project was developed for academic purposes as part of a professional ITI program in POWER BI DEVELOPMENT .
