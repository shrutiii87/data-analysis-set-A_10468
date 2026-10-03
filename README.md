<img width="1200" height="500" alt="1st" src="https://github.com/user-attachments/assets/37b6b483-6ae2-4006-ad07-edb18ff903dd" />



# 🚚 Delivery Delay Analysis

A logistics company wants to understand where and why its deliveries are running late, so it can decide which service type and which hubs need operational attention. This project analyzes 12 delivery records across 4 routes and 4 hubs (Jan–Mar) using SQL, Python, Excel, and Power BI, and answers two business questions.

---

**Student Name:** Shruti Bhawsar
**GR No:** 10468
**Assigned Set:** Set A
**Repository:** `data-analysis-set-A_10468`


---

🔨 Tools used :- 

 
<div>

<img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
<img src="https://img.shields.io/badge/Excel-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white"/>
<img src="https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"/>
<img src="https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white"/>
<img src="https://img.shields.io/badge/MySQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white"/>
<img src="https://img.shields.io/badge/Pandas-150458?style=for-the-badge&logo=pandas&logoColor=white"/>
<img src="https://img.shields.io/badge/NumPy-013243?style=for-the-badge&logo=numpy&logoColor=white"/>
<img src="https://img.shields.io/badge/Matplotlib-11557C?style=for-the-badge&logo=plotly&logoColor=white"/>
<img src="https://img.shields.io/badge/DAX-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"/>
<img src="https://img.shields.io/badge/Power_Query-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"/>
<img src="https://img.shields.io/badge/XLOOKUP-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white"/>
<img src="https://img.shields.io/badge/PivotTable-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white"/>
<img src="https://img.shields.io/badge/SUMIFS-217346?style=for-the-badge&logo=microsoft-excel&logoColor=white"/>
<img src="https://img.shields.io/badge/DAX_Measures-Calc--Column%20%7C%20SUMX%20%7C%20DIVIDE-EC4899?style=for-the-badge"/>

</div>

---

## 📌 Overview

Delivery Delay Analysis to identify which service type has the highest delay burden and which hub needs priority attention. Done across four modules — Excel, Power BI, SQL, and Python — using deliveries and routes datasets linked by route_id. Includes duplicate removal, delay_days derivation, and analysis by service type, hub, route, and month.

---

## 🎯 Business Objective

The business objective of this project is to analyze delivery delay patterns from route-level delivery data to answer two key questions — which service type (Standard vs Express) contributes most to overall delay burden and which hub requires immediate attention. The analysis aims to support operational decisions by identifying high-delay routes and hubs, understanding delay trends over time, and recommending targeted improvements to reduce delays and enhance service reliability.

---

<img width="1536" height="1024" alt="7ca0ec1e-2ae6-43dd-8681-9e4b6d2ee6bb" src="https://github.com/user-attachments/assets/de8dcf8e-6b36-4a0f-ba3f-30b50701b9fd" />


---

## 🎬 Project Demo

