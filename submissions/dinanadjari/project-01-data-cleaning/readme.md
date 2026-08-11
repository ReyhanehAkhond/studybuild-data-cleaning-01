# Project 01 – Data Cleaning

## 📌 Project Overview

This project focuses on cleaning and validating a customer dataset using **Python** and **Pandas**. The objective is to identify data quality issues, apply appropriate cleaning techniques, and produce a reliable dataset suitable for further analysis.

### Project Structure

| Folder | Description |
|--------|-------------|
| `data/` | Original dataset (`First Dataset.xlsx`) and cleaned dataset (`cleaned_dataset.xlsx`) |
| `notebook/` | Jupyter notebook containing the complete data cleaning process |
| `report/` | PDF report summarizing the analysis |
| `README.md` | Project documentation |

---

# Data Cleaning Process

## 1. Problems Found in the Dataset

During the inspection of the dataset, the following issues were identified:

| Problem | Description |
|---------|-------------|
| Missing Values | Missing values existed in the `age` and `total_spending` columns. |
| Duplicate Records | Duplicate rows were present in the dataset. |
| Invalid Age Values | The `age` column contained unrealistic values (e.g., 145 years old). |
| Incorrect Gender Values | The `gender` column contained inconsistent values that did not match customer names. |
| Illogical Purchase Data | Some records had `returned_items` greater than `purchase_count`. |
| Inconsistent Spending Data | One record had `total_spending = 0` while `avg_order_value` was non-zero. |
| Data Type Issues | The `signup_date` column was stored as text instead of datetime and the `age` column was stored as float instead of integer. |
| Categorical Encoding | The `discount_used` column contained "Yes"/"No" values instead of numeric values. |

---

## 2. Changes Applied

The following modifications were performed:

| Change | Action |
|--------|--------|
| Removed duplicate records | Deleted duplicated rows. |
| Fixed invalid ages | Replaced ages below **10** or above **90** with `NaN`. |
| Filled missing values | Replaced missing values in `age` and `total_spending` using the median. |
| Corrected gender values | Updated the `gender` column using a dictionary based on customers' first names. |
| Fixed illogical purchase data | Replaced `purchase_count` and `returned_items` with `NaN` where returned items exceeded purchases. |
| Removed invalid record | Deleted the record with `customer_id = 1024` because it contained multiple logical inconsistencies. |
| Converted data type | Converted `signup_date` to `datetime`, and `age` to `int`. |
| Encoded categorical values | Converted `discount_used` from `Yes/No` to `1/0`. |

---

## 3. Why These Changes Were Made

Each cleaning step was performed to improve data quality and analysis reliability.

| Cleaning Step | Reason |
|--------------|--------|
| Removing duplicates | Prevents duplicate observations from affecting analysis. |
| Replacing unrealistic ages | Invalid values can distort descriptive statistics and visualizations. |
| Filling missing values with the median | The median is robust against outliers and preserves the distribution better than the mean. |
| Correcting gender values | Ensures consistency between customer names and recorded gender. |
| Handling logical inconsistencies | Prevents impossible business scenarios from influencing the analysis. |
| Removing the faulty record | The record with `customer_id = 1024` contained multiple errors that could not be corrected reliably. |
| Converting date format | Enables date-based operations and analysis. |
| Encoding categorical values | Makes the data suitable for statistical analysis and machine learning models. |

---

## 4. Missing Value Management

Missing values were handled using two approaches:

| Situation | Method |
|-----------|--------|
| Invalid age values | Replaced with `NaN`. |
| Existing missing values in `age` and `total_spending` | Filled using the median of each column. |
| Illogical purchase information | Replaced with `NaN` because there was no reliable way to recover the true values. |

The median was selected because it is less sensitive to extreme values than the mean.

---

## 5. Duplicate Detection

Duplicate records were checked using:

```python
dataset.duplicated()
```

After verification, duplicated rows were removed using:

```python
dataset.drop_duplicates(inplace=True)
```

---

## 6. Data Type Changes

| Column | Original Type | New Type |
|---------|---------------|----------|
| `signup_date` | Object (String) | Datetime |
| `discount_used` | Yes / No | 1 / 0 |
| `age` | float | int |

---

## 7. Outliers and Illogical Values

The following abnormal values were detected:

| Column | Issue | Action |
|---------|-------|--------|
| `age` | Values below 10 or above 90 | Replaced with `NaN` |
| `purchase_count` & `returned_items` | Returned items exceeded purchases | Replaced with `NaN` |
| `total_spending` & `avg_order_value` | Contradictory values in one record | Removed the faulty record (`customer_id = 1024`) |

---

## 8. Tools and Libraries Used

| Tool | Purpose |
|------|---------|
| Python | Programming language |
| Pandas | Data manipulation and cleaning |
| NumPy | Numerical operations and missing value handling |
| Matplotlib | Data visualization (histograms and bar charts) |
| Jupyter Notebook | Interactive development environment |

---

## 9. Final Dataset vs. Original Dataset

| Original Dataset | Cleaned Dataset |
|------------------|-----------------|
| Contained duplicate rows | Duplicate rows removed |
| Included missing values | Missing values handled using the median where appropriate |
| Had unrealistic age values | Invalid ages replaced with `NaN` |
| Contained incorrect gender values | Gender values corrected |
| Included logical inconsistencies | Invalid records corrected or removed |
| Dates stored as text | Dates converted to `datetime` |
| Yes/No categorical values | Encoded as binary values (1/0) |
| Less suitable for analysis | Ready for analysis and visualization |

---

# Output

The cleaned dataset was saved as:

```
data/cleaned_dataset.xlsx
```

The notebook also includes several visualizations (histograms and a bar chart) to inspect the distributions of numerical variables and the gender distribution after cleaning.

---

## Conclusion

The dataset was successfully cleaned by removing duplicates, handling missing values, correcting invalid and inconsistent records, converting data types, and encoding categorical variables. These improvements make the final dataset more accurate, consistent, and suitable for further statistical analysis and machine learning tasks.