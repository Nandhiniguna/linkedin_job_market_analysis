# LinkedIn Job Market Analytics – Power BI Dashboard

## Project Overview

This project analyzes real LinkedIn job postings (2023–2024) from a Kaggle dataset (~124K postings, 11 CSV files) to explore hiring trends, salary benchmarks, job demand vs competition, remote work patterns, and company‑level behavior.

The final deliverable is a Power BI report with a star‑schema model and four interactive dashboard pages tailored for data analyst / BI portfolio use.

## Data Source

**Dataset:** LinkedIn Job Postings (2023–2024) – Kaggle

**Scope:** ~124K job postings scraped from LinkedIn, spanning multiple countries, companies, and roles.

### Files Used (Core)

- `job_postings.csv` (renamed to `Fact_Job_Postings` in Power BI) – main fact table
- `company_details/companies.csv` – company attributes (name, HQ location, size)
- `company_details/employee_counts.csv` – company employee counts and follower counts
- `job_details/benefits.csv` – optional enrichment for benefits at job level
- **Key text fields:** `skills_desc`, `description` for skills and text analysis

**Dataset link:** (add Kaggle URL here in your repo)

## Objective

To analyze hiring trends, salary benchmarks, job demand and competition, remote work adoption, and company‑level hiring patterns using real LinkedIn job posting data, and present them through an interactive Power BI dashboard.

## Data Preparation

All cleaning and prep were done in Power Query inside Power BI.

### Data Ingestion

- Imported `job_postings.csv` as the primary dataset
- Imported `companies.csv` and `employee_counts.csv` for company enrichment

### Cleaning & Transformation

- Removed duplicate rows using `job_id`
- Created `posted_date` from `listed_time` (DateTime → Date)
- Standardized job titles into `job_title_clean`
- Normalized work type into `work_type_clean` (Remote / Hybrid / Onsite)
- Validated and cleaned `applies` and `views` (removed invalid or obviously erroneous values)
- **Normalized salary:**
  - Used `min_salary`, `med_salary`, `max_salary` and `pay_period` (hourly/monthly/yearly)
  - Created `annual_salary` by converting all pay periods to annual equivalents
  - Performed basic outlier checks on salary ranges
- Parsed job location text into country, state, city where possible
- Prepared text fields using `skills_desc` and `description` for skills/keyword analysis

## Data Model (Star Schema)

### Fact Table

**Fact_Job_Postings** (from `job_postings.csv`):
- `job_id`
- `company_id`
- `posted_date`
- `job_title_clean`
- `experience_level` (from `formatted_experience_level`)
- `work_type_clean`
- `applies`
- `views`
- `annual_salary`
- Location attributes (country/state/city derived from job location)

### Dimension Tables

**Dim_Date**
- Derived from `posted_date`
- Used for trends and time‑based filtering

**Dim_Role**
- Built from `job_title_clean`
- Used for role, seniority, and salary analysis

**Dim_Company** (from `companies.csv`)
- Company name
- HQ country/state/city
- Company size band

**Dim_Employee_Stats** (from `employee_counts.csv`)
- Employee count
- Follower count

**Dim_Location** (optional)
- Standardized country/state/city from job location

### Relationships

- `Fact_Job_Postings[posted_date]` → `Dim_Date[Date]`
- `Fact_Job_Postings[company_id]` → `Dim_Company[company_id]`
- `Dim_Company[company_id]` → `Dim_Employee_Stats[company_id]`

## Key DAX Measures

Some example measures used in the report (simplified names):

```dax
Total Jobs = COUNTROWS(Fact_Job_Postings)

Distinct Companies = DISTINCTCOUNT(Fact_Job_Postings[company_id])

Median Salary = MEDIAN(Fact_Job_Postings[annual_salary])

Avg Salary = AVERAGE(Fact_Job_Postings[annual_salary])

Avg Applies per Job = DIVIDE(SUM(Fact_Job_Postings[applies]), [Total Jobs])

Avg Views per Job = DIVIDE(SUM(Fact_Job_Postings[views]), [Total Jobs])

Remote % = DIVIDE(Jobs marked Remote/Hybrid in work_type_clean, [Total Jobs])

Apply Rate = DIVIDE(SUM(Fact_Job_Postings[applies]), SUM(Fact_Job_Postings[views]))

Time to Fill (days) = DATEDIFF(Fact_Job_Postings[listed_time], Fact_Job_Postings[closed_time], DAY)
```
*(where `closed_time` exists)*

## Dashboard Pages

### Page 1 – Overview

**KPIs (cards)**
- Total Jobs (Count of `job_id`)
- Distinct Companies (Distinct `company_id`)
- Median Salary
- Remote %
- Avg Applies per Job

**Charts**
- **Filled Map:** Job count by country
- **Combo Chart:**
  - Columns → Top 10 job titles by job count
  - Line → Median salary for those titles

**Purpose:** Quick snapshot of market size, salary levels, and high‑demand roles.

### Page 2 – Salary & Roles

**Slicers**
- Country, City
- Experience Level
- Work Type / Remote
- Date (`posted_date`)

**Charts**
- **Clustered column:** Median salary by job title or by experience level
- **Box‑style approximation:** min/median/max salary by country or by company size

**Purpose:** Salary benchmarking across locations, roles, and seniority.

### Page 3 – Demand & Competition

**Charts**
- **Scatter (hero visual):**
  - X = Median salary
  - Y = Avg applies per job
  - Size = Views
  - Color = Country
  - Detail = Job title or company
- **Column charts:**
  - Top 10 job titles by job count (high demand)
  - Top 10 job titles by applies per job (high competition)

**Purpose:** Understand how compensation, demand, and competition interact for different roles and markets.

### Page 4 – Remote & Company View

**Charts**
- **Stacked column:** Remote vs onsite jobs by country and by work type
- **Matrix / table:**
  - Company name
  - HQ country
  - Company size
  - Job count
  - Median salary
  - Remote %
  - Avg views
  - Avg applies

**Purpose:** Company‑level hiring behavior and remote work adoption.

## Validation & Quality Checks

### Data Validation

- Fact row count matches cleaned `job_postings.csv`
- `posted_date` correctly derived from `listed_time`
- Salary values within realistic ranges; outliers inspected
- `applies` and `views` checked for invalid values and extreme outliers
- Company joins validated using `company_id`

### Business Logic Validation

- Median vs average salary compared by role and location to ensure consistency
- Remote % recalculates correctly under different slicer combinations
- Time‑to‑fill logic checked where `closed_time` exists
- Senior roles show higher salary distributions than junior roles in general

### UX & Performance

- All slicers (date, location, role, experience, work type) correctly filter relevant visuals
- Drill‑down enabled for date (year → month) and geography (country → state → city where available)
- Measures used instead of unnecessary calculated columns to keep model light
- Text‑heavy/skills visuals isolated to dedicated views to keep page load reasonable

## Repository Contents

Suggested structure for this repo:

```
/
├── README.md (this file)
├── reports/
│   └── linkedin-job-market-dashboard.pbix – Power BI report (if shared)
├── docs/
│   ├── data_dictionary.md – key fields used from each file
│   └── model_diagram.png – exported model view
├── dax/
│   └── DAX_measures.md – detailed DAX definitions
└── power_query/
    └── (optional Power Query M scripts for key transformations)
```

---

This project demonstrates end‑to‑end work: data ingestion, cleaning, modeling, DAX, and interactive business‑focused visualization on top of a real job‑market dataset.