[![Watch Demo](https://img.shields.io/badge/Watch%20Demo-click_to%20watch%20video-blue?style=for-the-badge&logo=googledrive&logoColor=white)](https://drive.google.com/file/d/1RbKVw8aQmSmwppOHCuHrSRZvQMGr-aEG/view?usp=sharing)

📹 Add a link to your project walkthrough video here.

---

## 📁 Dataset Filenames

| File | Description |
|---|---|
| `setup.sql` | Creates `routes` and `deliveries` tables, inserts raw data |
| `queries.sql` | 5 SQL analysis queries |
| `deliveries.csv` | Raw delivery records |
| `routes.csv` | Raw route lookup table |
| `clean_data.csv` | Cleaned, merged Python output |
| `python_summary.csv` | Python service-type delay summary |
| `Delivery_Delay_Analysis.ipynb` | Python notebook |
| `s2a_delay_by_service_type_csv.csv` | SQL — delay by service type |
| `s2b_routes_significant_delay.csv` | SQL — routes with delay > 8 |
| `s2c_top_two_hubs.csv` | SQL — top 2 hubs by delay |
| `s3_unmatched_route_check.csv` | SQL — orphaned route check |
| `analysis.xlsx` | Excel: `Raw`, `Lookup`, `Clean`, `Summary` sheets |
| `Power_BI_dashboard.png` | Dashboard screenshot |

---

<img width="1600" height="820" alt="task 2 " src="https://github.com/user-attachments/assets/ab70e9c2-d3be-498e-a096-2eae7574246c" />

---

### 📖 Data Dictionary

**`routes` (4 rows)**

| Column | Type | Meaning |
|---|---|---|
| `route_id` | Text (PK) | `R1`–`R4` |
| `route_name` | Text | Route name |
| `service_type` | Text | `Express` or `Standard` |

**`deliveries` (13 raw rows → 12 after cleaning)**

| Column | Type | Meaning |
|---|---|---|
| `record_id` | Integer (PK) | Unique delivery ID |
| `month` | Text | `Jan` / `Feb` / `Mar` |
| `route_id` | Text (FK) | → `routes.route_id` |
| `hub` | Text | Ahmedabad / Chennai / Delhi / Mumbai |
| `promised_days` | Integer | Promised delivery days |
| `actual_days` | Integer | Actual delivery days |

**Derived columns**

| Column | Type | Meaning |
|---|---|---|
| `delay_days` | Integer | `MAX(actual_days − promised_days, 0)` |
| `is_delayed` | Boolean | `actual_days > promised_days` |

**Full cleaned dataset (12 rows):**

| record_id | month | route_id | hub | promised | actual | route_name | service_type | delay_days | is_delayed |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Jan | R1 | Ahmedabad | 2 | 2 | Metro Link | Express | 0 | False |
| 2 | Jan | R2 | Chennai | 3 | 4 | City Dash | Express | 1 | True |
| 3 | Jan | R3 | Delhi | 5 | 5 | Highway Freight | Standard | 0 | False |
| 4 | Jan | R4 | Mumbai | 4 | 10 | Rural Feeder | Standard | 6 | True |
| 5 | Feb | R1 | Chennai | 2 | 5 | Metro Link | Express | 3 | True |
| 6 | Feb | R2 | Delhi | 3 | 3 | City Dash | Express | 0 | False |
| 7 | Feb | R3 | Mumbai | 5 | 10 | Highway Freight | Standard | 5 | True |
| 8 | Feb | R4 | Chennai | 6 | 7 | Rural Feeder | Standard | 1 | True |
| 9 | Mar | R1 | Delhi | 2 | 8 | Metro Link | Express | 6 | True |
| 10 | Mar | R2 | Mumbai | 3 | 5 | City Dash | Express | 2 | True |
| 11 | Mar | R3 | Chennai | 5 | 5 | Highway Freight | Standard | 0 | False |
| 12 | Mar | R4 | Mumbai | 6 | 15 | Rural Feeder | Standard | 9 | True |

---

## 🧹 Cleaning Steps Taken

- Loaded `deliveries.csv` + `routes.csv`; checked shape, dtypes, nulls (none found).
- Found and removed 1 exact duplicate row (`record_id = 12`): **13 → 12 rows**.
- Left-merged `deliveries` with `routes` on `route_id`.
- Validated merge: 12 rows, zero missing `service_type` (confirmed via SQL unmatched-route query → `0`).
- Derived `delay_days` and `is_delayed`.
- Exported `clean_data.csv` and `python_summary.csv`.

### 📐 Metric Definitions

```
delay_days = MAX(actual_days - promised_days, 0)

delay_incidence_rate =
    COUNT(actual_days > promised_days) / COUNT(total records) * 100
```

Example (Express, 6 records): 4 delayed → `4/6*100 = 66.67%`; `total_delay_days = 1+3+6+2 = 12`

---

## 🛠️ Tools & Versions Used

| Tool | Version |
|---|---|
| SQL Engine | SQLite 3 |
| Python | 3.11 |
| pandas | 2.2.x |
| matplotlib | 3.8.x |
| Jupyter Notebook | 7.x |
| Microsoft Excel | Microsoft 365 (desktop) |
| Power BI | Power BI Desktop (Sept 2026 release) |

> Run `pip freeze > requirements.txt` and paste exact pinned versions here.

---

## 📊 TASK :- 1 Excel Sheet Guide 

<img width="1200" height="500" alt="task 2 " src="https://github.com/user-attachments/assets/76dd39d5-efd1-475f-8855-2b6922596378" />

---

| Sheet | Purpose |
|---|---|
| `Raw` | Original, unedited `deliveries` data (13 rows) plus a row-count check cell |
| `Lookup` | Route reference table (`route_id`, `route_name`, `service_type`) used for lookups |
| `Clean` | Deduplicated + merged data (12 rows) with `delay_days` calculated; includes a Cleaning Check panel (Before Row Count 13 → After Row Count 12 → Duplicate Removed 1) |
| `Summary` | PivotTable-based summary — total delay days by hub, and total delay days by service type × month |

---

## 🧮 TASK :- 2  SQL Setup & Query Execution Steps

<img width="1200" height="500" alt="task 2 " src="https://github.com/user-attachments/assets/68802ff9-bbaa-4e0e-b02e-b2f884171887" />

---

Run `setup.sql` first, then `queries.sql`:

```bash
sqlite3 delivery.db < setup.sql
sqlite3 delivery.db < queries.sql
```

**`setup.sql`** — creates tables + loads data (key part):

```sql
CREATE TABLE deliveries (
    record_id INTEGER PRIMARY KEY,
    month VARCHAR(10) NOT NULL,
    route_id VARCHAR(10) NOT NULL,
    hub VARCHAR(50) NOT NULL,
    promised_days INTEGER NOT NULL,
    actual_days INTEGER NOT NULL,
    CONSTRAINT fk_deliveries_route
        FOREIGN KEY (route_id) REFERENCES routes(route_id)
);
```

**`queries.sql`** — the core delay-metric query (repeated per grouping):

```sql
SELECT
    r.service_type,
    SUM(GREATEST(d.actual_days - d.promised_days, 0)) AS total_delay_days
FROM deliveries AS d
JOIN routes AS r ON d.route_id = r.route_id
GROUP BY r.service_type
ORDER BY total_delay_days DESC;
```

Same pattern is reused for the route (`> 8` filter), hub (`LIMIT 2`), and unmatched-route (`LEFT JOIN ... IS NULL`) queries — see `queries.sql` for the full set of 5.

---

## 🐍 TASK :- 3 Python Environment Setup & Run Instructions

<img width="1200" height="500" alt="task 2 " src="https://github.com/user-attachments/assets/03c99021-5bdd-47bf-a8ca-8d7327180385" />


---

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

pip install -r requirements.txt
python python/analysis.py
```

Or run interactively:

```bash
jupyter notebook python/Delivery_Delay_Analysis.ipynb
```

**Core cleaning + metric logic** (from the notebook):

```python
deliveries = deliveries.drop_duplicates()
df = deliveries.merge(routes, on='route_id', how='left')

df['delay_days'] = (df['actual_days'] - df['promised_days']).clip(lower=0)
df['is_delayed'] = df['actual_days'] > df['promised_days']

df.to_csv('clean_data.csv', index=False)
```

Notebook stages: `P1` load/clean/merge → `P2` derive metrics + service-type summary → `P3` monthly delay chart → export CSVs.


---

## 📊 TASK :- 4  Power BI Data-Source Refresh Instructions

<img width="1200" height="500" alt="task 2 " src="https://github.com/user-attachments/assets/4bc49d82-f9d6-449e-988f-93c5fad510b4" />

---

<img width="1165" height="657" alt="Power BI dashboard" src="https://github.com/user-attachments/assets/a17887a7-f1bc-4537-8aa6-9aef5a561dd7" />


---

1. Open `.pbix` in Power BI Desktop.
2. **Home → Transform Data → Data Source Settings**.
3. Select the `clean_data.csv` source → **Change Source…**.
4. Point to the cloned repo path:
   ```
   .../data-analysis-set-A_10468/python/clean_data.csv
   ```
5. **Refresh** on the Home ribbon.
6. If refresh errors, check that column headers still match (`month`, `hub`, `service_type`, `delay_days`).

---

## 📈 Numeric Findings & Recommendation

- **Finding 1:** Standard routes = **21 of 33 total delay days (≈64%)** vs 12 for Express.
- **Finding 2:** **Mumbai** = **22 total delay days**, vs 6 for the next hub (Delhi). Route-level: Rural Feeder (R4) = 16, Metro Link (R1) = 9 — the only two routes above the 8-day threshold.
- **Trend:** Total delay days by month: **Jan 7 → Feb 9 → Mar 17** (rising every month).

**Recommendation:** Prioritize Mumbai hub capacity and the Rural Feeder (R4) route — they drive the majority of delay, and the trend is worsening, not flat.

---

## 🔗 Cross-Tool Reconciliation

**Metric:** Total delay days

| Tool | Value |
|---|---|
| Python | 33 |
| SQL | 33 |
| Excel | 33 |
| Power BI | 42 |

**Note:** Power BI uses the raw 13-row source (duplicate `record_id 12` not removed). Python/SQL/Excel dedupe first → 33. No rounding issue; gap = 1 duplicate row × 9 delay days.

---

**Overall delay incidence rate — all 12 deliveries**

| Tool | Value |
|---|---|
| SQL | 66.67% |
| Python | 66.666...% (rounds to 66.67%) |
| Excel | 66.67% |
| Power BI | 66.67% |

✅ Matches once rounded to 2 decimals — Python's raw float is just the unrounded form of the same number.

---

## ⚠️ Assumptions & Limitations

- Small sample (12 rows) — findings are directional, not statistically robust.
- Early/on-time deliveries all treated as `delay_days = 0`.
- Only 1 duplicate found; no other data-quality issues after cleaning.

---


## 🙏 Thank You

Thank you for taking the time to explore this project!
Your feedback, suggestions, and contributions are always welcome.

⭐ If you found this project helpful, don't forget to **star the repository** and share it with others.
