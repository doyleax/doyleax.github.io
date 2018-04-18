---
layout: post
title:  "Space Efficiency with Pandas DataFrames"
date:   2018-04-18
categories: how-to
---

# Space Efficiency with Pandas DataFrames

Lately I've been working with enormous datasets (10s of millions of rows) with anywhere from tens to hundreds or even thousands of features. What I have found to be absolutely critical is cutting down on as much space as possible. Assigning dtypes to your dataframe is the best thing you can do.

The general way to change a column's dtype is by running the following:

```python
df[col] = df[col].astype(dtype)
```
Above, dtype will be one of the following: ['object','category','int8','int16','int32','int64','float8','float16','float32','float64']. Note that there are 4 different sizes that numeric columns could be for both integers and floats.

While you can change your dtype with the above command, this takes far too long on a large df. Instead, we will be going through to determine which dtype each column should be converted to for optimal memory usage by creating a dictionary of column names and dtype.

```python
dtypes = {}
```

### Categorical Columns

The first thing you should do is check if you have categorical variables. The object dtype sucks up the most space. If you can pinpoint these and convert them to the 'category' dtype, then you'll drastically reduce the size of your df.
Typically, less than 40% of the total values in a column should be distinct in order to convert it to a categorical column.

```python
n = df.shape[0]    # get the number of rows in the df
print('CONVERT TO CATEGORY:')
print('--------------------')

for col in df.columns:
    if df[col].nunique() / n < 0.4:
        print(col)
        dtypes[col] = 'category'
```

### Numeric Columns

When you load data into a dataframe, it usually will give you the largest possible dtype for numeric columns, either int64 or float64. Even though a binary feature would be ideal in an 8 bit dtype, it bloats it into 64 bit, taking up more space than necessary.

Numpy has an iinfo attribute that can tell us the range which a column's values should fall within for each size. This will be used below.

```python
import re

# grab all columns that are either integer or float dtypes
temp = df.select_dtypes(['integer','float'])

# create a dictionary of the original dtypes of this numeric df
original_dtypes = dict(temp.dtypes)

for col in df.columns:
  min_val = df[col].min()
  max_val = df[col].max()
  
  # find out whether this numeric column is int or float
  num_type = re.match(r'[^0-9]+',str(original_dtypes[col])).group()
  
  # columns containing values within the following range qualify for an 8-bit dtype
  if (min_val >= np.iinfo(num_type+'8').min) & (min_val <= np.iinfo(num_type+'8').max):
    dtypes[col] = num_type + '8'

  if (min_val >= np.iinfo(num_type+'16').min) & (min_val <= np.iinfo(num_type+'16').max):
    dtypes[col] = num_type + '16'

  if (min_val >= np.iinfo(num_type+'32').min) & (min_val <= np.iinfo(num_type+'32').max):
    dtypes[col] = num_type + '32'
  
  if (min_val >= np.iinfo(num_type+'64').min) & (min_val <= np.iinfo(num_type+'64').max):
    dtypes[col] = num_type + '64'
```




