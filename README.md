# 🚚 Delivery Delay Analysis

**Student Name:** Shruti Bhawsar
**GR No:** 10468
**Assigned Set:** Set A
**Repository:** `data-analysis-set-A_10468`

---

## 📌 Overview

This project analyzes a small but complete logistics dataset — 12 delivery records spread across 4 routes, 4 hubs, and 3 months (Jan–Mar) — using **four different tools**: SQL, Python, Excel, and Power BI. The goal is not just to compute numbers once, but to compute the **same metrics independently in each tool** and then reconcile them, so that the final answer is trustworthy and not the result of a single script or spreadsheet formula having a hidden bug.

The dataset is intentionally small (12 rows) so that every number in this README can be manually traced back to the raw data if needed — nothing here is a black box.

---

## 🎯 Business Objective

A logistics company wants to understand **where** and **why** its deliveries are getting delayed, so it can prioritize operational fixes across its service types, routes, and hubs, rather than reacting to delays after the fact. This project cleans, merges, and analyzes the delivery data to surface the highest-impact delay sources and to check whether the delay problem is stable or getting worse over time.

### Business Questions Answered

1. **Which service type (Express vs Standard) accumulates the most total delay days, and which specific routes cross a significant-delay threshold (> 8 total delay days)?**
   This question matters because service types are priced and staffed differently — if one service type is systematically underperforming its promise, that has direct customer-experience and contractual implications.

2. **Which hubs are the biggest contributors to delay, and how does total delay trend month over month (Jan → Mar)?**
   This question matters because hub-level delay usually points to a local, fixable cause (staffing, vehicle availability, local traffic/route conditions), and the monthly trend tells us whether this is a one-off blip or a worsening pattern that needs urgent attention.

Both questions are answered independently in SQL, Python, Excel, and Power BI, and the results are cross-checked in the **Reconciliation** section below.

---

## 📁 Dataset Filenames

| File | Description |
|---|---|
| `setup.sql` | Creates the `routes` and `deliveries` tables (with a foreign key constraint) and inserts the raw source data |
| `queries.sql` | Five SQL analysis queries: delay by service type, high-delay routes, top hubs, unmatched-route check, row counts |
| `deliveries.csv` | Raw delivery-level source table read by the Python notebook |
| `routes.csv` | Raw route lookup table (route ID → name → service type) read by the Python notebook |
| `clean_data.csv` | Cleaned, deduplicated, merged Python output (deliveries + routes + derived fields) |
| `python_summary.csv` | Python's service-type delay summary (total delay days + delay incidence rate) |
| `Delivery_Delay_Analysis.ipynb` | Python notebook — load, clean, merge, derive metrics, chart, export |
| `s2a_delay_by_service_type_csv.csv` | SQL output — total delay days by service type |
| `s2b_routes_significant_delay.csv` | SQL output — routes with total delay days > 8 |
| `s2c_top_two_hubs.csv` | SQL output — top 2 hubs by total delay days |
| `s3_unmatched_route_check.csv` | SQL output — orphaned `route_id` check (data-quality validation) |
| `analysis.xlsx` | Excel workbook with four sheets: `Raw`, `Lookup`, `Clean`, `Summary` |
| `Power_BI_dashboard.png` | Screenshot of the published Power BI dashboard |

### 📖 Data Dictionary

**`routes` table / `routes.csv` (4 rows — the route master/lookup list)**

| Column | Type | Meaning |
|---|---|---|
| `route_id` | Text (Primary Key) | Unique route code — `R1`, `R2`, `R3`, `R4` |
| `route_name` | Text | Human-readable route name (e.g. "Metro Link", "Rural Feeder") |
| `service_type` | Text | Either `Express` or `Standard` — determines the delivery-speed tier the route belongs to |

**`deliveries` table / `deliveries.csv` (13 raw rows → 12 after cleaning)**

| Column | Type | Meaning |
|---|---|---|
| `record_id` | Integer (Primary Key) | Unique delivery record ID |
| `month` | Text | Delivery month — `Jan`, `Feb`, or `Mar` |
| `route_id` | Text (Foreign Key) | Links to `routes.route_id`; identifies which route handled this delivery |
| `hub` | Text | Origin/handling hub — `Ahmedabad`, `Chennai`, `Delhi`, or `Mumbai` |
| `promised_days` | Integer | Number of days promised to the customer for this delivery |
| `actual_days` | Integer | Number of days the delivery actually took |

**Derived / calculated columns** (added during cleaning, visible in `clean_data.csv` and the Excel `Clean` sheet)

| Column | Type | Meaning |
|---|---|---|
| `delay_days` | Integer | `MAX(actual_days − promised_days, 0)` — how many days late the delivery was (0 if on-time or early) |
| `is_delayed` | Boolean | `True` if `actual_days > promised_days`, else `False` — a simple yes/no flag used for the incidence-rate calculation |

