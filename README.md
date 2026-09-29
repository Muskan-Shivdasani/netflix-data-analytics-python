# netflix-data-cleaning-python
End-to-end EDA and business insights on a Netflix content dataset using Python (pandas, matplotlib/seaborn) — Auspify Technologies internship project.

# Netflix Data Analytics — Auspify Technologies Internship

End-to-end analysis of a Netflix content dataset (8,790 titles) as part of the
Auspify Technologies "Data Analysis Using Python" internship.

## Project Structure
netflix-data-analytics-python/
├── data/
│   ├── raw/          # original, untouched dataset
│   └── cleaned/       # output of Task 1
├── notebooks/
│   └── 01_data_cleaning.ipynb
└── README.md

## Task 1: Data Cleaning & Preparation

**Objective:** prepare the raw Netflix dataset for reliable downstream analysis.

**Key findings and decisions:**
- `director` and `country` contained a `'Not Given'` placeholder instead of true
  nulls (29.4% and 3.3% of rows respectively) — converted to proper `NaN` so
  aggregations exclude them automatically, rather than dropping the rows.
- 3 pairs of rows (6 total) were exact duplicates except for `show_id`, and
  each pair's `title` field had been corrupted into date-like text
  (e.g. `'15-Aug'`) prior to receipt, with the original titles unrecoverable —
  confirmed via `pd.to_datetime()` parsing and file-metadata verification.
  These 6 rows were dropped.
- `date_added` was stored as text; converted to a proper datetime type.
- One title had a hidden trailing space; stripped.
- Rating labels `UR` (Unrated) and `NR` (Not Rated) were merged into `NR`,
  since they represent the same category under different naming conventions.
- `'Soviet Union'` and `'West Germany'` were kept as-is in `country`
  (2 rows total) — remapping to modern country names would misrepresent
  historical fact for a negligible row count.

**Result:** 8,784 clean rows, saved to `data/cleaned/netflix_cleaned.csv`.

## Tools
Python, pandas, Jupyter (VS Code)

## Author
Muskan Shivdasani
[LinkedIn](https://www.linkedin.com/in/muskan-shivdasani-369893277/) · [GitHub](https://github.com/Muskan-Shivdasani)