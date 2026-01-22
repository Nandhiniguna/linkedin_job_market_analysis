# Data Dictionary – LinkedIn Job Postings (2023–2024)

Source dataset: **LinkedIn Job Postings (2023–2024)** on Kaggle. [page:1]  
This project uses a subset of the available files and columns, focused on job postings, companies, and company employee statistics. [page:1]

---

## 1. Fact_Job_Postings  (from `job_postings.csv`)

Main fact table containing one row per job posting. [page:1]

| Column name               | Type        | Description                                                                                 |
|---------------------------|------------|---------------------------------------------------------------------------------------------|
| job_id                    | Integer    | Unique job identifier as defined by LinkedIn.                                              |
| company_id                | Integer    | Identifier of the company associated with the job (links to `companies.csv`).              |
| title                     | Text       | Original job title from LinkedIn.                                                          |
| job_title_clean           | Text       | Cleaned / standardized job title created in Power Query.                                   |
| description               | Text       | Full job description text.                                                                 |
| min_salary                | Decimal    | Minimum salary value provided in the posting.                                              |
| med_salary                | Decimal    | Median salary value provided in the posting.                                               |
| max_salary                | Decimal    | Maximum salary value provided in the posting.                                              |
| pay_period                | Text       | Salary pay period (Hourly, Monthly, Yearly).                                               |
| currency                  | Text       | Currency code for the salary (e.g., USD, GBP).                                             |
| annual_salary             | Decimal    | Normalized annual salary derived from salary fields and pay_period (project transformation). |
| formatted_work_type       | Text       | Work type such as Fulltime, Parttime, Contract.                                            |
| work_type                 | Text       | Work type associated with the job (raw field from dataset).                                |
| work_type_clean           | Text       | Derived work type category (Remote / Hybrid / Onsite).                                     |
| location                  | Text       | Raw job location string from LinkedIn.                                                     |
| country                   | Text       | Parsed country from location where available.                                              |
| state                     | Text       | Parsed state/region from location where available.                                         |
| city                      | Text       | Parsed city from location where available.                                                 |
| applies                   | Integer    | Number of applications submitted for the job.                                              |
| views                     | Integer    | Number of times the job posting has been viewed.                                           |
| remote_allowed            | Integer    | Flag indicating whether remote work is permitted (0/1).                                    |
| formatted_experience_level| Text       | Experience level label (entry, associate, mid, senior, executive, etc.).                   |
| experience_level          | Text       | Derived/cleaned experience level (may mirror formatted_experience_level).                  |
| skills_desc               | Text       | Description of required skills extracted for the job.                                      |
| listed_time               | DateTime   | Timestamp when the job was listed.                                                         |
| original_listed_time      | DateTime   | Original listed time if the posting was updated/relisted.                                  |
| posted_date               | Date       | Date derived from listed_time (project transformation).                                    |
| expiry                    | DateTime   | Expiration date/time of the job listing.                                                   |
| closed_time               | DateTime   | Timestamp when the job listing was closed.                                                 |
| job_posting_url           | Text       | URL to the job posting (LinkedIn or other platform).                                       |
| application_url           | Text       | URL where applications can be submitted.                                                   |
| application_type          | Text       | Type of application process (offsite, simple onsite, complex onsite).                      |
| posting_domain            | Text       | Domain of the website hosting the application.                                             |
| sponsored                 | Integer    | Indicates whether the job listing is sponsored/promoted.                                   |
| compensation_type         | Text       | Type of compensation (e.g., base, base + bonus).                                           |

---

## 2. Dim_Company  (from `company_details/companies.csv`)

Company attributes used for company‑level and HQ analyses. [page:1]

| Column name   | Type     | Description                                                                 |
|---------------|----------|-----------------------------------------------------------------------------|
| company_id    | Integer  | Unique company identifier (joins to Fact_Job_Postings.company_id).          |
| name          | Text     | Company name.                                                               |
| description   | Text     | Company description from LinkedIn.                                         |
| company_size  | Integer  | Company size band (0–7, from smallest to largest).                         |
| country       | Text     | Country of company headquarters.                                           |
| state         | Text     | State/region of company headquarters.                                      |
| city          | Text     | City of company headquarters.                                              |
| zip_code      | Text     | ZIP/postal code of headquarters.                                           |
| address       | Text     | Street address of headquarters.                                            |
| url           | Text     | URL to the company’s LinkedIn page.                                        |

---

## 3. Dim_Employee_Stats  (from `company_details/employee_counts.csv`)

Time‑stamped employee and follower counts for companies. [page:1]

| Column name    | Type     | Description                                                |
|----------------|----------|------------------------------------------------------------|
| company_id     | Integer  | Company identifier (joins to Dim_Company.company_id).     |
| employee_count | Integer  | Number of employees at the company at time_recorded.      |
| follower_count | Integer  | Number of LinkedIn followers at time_recorded.            |
| time_recorded  | Integer  | Unix timestamp when the counts were collected.            |

---

## 4. job_details/benefits.csv  (optional enrichment)

Used for benefits‑level analysis where included. [page:1]

| Column name | Type    | Description                                                                 |
|-------------|---------|-----------------------------------------------------------------------------|
| job_id      | Integer | Job identifier (joins to Fact_Job_Postings.job_id).                        |
| type        | Text    | Benefit type (e.g., 401K, Medical Insurance, Paid Time Off).               |
| inferred    | Integer | Flag indicating whether the benefit was inferred or explicitly tagged.     |

---

## 5. Dim_Date  (project‑created date table)

Standard date dimension generated in Power BI based on posted_date. [page:1]

| Column name | Type   | Description                                      |
|------------|--------|--------------------------------------------------|
| Date       | Date   | Calendar date.                                   |
| Year       | Integer| Calendar year.                                   |
| Month      | Integer| Month number (1–12).                             |
| MonthName  | Text   | Month name (Jan, Feb, etc.).                     |
| Quarter    | Integer| Quarter of the year (1–4).                       |
| YearMonth  | Text   | Concatenated Year‑Month label (e.g., 2024‑01).   |

---

## 6. Dim_Role  (project‑created from job_title_clean)

Role dimension used for role‑level views. [page:1]

| Column name   | Type   | Description                                         |
|---------------|--------|-----------------------------------------------------|
| Role_ID       | Integer| Surrogate key for role (if created).                |
| job_title_clean| Text  | Standardized job title.                             |
| Role_Group    | Text   | Optional grouping (e.g., Data, Marketing, Finance). |

---

## 7. Dim_Location  (project‑created, optional)

Standardized job location dimension. [page:1]

| Column name | Type    | Description                                      |
|------------|---------|--------------------------------------------------|
| Location_ID| Integer | Surrogate key for location.                      |
| country    | Text    | Country for job location.                        |
| state      | Text    | State/region for job location.                   |
| city       | Text    | City for job location.                           |

---

You can trim or extend this dictionary depending on which columns you actually use in your visuals and measures.
