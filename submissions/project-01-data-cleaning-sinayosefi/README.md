# Customer Dataset Data Cleaning Project

## Project Overview
This project focuses on cleaning and preprocessing a customer dataset in order to improve its quality, consistency, and usability for later analysis or modeling.

The main goal was to identify common data quality issues such as:

- Missing values
- Duplicate records
- Incorrect data types
- Outliers and unrealistic values
- Inconsistent text formatting
- Logical inconsistencies between columns
- Incorrect or unreliable values in the `gender` column

After identifying these issues, appropriate cleaning and imputation methods were applied to produce a more reliable final dataset.

---

## Libraries and Tools Used

The following Python libraries were used in this project:

- **pandas**: for data manipulation and cleaning
- **numpy**: for numerical operations and handling missing values
- **seaborn**: for visualization
- **matplotlib**: for plotting charts
- **scikit-learn**:
  - `RandomForestRegressor` for predicting missing `age` values
- **requests**: for sending API requests to Genderize.io
- **time**: for rate-limit friendly delays while calling the API

---

## Main Problems Found in the Dataset

During the cleaning process, the following data quality issues were identified:

### 1. Missing Values
Several columns contained missing values, including important columns such as:
- `age`
- `total_spending`

### 2. Outliers
Numeric columns contained outlier values that could distort analysis and model behavior.

### 3. Duplicate Records
Some rows were duplicated and needed to be removed.

### 4. Incorrect Data Types
Some columns had unsuitable data types, for example:
- `signup_date` was stored as text instead of datetime
- ID and count columns needed integer-compatible types
- `discount_used` needed normalization to a consistent binary form

### 5. Inconsistent Text Formatting
Text columns contained:
- Leading/trailing spaces
- Repeated internal spaces
- Inconsistent writing styles

### 6. Logical Inconsistency in `gender`
The `gender` column was found to be unreliable and effectively random, so it could not be trusted as a valid source.

---

## What Changes Were Made?

The following cleaning steps were applied:

### 1. Missing Value Detection
Rows with missing values were identified using:

```python
df[df.isna().any(axis=1)]
```

### 2. Outlier Handling
Outliers were detected in numeric columns using the **IQR method**:

- Values below  
  $$
  Q1 - 1.5 \times IQR
  $$
- Or above  
  $$
  Q3 + 1.5 \times IQR
  $$

were considered outliers.

Instead of deleting entire rows, detected outlier values were converted to `NaN` so they could be handled later more carefully.

### 3. Correlation Analysis
A correlation matrix was created to better understand the relationships between numeric variables.

### 4. Rule-Based Recovery for `total_spending`
A strong relationship between these variables was identified:

$$
\text{total\_spending} = \text{purchase\_count} \times \text{avg\_order\_value}
$$

This relationship was used to reconstruct missing `total_spending` values.

### 5. Predictive Imputation for `age`
After other cleaning steps, only a small number of missing values remained in the `age` column.  
These were imputed using a **RandomForestRegressor** model trained on:

- `total_spending`
- `avg_order_value`
- `purchase_count`
- `satisfaction_score`

### 6. Duplicate Removal
Duplicate rows were identified and removed.

### 7. Data Type Corrections
Several columns were converted to more appropriate types, such as:

- `customer_id` → integer-compatible type or string
- `signup_date` → `datetime64[ns]`
- count-related columns → `Int64`
- `discount_used` → normalized to binary format

### 8. Text Cleaning
Extra spaces were removed from text columns, including:
- leading spaces
- trailing spaces
- repeated internal spaces

### 9. Rebuilding `gender` from `first_name`
Since the original `gender` column was unreliable, gender values were inferred again from the `first_name` column using the **Genderize.io API** with:

- `country_id=IR`

This was done to make gender inference more appropriate for Iranian names written in English transliteration.

---

## Why Were These Changes Applied?

These changes were necessary because raw datasets often contain noise, inconsistency, and incomplete information.

The reasons for each change are:

