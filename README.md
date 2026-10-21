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
  04_trend_analysis_by_release_year.ipynb

screenshots/  screenshots documenting all tasks

README.md
```
## Task 1: Data Cleaning & Preparation

**Objective:** prepare the raw Netflix dataset (8,790 rows, 10 columns) for reliable
downstream analysis.

**Approach:** inspected structure and data types, hunted for hidden missing values,
checked duplicates and formatting inconsistencies, standardized labels, applied the
cleaning steps in one consolidated cell, verified the result with assertions, and
exported the cleaned file.

**Key findings and decisions:**
- **Hidden missing values:** `info()` reported zero nulls, but `director` and
  `country` contained a `'Not Given'` placeholder (2,588 rows / 29.4% and 287 rows /
  3.3% respectively). Converted to proper `NaN` so aggregations exclude them
  automatically. Rows were kept, since they hold valid values in other columns.
- **Date-formatted titles:** three titles (`'15-Aug'`, `'22-Jul'`, `'9-Feb'`) look like
  dates. A title can legitimately be a date, and the same titles appeared after
  re-downloading the raw file, so titles were left unchanged.
- **Duplicates:** `df.duplicated()` returned 0 because `show_id` is unique in every row.
  Ignoring `show_id` revealed 3 duplicate pairs (the date-formatted titles above). After
  stripping whitespace, a fourth pair (`'Consequences'`) appeared, previously hidden by
  a trailing space. One copy of each pair was kept and 4 rows were removed.
- `date_added` was stored as text; converted to datetime with an explicit format
  (`%m/%d/%Y`).
- One title had a hidden trailing space; stripped (this is what exposed the fourth
  duplicate pair).
- Rating labels `UR` (Unrated) and `NR` (Not Rated) were merged into `NR`, since they
  represent the same category under different names (79 vs 3 rows).
- `'Soviet Union'` and `'West Germany'` were kept as-it-is in `country` (2 rows total) -
  remapping to modern country names would misrepresent historical fact.
- **Left unchanged:** `duration` mixes units (minutes and seasons), and `director` and
  `listed_in` hold multiple values per cell. These would need splitting before any
  per-genre or per-director analysis.
- **Verification:** assertions confirm unique `show_id`, no duplicate records, a valid
  datetime `date_added` with no missing values, and a plausible `release_year` range.

**Result:** 8,786 clean rows, saved to `data/cleaned/netflix_cleaned.csv`.


## Task 2: Content Type Analysis Dashboard

**Objective:** analyze the distribution of Movies vs. TV Shows in the cleaned dataset.

**Approach:**
- Loaded `data/cleaned/netflix_cleaned.csv` with `parse_dates=['date_added']`, since
  CSV format does not preserve pandas dtypes across a save/load cycle.
- Calculated raw counts and the percentage split using `value_counts()` and
  `value_counts(normalize=True)`.
- Verified with assertions that the two types account for every row and that `type`
  contains no missing values.
- Built a two-panel dashboard in matplotlib/seaborn: a bar chart for exact counts and a
  pie chart for proportions. Colors are mapped to labels with a dictionary
  (Netflix red for Movie, blue for TV Show), so each type keeps the same color in
  both charts regardless of data order.

**Key findings:**
- Movies: 6,123 titles (69.7%)
- TV Shows: 2,663 titles (30.3%)
- Movies outnumber TV Shows by roughly 2.3:1.
- This base distribution should be kept in mind in later tasks, since any
  type-level skew will naturally bias raw counts in country/rating breakdowns.

**Limitation:** the analysis counts titles, not viewing time or catalogue effort - one
multi-season series counts the same as one short film.

**Output:**
- `screenshots/Task2_01.content_type_distribution.png` - bar + pie dashboard
- `screenshots/Task2_02.Loaded_cleaned_dataset.png` - cleaned dataset loaded with
  `date_added` parsed as datetime
- `screenshots/Task2_03.Bar_and_chart_configuration.png` - dashboard code

## Tools
Python, pandas, matplotlib, seaborn


## Task 3: Country Wise Netflix Content Analysis

**Objective:** analyze Netflix content availability and distribution across countries.

**Approach:**
- Loaded `data/cleaned/netflix_cleaned.csv` with `parse_dates=['date_added']`.
- Quantified missing country information (287 titles) before analysis, since
  `value_counts()` excludes missing values silently.
- Counted titles by country using `value_counts()` and selected the top 15 countries
  by title count (the 15th has 104 titles; the 16th, China, has 100).
- Calculated shares with both denominators: all titles, and titles with a known country.
- Built three charts: a top-15 ranking, the same ranking excluding the US outlier to
  make mid-tier differences visible, and a stacked bar chart of Movie vs. TV Show
  share for the top 10 countries, built with `.groupby()` and normalized within each
  country so countries of different sizes compare fairly. The last chart shows the
  platform-wide Movie share as a reference line and the number of titles (n) behind
  each country.

**Key findings:**
- **Content production is highly concentrated:** the top 15 countries account for
  81.8% of all titles (84.6% of titles with a known country). The other 70 countries
  contribute 14.9%, and 3.3% of titles have no country.
- **The United States alone accounts for 36.9%** of all titles (3,240; 38.1% of titles
  with a known country), making it a clear outlier that was excluded from a second
  chart. India is a distant second (1,056).
- **India has the most extreme Movie/TV Show split in the top 10:** 92.3% Movie vs.
  7.7% TV Show, well above the platform-wide Movie share of 69.7%.
- **Pakistan, South Korea, and Japan invert the platform's overall trend**, skewing
  TV Show-heavy: Pakistan 83.1%, South Korea 77.1%, Japan 66.4%.
- **Country data is incomplete:** 287 titles (3.3%) have no recorded country and are
  excluded from this analysis.

**Business interpretation:**
- The catalogue is heavily US-centric; the largest non-US contributors are India,
  the UK, Pakistan, and Canada.
- Pakistan, South Korea, and Japan lean towards series, while India leans towards
  films, so the Movie/TV Show mix differs by country.

**Limitations:**
- Counts reflect catalogue titles, not viewership or subscribers.
- Each title carries a single country, so co-productions are not captured.
- The 287 titles with no country could change the picture if their origin were known.

**Output:**
- `screenshots/Task3_01.top15_countries.png` - top 15 ranking
- `screenshots/Task3_02.top_countries_excl_us.png` - ranking excluding US outlier
- `screenshots/Task3_03.movie_tv_share_by_country.png` - Movie/TV Show split by country

## Tools
Python, pandas, matplotlib, seaborn


## Task 4: Trend Analysis by Release Year

**Objective:** analyze how the Netflix catalogue is distributed across original release years.

**Approach:**
- Loaded `data/cleaned/netflix_cleaned.csv` with `parse_dates=['date_added']` and asserted
  the expected row count (8,786) so a stale cleaned file cannot slip through.
- Counted titles per release year and reindexed to every year in the range, filling
  years with no titles with 0.
- Printed the full year-by-year table in 30-year blocks and identified three volume
  phases from it. Each phase is checked in code so its description is verifiable:
  - 1925-1978: mostly single digits per year (exception: 1973, 10 titles)
  - 1979-2007: mostly double digits per year (exception: 1987, 8 titles)
  - 2008-2021: 100+ titles every year, with no exception
- Calculated the year-over-year change in titles and in percent, using both because
  percentage change misleads on small bases.
- Built two charts: a three-panel view of the phases (each with its own y-axis) and a
  single-scale view of 1979-2021 with the peak and the start of the high-volume
  phase marked. The partial year 2021 is highlighted in both.

**Key findings:**
- The catalogue is dominated by recent releases: 7,741 titles (88.1%) were released
  2008-2021. Phase 1 holds only 111 titles (1.3%) and Phase 2 holds 934 (10.6%).
- Every year from 2008 has 100 or more titles; 2017, 2018 and 2019 exceed 1,000.
- The steepest rises were 2015 (+204 titles) and 2016 (+346). The peak is 2018 with 1,144 titles.
- 2019 (1,029) and 2020 (953) fall below the peak.
- 2021 (592 titles) is a partial year: the dataset only includes titles added through
  25 September 2021, so it cannot be read as a decline.

**Interpretation (hypotheses; the dataset cannot prove causes):**
- The catalogue is overwhelmingly modern, consistent with a platform that expanded its
  library mainly after the mid-2010s.
- The 2019-2020 dip could reflect slower production, licensing changes or reporting lag.

**Limitations:**
- `release_year` is a title's original release year, not the year Netflix produced it.
  Most of the catalogue is licensed content, so this is not a measure of Netflix production.
- The data is a catalogue snapshot: older titles may have left the platform, so sparse
  early years show what is available, not how much was made.
- 2021 is incomplete.

**Output:**
- `screenshots/Task4_01.yearly_release_trend_three_eras.png` - three volume phases
- `screenshots/Task4_02.yearly_release_trend_1979_2021.png` - 1979-2021 on one scale

## Tools
Python, pandas, matplotlib, seaborn


## Author
Muskan Shivdasani
[LinkedIn](https://www.linkedin.com/in/muskan-shivdasani-369893277/) · [GitHub](https://github.com/Muskan-Shivdasani)