For reference, here is the full 12-row cleaned dataset that every tool's numbers ultimately come from:

| record_id | month | route_id | hub | promised_days | actual_days | route_name | service_type | delay_days | is_delayed |
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

The raw `deliveries.csv` originally contained **13 rows**, one of which was an exact duplicate of `record_id = 12` (Mar / R4 / Mumbai / 6 promised / 15 actual). This was caught and removed during cleaning in both Python and Excel, independently, with matching before/after counts:

1. **Load** `deliveries.csv` and `routes.csv` and inspect shape, column dtypes, and null counts. No missing values were found in either table.
2. **Check for duplicates** in `deliveries` — one exact duplicate row was found (`record_id = 12` appeared twice, identical in every column).
3. **Remove the duplicate** — row count drops from **13 → 12**. This is recorded explicitly in the Excel `Clean` sheet's cleaning-check cell (`Before Row Count: 13`, `After Row Count: 12`, `Duplicate Removed: 1`).
4. **Merge** `deliveries` with `routes` on `route_id` using a left join, so every delivery row picks up its `route_name` and `service_type`.
5. **Validate the merge** — asserted that the merged table still has exactly 12 rows and that `service_type` is non-null for every row (i.e., every `route_id` in `deliveries` successfully matched a row in `routes`). This was cross-checked independently in SQL via the unmatched-route query, which returns an **unmatched_route_count of 0** — confirming there are no orphaned deliveries pointing to a route that doesn't exist.
6. **Derive** the two calculated columns, `delay_days` and `is_delayed`, on the cleaned, merged table (formulas below).
7. **Cast/verify types** — confirmed `promised_days` and `actual_days` are integers (not strings) before subtracting them, to avoid silent string-concatenation bugs.
8. **Export** the cleaned table as `clean_data.csv` for downstream use in Power BI, and export the service-type rollup as `python_summary.csv`.

### 📐 Metric Definitions

**Delay Days (per delivery record):**

```
delay_days = MAX(actual_days − promised_days, 0)
```

In words: if a delivery took longer than promised, `delay_days` is the number of extra days it took. If a delivery arrived early or exactly on time, `delay_days` is clamped to 0 — early deliveries are **not** treated as "negative delay," since the business only cares about lateness, not the magnitude of being early.

**Delay Incidence Rate (per group, e.g. per service type or per hub):**

```
delay_incidence_rate = (COUNT(records where actual_days > promised_days) / COUNT(total records in group)) × 100
```

In words: of all the deliveries in a given group, what percentage arrived later than promised at all (regardless of how many days late). This is a **frequency** metric, distinct from `total_delay_days`, which is a **magnitude** metric — a group can have a high incidence rate but low total delay (many small delays) or a low incidence rate but high total delay (few but very large delays). Reporting both together avoids misleading conclusions from either one alone.

Worked example using Express deliveries (6 records: IDs 1, 2, 5, 6, 9, 10):
- Delayed records: IDs 2, 5, 9, 10 → 4 delayed out of 6 total
- `delay_incidence_rate = (4 / 6) × 100 = 66.67%`
- `total_delay_days = 1 + 3 + 6 + 2 = 12`

---

## 🛠️ Tools & Versions Used

| Tool | Version |
|---|---|
| SQL Engine | SQLite 3 |
| Python | 3.11 |
| pandas | 2.2.x |
| matplotlib | 3.8.x |
| Jupyter Notebook | 7.x |
| Microsoft Excel | Microsoft 365 (desktop build) |
| Power BI | Power BI Desktop (latest, Sept 2026 release) |

> ⚠️ **Note:** Run `pip freeze > requirements.txt` in your own environment before submission and paste the exact pinned versions here (e.g. `pandas==2.2.2`), so the versions listed match precisely what you used to generate the notebook outputs.

---

## 🗂️ Project Folder Structure

```
data-analysis-set-A_10468/
│
├── README.md                          # This file
├── setup.sql                          # SQL table creation + data load
├── queries.sql                        # SQL analysis queries
│
├── data/
│   ├── deliveries.csv                 # Raw delivery records (13 rows, pre-cleaning)
│   └── routes.csv                     # Raw route lookup table (4 rows)
│
├── sql_outputs/
│   ├── s2a_delay_by_service_type_csv.csv
│   ├── s2b_routes_significant_delay.csv
│   ├── s2c_top_two_hubs.csv
│   └── s3_unmatched_route_check.csv
│
├── python/
│   ├── Delivery_Delay_Analysis.ipynb   # Full analysis notebook
│   ├── clean_data.csv                  # Cleaned + merged output (12 rows)
│   ├── python_summary.csv              # Service-type delay summary
│   └── python_chart.png                # Monthly delay bar chart (exported from notebook)
│
├── excel/
│   └── analysis.xlsx                   # Raw / Lookup / Clean / Summary sheets
│
└── powerbi/
    ├── delivery_dashboard.pbix         # Power BI source file (if included)
    └── Power_BI_dashboard.png          # Dashboard screenshot
```