- **Missing values** can break analysis and machine learning workflows.
- **Outliers** may represent data entry errors or unrealistic values and can heavily distort results.
- **Duplicates** can bias statistics and lead to repeated observations being counted multiple times.
- **Incorrect data types** reduce reliability and make further processing more difficult.
- **Text inconsistencies** create false categories, such as treating `"Tehran"` and `" Tehran "` as different values.
- **Logical repairs** improve accuracy when valid business rules exist.
- **Replacing `gender`** was necessary because the original values were not trustworthy.

---

## How Were Missing Values Managed?

Missing values were handled in multiple ways depending on the nature of the column:

### 1. Outliers Converted to Missing Values
Outlier values in numeric columns were first replaced with `NaN`.

### 2. Rule-Based Imputation
For `total_spending`, missing values were filled using:

$$
\text{total\_spending} = \text{purchase\_count} \times \text{avg\_order\_value}
$$

This was preferred because it is based on a clear business relationship, making it more reliable than a statistical guess.

### 3. Model-Based Imputation
Missing values in `age` were predicted using a `RandomForestRegressor`.

This method was chosen because:
- `age` had only a few missing values
- several related numerical features were available
- a predictive model can preserve structure better than simple mean imputation

### 4. Text Cleaning Without Damaging Missing Values
For text columns, cleaning was applied conditionally so that existing `NaN` values were not converted into strings like `"nan"`.

---

## How Were Duplicates Checked?

Duplicate rows were identified using pandas duplicate detection and then removed.

Typical logic used:

```python
df.duplicated()
df.drop_duplicates()
```

This ensured each record appeared only once in the dataset.

---

## Were Column Data Types Changed?

Yes. Several columns required type correction to improve consistency and usability.

Examples:

- `signup_date` was converted from text to datetime
- count columns were converted to integer-compatible formats
- categorical or binary-like values such as `discount_used` were standardized
- identifiers were adjusted to a suitable non-floating type

This was important because incorrect types can:
- produce wrong summaries
- break calculations
- prevent proper filtering and modeling

---

## Were Outliers or Unrealistic Values Found?

Yes. Outliers were found in numeric columns.

They were detected using the IQR method and replaced with `NaN` rather than deleting full rows.  
This approach was chosen because:
- the row may still contain useful information in other columns
- missing value imputation can later recover only the problematic field

Additionally, logically suspicious values were also reviewed during the cleaning process.

---

## How Was the `gender` Column Fixed?

The original `gender` column was considered unreliable.

Instead of manually mapping names to gender, a scalable API-based approach was selected using **Genderize.io** with:

```bash
https://api.genderize.io?name=sina&country_id=IR
```

Example response:

```json
{
  "count": 24017,
  "name": "sina",
  "gender": "male",
  "country_id": "IR",
  "probability": 0.98
}
```

### Why this method was chosen:
- More scalable than manual dictionaries
- Better suited for large datasets
- Supports probability-based inference
- Country-specific prediction improves results for Iranian names

### Recommended implementation strategy:
- Extract unique names from `first_name`
- Send API requests only once per unique name
- Build a mapping from name to predicted gender
- Apply the mapping back to the entire dataset

This avoids unnecessary repeated API calls and makes the process much more efficient.

---

## Final Version vs Initial Version

The final cleaned dataset differs from the raw version in several important ways:

### Initial Version
- Contained missing values
- Included outliers
- Had duplicate rows
- Had inconsistent text formatting
- Included incorrect or weakly structured data types
- Had an unreliable `gender` column
- Contained some logically incomplete values

### Final Version
- Missing values were systematically handled
- Outliers were identified and converted to manageable missing values
- Duplicate records were removed
- Text fields were normalized
- Data types were corrected
- `total_spending` was recovered using business logic
- `age` missing values were predicted with a machine learning model
- `gender` was reconstructed using `first_name` and an external gender prediction API
- The dataset became cleaner, more consistent, and more suitable for analysis and modeling

---

## Conclusion

This project transformed a raw customer dataset into a cleaner and more analysis-ready version through a combination of:

- rule-based cleaning
- statistical detection
- machine learning imputation
- API-assisted enrichment
- text normalization
- structural validation

The final dataset is significantly more reliable than the original one and is better prepared for:
- exploratory data analysis
- dashboarding
- machine learning
- business reporting

