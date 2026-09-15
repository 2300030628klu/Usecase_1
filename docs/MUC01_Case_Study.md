# MUC01 Retail Sales Case Study

## 1. Business Problem

This notebook analyzes six months of retail transaction data for a regional retail chain with 12 stores across Andhra Pradesh.

The analysis helps the Category Manager identify:

- Categories that are growing
- Categories that are declining
- Categories that are consistently performing
- Categories that may require more shelf space or promotional investment

The source dataset is `MUC01_Retail_Sales_Dataset.csv`, which contains retail transactions from January to June 2026.

## 2. Import Libraries

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

- `pandas` is used for loading and analyzing tabular data.
- `numpy` supports numerical operations, although it is not currently used in the notebook.
- `matplotlib` and `seaborn` are used to create charts.

## 3. Load and Inspect the Dataset

```python
df = pd.read_csv("MUC01_Retail_Sales_Dataset.csv")
```

The notebook then checks the dataset using:

- `df.shape`: number of rows and columns
- `df.head()`: first five records
- `df.dtypes`: data types of each column
- `df.isnull().sum()`: missing values
- `df.describe()`: statistical summary
- `nunique()`: number of unique products and categories
- `unique()`: category names

The dataset contains 107,836 transactions across 10 columns, 25 unique products, and 5 product categories. No missing values were found.

## 4. Prepare the Date Column

```python
df['date'] = pd.to_datetime(df['date'])
df['month'] = df['date'].dt.to_period('M')
```

The first statement converts the `date` column from text into a datetime format. The second extracts the year and month into a new `month` column, such as `2026-01` or `2026-02`.

This makes monthly analysis possible.

## 5. Calculate Monthly Category Revenue

```python
monthly_category_revenue = (
    df.groupby(['month', 'category'])['revenue']
      .sum()
      .reset_index()
)
```

This groups the transactions by month and product category, then adds the revenue for each group.

The resulting table contains:

| month | category | revenue |
|---|---|---:|
| 2026-01 | Apparel | Monthly Apparel revenue |
| 2026-01 | Grocery | Monthly Grocery revenue |

## 6. Plot Revenue Trends

The notebook uses a line chart to compare category revenue over time:

```python
sns.lineplot(
    data=monthly_category_revenue,
    x='month',
    y='revenue',
    hue='category',
    marker='o'
)
```

- The x-axis represents the month.
- The y-axis represents total revenue.
- Each colored line represents a product category.
- Each marker represents a monthly revenue value.

The chart makes growth, decline, and stability easier to identify visually.

## 7. Reshape the Data for Comparison

```python
category_change = (
    monthly_category_revenue
    .pivot(index='month', columns='category', values='revenue')
)
```

This changes the data so that each category becomes a separate column. This format makes it easier to compare the first and last month and calculate variation.

## 8. Calculate Percentage Change

```python
percentage_change = (
    (category_change.iloc[-1] - category_change.iloc[0])
    / category_change.iloc[0]
) * 100
```

The formula is:

```text
(June revenue - January revenue) / January revenue × 100
```

Interpretation:

- A positive value means the category grew.
- A negative value means the category declined.

`percentage_change.sort_values()` orders the categories from the largest decline to the strongest growth.

## 9. Measure Category Consistency

```python
consistency = (
    category_change.std() / category_change.mean()
) * 100
```

This calculates the coefficient of variation:

```text
Standard deviation / Average revenue × 100
```

A lower coefficient of variation means that monthly revenue was more stable. A higher value indicates greater fluctuation.

## 10. Business Findings

### Electronics

Electronics declined by 35.02% from January to June, making it the steepest declining category. The supplier should be asked to explain the decline before additional shelf space or promotional investment is committed.

### Apparel

Apparel increased by 34.10% from January to June. This indicates growing customer demand and supports considering additional attention, shelf space, or promotional activity.

### Grocery

Grocery was the most consistent category, with a coefficient of variation of 3.87%. Its stable revenue makes it a predictable contributor and supports maintaining a stable shelf presence.

## 11. Recommendation

The Category Manager should:

1. Investigate the decline in Electronics before increasing investment.
2. Consider increased focus and promotional support for Apparel.
3. Maintain Grocery's stable shelf presence and monitor it as a dependable revenue category.
4. Review the remaining categories using the same growth and consistency measures before making final supplier decisions.



