# Weaving the Data Threads of Maji Ndogo (Part 3)

This project analyzes water-quality audit data in `md_water_services` to identify mismatches between field surveyors and auditor assessments, then traces possible patterns at employee level.

## Project Objectives

- Compare `auditor_report.true_water_source_score` against `water_quality.subjective_quality_score`.
- Quantify agreement/mismatch between auditor and surveyor scores.
- Link mismatches to employees through the `visits` table.
- Identify employees with above-average mistake counts.
- Inspect `statements` text for possible patterns (for example, references to `cash`).

## Main Analysis Notebook

- Notebook: [project3.ipynb](project3.ipynb)
- Database connection uses `ipython-sql` + `PyMySQL`.
- SQL is executed via `%%sql` cells.

## Data Model / ERD

This repository includes an exported ERD of the `md_water_services` database.

- Editable diagram: [md_water_services_erd.drawio](md_water_services_erd.drawio)
- SVG export: [md_water_services_erd.drawio.svg](md_water_services_erd.drawio.svg)
- MySQL Workbench model: [Auditor_report/md_water_services_erd.mwb](Auditor_report/md_water_services_erd.mwb)

### ERD (SVG)

![md_water_services ERD](md_water_services_erd.drawio.svg)

## Environment Setup

1. Create and activate a virtual environment.
2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Ensure a local MySQL database exists:
	 - Host: `127.0.0.1`
	 - Port: `3306`
	 - Database: `md_water_services`
4. Update credentials in `project3.ipynb` connection cell if needed:

```python
%sql mysql+pymysql://<username>:<password>@127.0.0.1:3306/md_water_services
```

## Analysis Workflow (from notebook)

1. Validate and inspect source tables (`auditor_report`, `visits`, `water_quality`, `employee`, `water_source`).
2. Join `auditor_report` → `visits` → `water_quality` to compare auditor vs surveyor scores.
3. Filter to first-visit records (`visit_count = 1`) to avoid duplicate follow-up noise.
4. Build `Incorrect_records` view for reusable mismatch analysis.
5. Compute per-employee mistake counts and average mistakes across employees.
6. Flag suspect employees whose mistake count is above average.
7. Review suspect records and filter `statements` for potential signals (for example `LIKE '%cash%'`).

## Key Findings Captured in Notebook

- Most records are consistent between auditors and surveyors.
- Notebook notes indicate roughly **94%** of audited records were correct.
- A subset of employees were flagged with above-average mismatch counts.
- Current notebook summary explicitly lists four names in the conclusion section:
	- Zuriel Matembo
	- Malachi Mavuso
	- Bello Azibo
	- Lalitha Kaburi

These findings are indicators for investigation, not final proof of misconduct.

## Repository Structure

```text
.
├── project3.ipynb
├── README.md
├── requirements.txt
├── schema_extract.json
├── md_water_services_erd.drawio
└── Auditor_report/
		├── Auditor_report.csv
		├── md_water_services_erd.mwb
		└── md_water_services_erd.mwb.bak
```

## Notes

- CTEs in MySQL are scoped to a single query; reusable logic is implemented as the `Incorrect_records` view in the notebook.
- If column aliases contain spaces, use backticks when referencing them in later queries.
