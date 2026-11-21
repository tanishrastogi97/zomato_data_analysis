# Zomato Restaurant Data Analysis

Data cleaning pipeline (Python / pandas) and an interactive dashboard (Tableau) for exploring Zomato restaurant listings: ratings, costs, locations, votes, and online-order / table-booking availability.

## Project Structure

```
.
├── zomato_cleaning_notebook.ipynb   # Cleaning workflow, step by step (Jupyter / Colab)
├── zomato_cleaning_notebook.py      # Same workflow as a plain Python script
├── Zomato_dashboard.twb             # Tableau workbook with 6 analysis sheets
├── requirements.txt                 # Python dependencies
└── README.md
```

Input (not included in the repo): `zomato.csv`, the raw restaurant dataset.
Output (generated): `zomato_data_analysis.csv`, the cleaned dataset.

## Setup

Requires Python 3.9+ (developed on 3.11).

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

## Usage

1. Place the raw `zomato.csv` in the project root (same folder as the notebook).
2. Run the cleaning step using either option:

   ```bash
   # Option A: script
   python zomato_cleaning_notebook.py

   # Option B: notebook
   jupyter lab zomato_cleaning_notebook.ipynb
   ```
3. The cleaned file `zomato_data_analysis.csv` is written to the project root.

The notebook also runs in Google Colab; upload `zomato.csv` to the session first.

## Data Cleaning Steps

| Step | Action |
|------|--------|
| 1 | Load `zomato.csv` |
| 2 | Drop columns not needed for analysis: `url`, `phone`, `rest_type`, `dish_liked`, `reviews_list`, `menu_item`, `listed_in(city)` |
| 3 | Rename `approx_cost(for two people)` → `two_people_cost`, `listed_in(type)` → `type_of_restaurant`, `rate` → `rating` |
| 4 | Drop rows with missing `location`, `cuisines`, or cost |
| 5 | Strip commas from cost (e.g. `1,200` → `1200`) and convert to integer |
| 6 | Convert cost for two into `cost_per_person` (divide by 2) |
| 7 | Clean `rating`: `NEW` and `-` become NaN, and values like `4.1/5` become `4.1` |
| 8 | Fill missing ratings with the mean rating |
| 9 | Save to `zomato_data_analysis.csv` |

### Cleaned dataset columns

`address`, `name`, `online_order`, `book_table`, `rating`, `votes`, `location`, `cuisines`, `cost_per_person`, `type_of_restaurant` (plus the pandas index, saved as the first column).

## Tableau Dashboard

`Zomato_dashboard.twb` contains six worksheets:

- Avg rating per category
- Heatmap
- Location based costing
- Rating per booking type
- Votes vs type of restaurants
- Sheet 6 (additional view)

**Opening the workbook:** the workbook's data source points to an Excel file (`zomato_clean_data_5july.xlsx`) at a local path on the original author's machine, so Tableau will prompt you to locate the data. To reconnect, either:

- Convert the cleaned CSV to Excel (sheet name `zomato_data`) and point Tableau at it, or
- Use *Edit Data Source* in Tableau and connect to `zomato_data_analysis.csv` directly.

Note that the workbook's extract references 10,000 rows (`A1:K10001`), so it may have been built from a sampled subset of the cleaned data.

## Known Limitations

- **Index column:** `to_csv()` is called without `index=False`, so the output includes an extra unnamed index column (it appears as `Unnamed: 0` in Tableau). Use `index=False` if you don't want it.
- **Duplicates:** Duplicate restaurant rows are not removed.
- **Mean imputation:** Missing ratings are filled with the global mean, which can pull unrated or new restaurants toward the average. Consider leaving them as null for rating-sensitive analysis.
- **Currency:** Costs are as listed in the source data (assumed INR).

## Tech Stack

Python · pandas · NumPy · Jupyter · Tableau
