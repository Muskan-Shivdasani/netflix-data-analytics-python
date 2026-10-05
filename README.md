# Netflix Data Analytics - Auspify Technologies Internship

End-to-end analysis of a Netflix content dataset (8,790 titles) as part of the
Auspify Technologies "Data Analysis Using Python" internship.

## Project Structure

```text
netflix-data-analytics-python/

data/
  raw/        original, untouched dataset
  cleaned/    cleaned dataset (output of Task 1, used by all later tasks)

notebooks/
  01_data_cleaning.ipynb
  02_content_type_analysis.ipynb
  03_country_analysis.ipynb

screenshots/  screenshots documenting all tasks

README.md
```
## Task 1: Data Cleaning & Preparation

**Objective:** prepare the raw Netflix dataset for reliable downstream analysis.

**Key findings and decisions:**
- `director` and `country` contained a `'Not Given'` placeholder instead of true
  nulls (29.4% and 3.3% of rows respectively) - converted to proper `NaN` so
  aggregations exclude them automatically, rather than dropping the rows.
- 3 pairs of rows (6 total) were exact duplicates except for `show_id`, and
  each pair's `title` field had been corrupted into date-like text
  (e.g. `'15-Aug'`) prior to receipt, with the original titles unrecoverable -
  confirmed via `pd.to_datetime()` parsing and file-metadata verification.
  These 6 rows were dropped.
- `date_added` was stored as text; converted to a proper datetime type.
- One title had a hidden trailing space; stripped.
- Rating labels `UR` (Unrated) and `NR` (Not Rated) were merged into `NR`,
  since they represent the same category under different naming conventions.
- `'Soviet Union'` and `'West Germany'` were kept as-is in `country`
  (2 rows total) - remapping to modern country names would misrepresent
  historical fact for a negligible row count.

**Result:** 8,784 clean rows, saved to `data/cleaned/netflix_cleaned.csv`.

## Tools
Python, pandas, Jupyter (VS Code)



## Task 2: Content Type Analysis Dashboard

**Objective:** analyze the distribution of Movies vs. TV Shows in the cleaned dataset.

**Approach:**
- Loaded `data/cleaned/netflix_cleaned.csv` and re-converted `date_added` to
  datetime, since CSV format does not preserve pandas dtypes across a save/load
  cycle.
- Calculated raw counts and percentage split using `value_counts()`.
- Built a two-panel dashboard (bar chart + pie chart) in matplotlib/seaborn,
  using a consistent Netflix-red/blue color scheme for Movie vs. TV Show
  across both charts.

**Key findings:**
- Movies: 6,122 titles (69.7%)
- TV Shows: 2,662 titles (30.3%)
- Movies outnumber TV Shows by roughly 2.3:1.
- This base distribution should be kept in mind in later tasks, since any
  type-level skew will naturally bias raw counts in country/rating breakdowns.

**Output:**
- `screenshots/Task2_01.content_type_distribution.png` - bar + pie dashboard
- `screenshots/Task2_02.Loaded_cleaned_dataset.png` 
- `screenshots/Task2_03.date_type_conversion.png`
- `screenshots/Task2_04.bar_and_chart_configuration.png`

## Tools
Python, pandas, matplotlib, seaborn


## Task 3: Country Wise Netflix Content Analysis

**Objective:** analyze Netflix content availability and distribution across countries.

**Approach:**
- Loaded `data/cleaned/netflix_cleaned.csv`, re-converted `date_added` to datetime.
- Counted titles by country using `value_counts()`, selected a top-15 cutoff
  based on a natural breakpoint in the data (all 15 have 100+ titles).
- Built three charts: a full top-15 ranking, a second version excluding the
  US outlier to make mid-tier differences visible, and a stacked bar chart
  breaking down Movie vs. TV Show share by country using `.groupby()`.


## Key Findings

- **Content production is highly concentrated**: the top 15 countries account
  for 81.8% of all titles in the cleaned dataset, meaning the remaining ~70
  countries combined contribute less than a fifth of total content.
- **The United States alone accounts for 36.9%** of all titles - more than a
  third of the entire titles from a single country, making it a clear
  outlier that was excluded from a second chart to make mid-tier differences
  (ranks 2-15) visible.
- **India has the most extreme Movie/TV Show split among top-producing
  countries**: 92.3% Movie vs. just 7.7% TV Show - the strongest Movie lean
  of any country in the top 10, well above the overall platform average of
  69.7%.
- **Pakistan, South Korea, and Japan invert the platform's overall trend**,
  skewing TV Show-heavy instead of Movie-heavy - Pakistan most sharply, at
  83.1% TV Show vs. 16.9% Movie.
- **Country data is incomplete**: 287 titles (3.3% of the cleaned dataset)
  have no recorded country and are excluded from this entire analysis.

  **Output:**
- `screenshots/Task3_01.top15_countries.png` - top 15 ranking
- `screenshots/Task3_02.top_countries_excl_us.png` - ranking excluding US outlier
- `screenshots/Task3_03.movie_tv_share_by_country.png` - Movie/TV Show split by country

## Tools
  Python, pandas, matplotlib, seaborn


## Author
Muskan Shivdasani
[LinkedIn](https://www.linkedin.com/in/muskan-shivdasani-369893277/) · [GitHub](https://github.com/Muskan-Shivdasani)
