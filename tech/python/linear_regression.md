# Pandas Basics

```python
import pandas as pd
import seaborn as sns

import matplotlib.pyplot as plt
%matplotlib inline # to display plots inline (eg., in a jupyter notebook)

import numpy as np


# Read a csv into a dataframe
df = pd.read_csv("/path/to/my.csv")

# Return/view features of the dataframe: columns, non-null count, data type, etc.
df.info()

# Return/view the shape, i.e., dimensions of the dataframe
df.shape

# Return/view first n rows (n = 5 by default)
df.head(n) # if n < 0, returns all except last n rows

# Return/view last n rows
df.tail(n) # if n < 0, returns all except first n rows

# Generate descriptive statistics for the dataframe: 
# count, min, max, standard deviation, percentiles, etc
# Returns series or dataframe
df.describe()

# Get all column labels
df.columns

# Return sum of values over specified axis
df.sum()

# Detect missing values. Return a boolean same-sized object 
# indicating if the values are null
df.isnull()

# View number of nulls/missing values in each column
df.isnull().sum()

# Create a copy of a dataframe (regular assignment points to same df)
df_copy = df.copy()
```

# Imputation of Missing Data
```python
from sklearn.impute import KNNImputer

df_copy = orig_df.copy()

# Retrieve columns with numerical data 
column_list = [col for col in df_copy.columns if df_copy[col].dtype != 'object']

# Extract columns with at least one missing value
missing_col_list = [col for col in df_copy.loc[:, df_copy.isnull.any()]]

# Update df_copy with numeric columns that have missing values
df_copy = df_copy[missing_col_list]

# Initialise KNNImputer
knn = KNNImputer(n_neighbors = 5)

# Train the model
knn.fit(df_copy)

# Apply knn model to the data
vals = knn.transform(df_copy)

# Convert to dataframe with the column names
df_copy = pd.DataFrame(vals, columns = missing_col_list)

# Update original columns with the imputed ones
for col_name in missing_col_list:
    df[col_name] = df_copy.replace(df[col_name], df[col_name])
```
