![Status](https://img.shields.io/badge/status-active-success.svg)


## Project Overview — Layoffs-Data-Cleaning-Project (SQL)

### 1. Project Summary
Cleaned and transformed a raw **layoffs dataset** into an analysis-ready table by standardizing company names, normalizing locations, fixing date formats, calculating missing percentages, and structuring the data into staging layers.  
The workflow removed duplicates, handled nulls/blanks, enforced consistent formatting, and prepared tables suitable for downstream analytics, dashboards, and reproducible pipelines.

**Skills demonstrated:**  
- SQL data cleaning & transformation  
- ETL / staging design  
- Data modeling (star schema)  
- Feature engineering  
- Data validation & quality checks  
- Documentation & reproducible workflow design  

---

### 2. Business Objective
Apply best-practice data cleaning and normalization techniques to ensure decision-makers can:

- Track layoff events accurately across companies and countries  
- Compute layoff percentages and time-based trends  
- Build reliable dashboards and BI models  
- Perform cross-regional and cross-industry comparisons  

---

### 3. Dataset Description
**Source table:** `layoffs` (raw)

**Key observed columns:**
- `company` — free-text company name  
- `date` — event date stored in multiple inconsistent formats  
- `location` — mixed city/country or free-text values  
- `employees_laid_off` — numeric count  
- `percentage_laid_off` — provided or missing  
- Additional metadata fields  

**Raw data issues included:** inconsistent casing, excess whitespace, duplicates, nulls, mixed date formats, missing percentages, and combined location fields.

---

### 4. Detailed Methodology (SQL Pipeline)

#### 🔹 Staging Strategy
- Create staging table: `layoffs_stagging` (structure cloned from `layoffs`)
- Load raw data into staging for all cleaning transformations  
- Preserve the original `layoffs` table as immutable raw source

#### 🔹 Deduplication
Use `ROW_NUMBER()` over natural keys:  
`(company, date, location, employees_laid_off)`  
Delete rows where `row_num > 1`.

#### 🔹 Text Standardization
- Apply `TRIM()` to remove whitespace  
- Use `LOWER()` / `INITCAP()` to normalize formatting  
- Clean stray characters using `REGEXP_REPLACE()`  

#### 🔹 Null & Blank Handling
- Convert empty strings to `NULL`  
- Replace `N/A`, `Unknown`, `-` with standardized nulls or sentinel values  

#### 🔹 Numeric Conversions
- Remove non-numeric characters (commas, `%`, `+`)  
- Cast cleaned values to integer/decimal   

🔹 Date Cleaning

Standardize formats using TO_DATE() or STR_TO_DATE()

Convert final result to DATE type

For month/year-only values: set to the 1st of the month + flag approximate_date

🔹 Location Parsing

Split combined locations using last comma into city and country

Apply country mapping table (ISO) for standardization

🔹 Column Cleanup

Drop intermediate helper columns (e.g., row_num)

🔹 Finalization

Insert cleaned rows into layoffs_stagging2 (final cleaned table)

Add necessary constraints and indexes (company, date, country)

## 🧱 Recommended Data Model (Star Schema)

### 📌 fact_layoffs
| Column              | Description                           |
|---------------------|---------------------------------------|
| layoff_id (PK)      | Unique layoff event                   |
| company_id (FK)     | Links to `dim_company`                |
| date_id (FK)        | Links to `dim_date`                   |
| employees_laid_off  | Number of employees laid off          |
| total_employees     | Total workforce before layoffs        |
| percent_laid_off    | Percentage of workforce laid off      |
| source              | Data source or metadata               |

---

### 📌 dim_company
| Column          | Description                               |
|-----------------|-------------------------------------------|
| company_id (PK) | Unique company identifier                  |
| company_name    | Cleaned & standardized name                |
| industry        | Industry classification                    |
| hq_country      | Company headquarters country               |

---

### 📌 dim_date
| Column          | Description                               |
|-----------------|-------------------------------------------|
| date_id (PK)    | Unique date identifier                     |
| full_date       | Full date (DATE)                           |
| year            | Year                                       |
| quarter         | Quarter                                    |
| month           | Month                                      |
| day             | Day                                        |
| is_approximate  | TRUE/FALSE flag for estimated dates        |

---

### 📌 dim_location
| Column             | Description                             |
|--------------------|-----------------------------------------|
| location_id (PK)   | Unique location identifier               |
| city               | City name                                |
| country            | Country name                             |
| iso_country_code   | Standardized ISO country code            |

---

This star schema supports fast OLAP queries, enriched reporting, and scalable dashboard design.

---

## Suggested Analyses & KPIs

- Total layoffs by year/quarter  
- Top 10 companies by proportion of workforce laid off  
- Layoff rate by country  
- 3-month rolling layoff trend  
- Distribution of layoff percentages  

---

## Dashboard Concepts

### **Pages**
- **Overview** — KPIs, time series trend, global map  
- **Company Insights** — layoffs over time, proportions, comparison charts  
- **Geography** — country-level choropleth, city tables  

### **Recommended Visuals**
- Line charts  
- Bar charts (Top N)  
- Choropleth map  
- Histogram  
- Scatterplots  
