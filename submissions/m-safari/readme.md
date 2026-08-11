# Customer Data Cleaning

A Jupyter notebook project that cleans a raw customer dataset (`First Dataset.xlsx`) — a table of ~60 customers with fields like age, city, membership tier, purchases, spending, and satisfaction score.

## What it does

- **Explore** — loads the raw data and reviews summary statistics and info.
- **Assess quality** — checks for incorrect data types, inconsistent categorical values, duplicate rows, missing values, outliers (via boxplots and a 2-std rule), and integrity violations.
- **Clean** — de-duplicates rows, fills missing values, converts columns to proper types (categories, datetime, bool, unsigned ints), and reassigns customer gender using a Persian names lookup table (`persian_names.csv`), with manual fixes for unmatched names.
- **Reduce redundancy** — drops `avg_order_value` since it's derivable from other columns.
- **Export** — writes the cleaned dataset to `../data/cleaned.xlsx`.

## Project structure

```
├── data-cleaning.ipynb      # main notebook
├── raw_data/
│   ├── First Dataset.xlsx
│   └── persian_names.csv
└── data/
    └── cleaned.xlsx          # output
```

## Requirements

- Python 3.12
- pandas, numpy, matplotlib, openpyxl

## Resources

- [https://www.kaggle.com/datasets/titanz123/persian-names]
- [https://www.geeksforgeeks.org/data-analysis/data-cleaning-introduction/]