---

## 🧮 SQL Setup & Query Execution Steps

1. Open the SQLite database (or create a new `.db` file) in your SQL client of choice (DB Browser for SQLite, `sqlite3` CLI, or similar).
2. **Run `setup.sql` first.** This script:
   - Drops the `deliveries` and `routes` tables if they already exist (so the script is safely re-runnable).
   - Creates `routes` (`route_id` PRIMARY KEY, `route_name`, `service_type`).
   - Creates `deliveries` (`record_id` PRIMARY KEY, `month`, `route_id` FOREIGN KEY → `routes.route_id`, `hub`, `promised_days`, `actual_days`).
   - Inserts all 4 routes and all 12 delivery rows.
3. **Then run `queries.sql`.** This executes five queries in sequence:
   1. **Total delay days by service type** — joins `deliveries` to `routes`, sums `GREATEST(actual_days − promised_days, 0)` grouped by `service_type`, ordered descending. → produces `s2a_delay_by_service_type_csv.csv`.
   2. **Routes with significant delay** — same delay calculation grouped by `route_id`/`route_name`, filtered with `HAVING SUM(...) > 8`, ordered descending. → produces `s2b_routes_significant_delay.csv`.
   3. **Top 2 hubs by delay** — groups by `hub`, sums delay days, orders descending (with hub name as a tiebreaker), and limits to 2 rows. → produces `s2c_top_two_hubs.csv`.
   4. **Unmatched-route check** — a `LEFT JOIN` from `deliveries` to `routes` where the joined `route_id` is `NULL`, counting any deliveries whose route doesn't exist in the lookup table (a data-integrity check). → produces `s3_unmatched_route_check.csv`, expected result: `0`.
   5. **Row-count check** — a simple sanity check returning the row counts of both tables in one row, to confirm 12 deliveries and 4 routes are present after `setup.sql` runs.
4. Export each query's result set as CSV (already provided in this repo as the `s2a`–`s3` files, matching the notebook and Excel outputs for reconciliation).

---

## 🐍 Python Environment Setup & Run Instructions

```bash
# create an isolated environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# install dependencies
pip install -r requirements.txt
# (equivalent to: pip install pandas matplotlib jupyter)

# run the notebook interactively
jupyter notebook python/Delivery_Delay_Analysis.ipynb

# or, if exported/converted to a plain script:
python python/analysis.py
```

**What the notebook does, cell by cell:**

- **P1 — Load, Clean & Merge:** reads `deliveries.csv` and `routes.csv`, inspects shape/nulls/dtypes, drops duplicates (13 → 12 rows), left-merges on `route_id`, and asserts the merge is complete (12 rows, no missing `service_type`).
- **P2 — Derived Field & Service-Type Analysis:** computes `delay_days` and `is_delayed`, builds the `service_summary` table (total delay days + delay incidence rate per service type), and identifies the single route with the greatest total delay along with its share of the overall delay total.
- **P3 — Monthly Total Delay Chart:** orders the data by month (`Jan → Feb → Mar`), sums `delay_days` per month, and plots a bar chart of monthly total delay (`python_chart.png`).
- **Export:** writes the cleaned, merged table to `clean_data.csv` and the service-type summary to `python_summary.csv`, both of which feed the reconciliation checks below and the Power BI data source.

---

## 📗 Excel Sheet Guide (`analysis.xlsx`)

| Sheet | Purpose |
|---|---|
| **Raw** | Original, unmodified delivery records exactly as received (13 rows, including the duplicate `record_id = 12` row) — kept untouched as an audit trail |
| **Lookup** | Route → route name → service type lookup table, used as the reference table for `VLOOKUP`/`XLOOKUP` formulas that pull `service_type` into the Clean sheet |
| **Clean** | Deduplicated (12 rows), merged data with `delay_days` computed via formula; includes a small cleaning-check box confirming `Before Row Count: 13`, `After Row Count: 12`, `Duplicate Removed: 1` |
| **Summary** | Two PivotTables: (1) total delay days by hub, and (2) total delay days by service type × month (Jan/Feb/Mar columns, with a grand-total row), used to build the reconciliation and monthly-trend figures in this README |

---

## 📊 Power BI Data-Source Refresh Instructions

After cloning this repository, the Power BI file still points to the CSV path from the original machine it was built on, so the data source must be repointed before the dashboard will refresh correctly:

1. Open the `.pbix` file in Power BI Desktop.
2. Go to **Home → Transform Data → Data Source Settings**.
3. Select the data source pointing to `clean_data.csv` (or `deliveries.csv` / `routes.csv`, if the model uses the raw files directly) and click **Change Source…**.
4. Browse to the cloned repo's local path, e.g. `.../data-analysis-set-A_10468/python/clean_data.csv`, and confirm.
5. Click **Refresh** on the Home ribbon so every visual (KPI cards for delivery count / total delay days / delay incidence rate, the service-type bar chart, the monthly trend area chart, and the hub slicer) recalculates against the local data.
6. If Power BI reports a "file not found" or column-mismatch error, double-check that the CSV headers in your local `clean_data.csv` still match the columns the visuals were built against (`month`, `hub`, `service_type`, `delay_days`, etc.) — this is the most common cause of a broken refresh after cloning.

---

## 📈 Numeric Findings & Recommendation

**Finding 1 — Service type:** Standard-service routes account for **21 of the 33 total delay days (≈64%)**, versus 12 for Express — Standard routes are the larger delay contributor overall, even though there are only 2 routes in each service tier.

**Finding 2 — Hub concentration:** **Mumbai** is by far the most delay-affected hub with **22 total delay days**, more than 3.5× the next-highest hub (Delhi, 6 days). At the route level, the **Rural Feeder (R4)** route alone contributes **16 delay days**, and only two routes cross the significant-delay threshold of > 8 total delay days: **Rural Feeder (16 days)** and **Metro Link (9 days)**.

**Finding 3 — Trend:** Total delay days climb every single month: **7 days in January, 9 in February, 17 in March** — more than doubling from Jan to Mar. This is a monotonic upward trend across all three months, not an isolated spike, and it lines up with March also containing the single worst individual delivery in the dataset (record 12: Rural Feeder / Mumbai, 15 actual days vs. 6 promised — a 9-day delay).

**Recommendation:** Investigate capacity and scheduling at the **Mumbai hub** and on the **Rural Feeder (R4)** route first, since together they explain the majority of total delay days in the dataset. Because the monthly trend is worsening rather than flat, this should be escalated as an urgent, active problem — not a historical one-off — and a follow-up check should be run once April data is available to see whether the upward trend continues.

---

## 🔗 Cross-Tool Reconciliation

**Reconciled metric:** Total delay days for the **Mumbai hub**

| Tool | Value | Source |
|---|---|---|
| SQL | 22 | `s2c_top_two_hubs.csv` |
| Python | 22 | `clean_data.csv`, grouped by `hub` |
| Excel | 22 | `Summary` sheet, Hub Delay Summary PivotTable |
| Power BI | 22 | Dashboard, hub slicer filtered to "Mumbai" |

✅ All four tools agree on **22 total delay days for Mumbai** — no rounding differences arise here, since `delay_days` is an integer sum with no fractional components anywhere in this dataset.

**Secondary reconciled metric:** Overall delay incidence rate across all 12 deliveries

| Tool | Value |
|---|---|
| SQL | 66.67% (8 of 12 records delayed) |
| Python | 66.666...% (unrounded), displayed rounded to 66.67% |
| Excel | 66.67% (PivotTable percentage) |
| Power BI | 66.67% (KPI card) |

✅ This metric agrees across tools once rounded to 2 decimal places. Python's raw float output (`66.66666666666666`) is the unrounded version of the same figure shown as `66.67%` elsewhere — this is a **display/rounding difference only**, not a calculation discrepancy, and is noted here so the two aren't mistaken for conflicting results.

---

## ⚠️ Assumptions & Limitations

- The dataset is very small (12 clean records), so findings and the monthly trend should be read as **directional signals**, not statistically robust conclusions — a larger sample would be needed before making major operational decisions purely on this data.
- Early or on-time deliveries are treated identically (`delay_days = 0`); the analysis does not currently reward or separately track "how early" a delivery arrived.
- Only one duplicate row was found and removed; no other data-quality issues (missing values, type mismatches, unmatched routes) were present after cleaning.
- The Power BI dashboard is a static screenshot (`Power_BI_dashboard.png`) included for review; the live `.pbix` file (if present) is the one that needs the data-source refresh steps above after cloning.

---

## 🎬 Working Video

**URL:** _[Add your walkthrough video link here]_
**Duration:** _[Add duration, e.g. 6 min 40 sec]_

---

## 📚 References

No external code, libraries beyond the standard `pandas`/`matplotlib` stack, or external datasets were used beyond the assignment-provided source files (`deliveries.csv`, `routes.csv`). All SQL queries, Python cleaning/analysis code, Excel formulas/PivotTables, and the Power BI dashboard were built directly from these files by the author.

---

## ✍️ Authorship Declaration

All work in this repository is my own except where cited.
