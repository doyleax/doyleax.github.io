---
layout: post
title:  "Space Efficiency with Pandas DataFrames"
date:   2018-05-18
categories: how-to
---

Lately I've been working with enormous datasets (10s of millions of rows) with anywhere from tens to hundreds or even thousands of features. What I have found to be absolutely critical is cutting down on as much space as possible. Assigning dtypes to your dataframe is the best thing you can do for performance enhancement.

I'll be using a data set on US Census data, found at the link below.

```python
import pandas as pd
url = "https://archive.ics.uci.edu/ml/machine-learning-databases/census1990-mld/USCensus1990.data.txt"

df = pd.read_table(url,sep=',')
```

First thing to do is check the dataframe info. This will detail the dtypes of the columns, along with the total size of your table.

```python
df.info()
```

This tells us the following:
```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 2458285 entries, 0 to 2458284
Data columns (total 69 columns):
caseid       int64
dAge         int64
dAncstry1    int64
dAncstry2    int64
iAvail       int64
iCitizen     int64
iClass       int64
dDepart      int64
iDisabl1     int64
iDisabl2     int64
iEnglish     int64
iFeb55       int64
iFertil      int64
dHispanic    int64
dHour89      int64
dHours       int64
iImmigr      int64
dIncome1     int64
dIncome2     int64
dIncome3     int64
dIncome4     int64
dIncome5     int64
dIncome6     int64
dIncome7     int64
dIncome8     int64
dIndustry    int64
iKorean      int64
iLang1       int64
iLooking     int64
iMarital     int64
iMay75880    int64
iMeans       int64
iMilitary    int64
iMobility    int64
iMobillim    int64
dOccup       int64
iOthrserv    int64
iPerscare    int64
dPOB         int64
dPoverty     int64
dPwgt1       int64
iRagechld    int64
dRearning    int64
iRelat1      int64
iRelat2      int64
iRemplpar    int64
iRiders      int64
iRlabor      int64
iRownchld    int64
dRpincome    int64
iRPOB        int64
iRrelchld    int64
iRspouse     int64
iRvetserv    int64
iSchool      int64
iSept80      int64
iSex         int64
iSubfam1     int64
iSubfam2     int64
iTmpabsnt    int64
dTravtime    int64
iVietnam     int64
dWeek89      int64
iWork89      int64
iWorklwk     int64
iWWII        int64
iYearsch     int64
iYearwrk     int64
dYrsserv     int64
dtypes: int64(69)
memory usage: 1.3 GB
```


### Numeric Columns

All int64 columns. An integer column can be 8, 16, 32, or 64 bits, while float columns can be 16, 32, or 64 bits. Know that a float16 column is the same size as an int16 column.

How do we know the optimal size for our numeric columns? Numpy has iinfo, finfo (integer, float info) attributes that tell us the range which a column's values should fall within for each size. Below, we will use this to create a reference that will give us all of the ranges for each bit size.

```python
# all the possible bit sizes for floats, ints
numeric_dtypes = {'float': {}, 'int': {} }
float_bits, int_bits = ['16', '32', '64'], ['8','16','32','64']

# use numpy's iinfo, finfo attributes to figure out what the min, max values are for each bit type
for bit in float_bits:
    numeric_dtypes['float'][bit] = [np.finfo("float"+bit).min,np.finfo("float"+bit).max]
for bit in int_bits:
    numeric_dtypes['int'][bit] = [np.iinfo("int"+bit).min,np.iinfo("int"+bit).max]
```    

Check out the bit information:

```
{'float': { '16': [-65504.0, 65504.0],
            '32': [-3.4028235e+38, 3.4028235e+38],
            '64': [-1.7976931348623157e+308, 1.7976931348623157e+308]},
 'int': {   '16': [-32768, 32767],
            '32': [-2147483648, 2147483647],
            '64': [-9223372036854775808, 9223372036854775807],
            '8': [-128, 127]}}
```

These are the ranges that the min, max value of a particular column must fall within to use the bit size. If you apply a smaller bit size to a column than it actually needs, your values will get messed up.

Now that we have this information, let's check out each column and see which size would be best. We're going to create a dictionary of the original dtypes in the df, and then create a new one with optimal bit sizes. Although this df only has ints, the following code snippet includes floats so that you can use it with any dataframe.

    
``` python    
# grab all columns that are either integer or float dtypes
temp = df.select_dtypes(include=['integer','float'])

# create a dictionary of the original dtypes of this numeric df
original_dtypes = dict(temp.dtypes)

# create an empty dictionary to place new dtypes in
dtypes = {}

for col in df.columns:
    min_val = df[col].min()
    max_val = df[col].max()
  
    # find out whether this numeric column is int or float
    num_type = re.match(r'[^0-9]+',str(original_dtypes[col])).group()

    for bit in numeric_dtypes[num_type]:
        if (min_val >= numeric_dtypes[num_type][bit][0]) & (min_val <= numeric_dtypes[num_type][bit][1]):
            dtypes[col] = num_type + bit
            break  # to ensure that the smallest possible bit size gets recorded in the dtype dict, break the loop here
      
```

We already have the old dtypes printed above (after df.info()) and they're all int64. So let's check out what the new proposed dtypes are:

```
dtypes
{'caseid': 'int16',
 'dAge': 'int8',
 'dAncstry1': 'int8',
 'dAncstry2': 'int8',
 'dDepart': 'int8',
 'dHispanic': 'int8',
 'dHour89': 'int8',
 'dHours': 'int8',
 'dIncome1': 'int8',
 'dIncome2': 'int8',
 'dIncome3': 'int8',
 'dIncome4': 'int8',
 'dIncome5': 'int8',
 'dIncome6': 'int8',
 'dIncome7': 'int8',
 'dIncome8': 'int8',
 'dIndustry': 'int8',
 'dOccup': 'int8',
 'dPOB': 'int8',
 'dPoverty': 'int8',
 'dPwgt1': 'int8',
 'dRearning': 'int8',
 'dRpincome': 'int8',
 'dTravtime': 'int8',
 'dWeek89': 'int8',
 'dYrsserv': 'int8',
 'iAvail': 'int8',
 'iCitizen': 'int8',
 'iClass': 'int8',
 'iDisabl1': 'int8',
 'iDisabl2': 'int8',
 'iEnglish': 'int8',
 'iFeb55': 'int8',
 'iFertil': 'int8',
 'iImmigr': 'int8',
 'iKorean': 'int8',
 'iLang1': 'int8',
 'iLooking': 'int8',
 'iMarital': 'int8',
 'iMay75880': 'int8',
 'iMeans': 'int8',
 'iMilitary': 'int8',
 'iMobility': 'int8',
 'iMobillim': 'int8',
 'iOthrserv': 'int8',
 'iPerscare': 'int8',
 'iRPOB': 'int8',
 'iRagechld': 'int8',
 'iRelat1': 'int8',
 'iRelat2': 'int8',
 'iRemplpar': 'int8',
 'iRiders': 'int8',
 'iRlabor': 'int8',
 'iRownchld': 'int8',
 'iRrelchld': 'int8',
 'iRspouse': 'int8',
 'iRvetserv': 'int8',
 'iSchool': 'int8',
 'iSept80': 'int8',
 'iSex': 'int8',
 'iSubfam1': 'int8',
 'iSubfam2': 'int8',
 'iTmpabsnt': 'int8',
 'iVietnam': 'int8',
 'iWWII': 'int8',
 'iWork89': 'int8',
 'iWorklwk': 'int8',
 'iYearsch': 'int8',
 'iYearwrk': 'int8'}
```
From a quick glance, I can see that there are no longer any int64 columns. 


### Converting Column Data Types

Now that we know what dtypes to switch each column through, I'll note two options to change the dtype.

The general way to change a column's dtype is by running the following:

```python
df[col] = df[col].astype(dtype)
```
Above, dtype will typically be one of the following: ['object','category','int8','int16','int32','int64','float16','float32','float64']. 

While you can change your dtype with the above command, this takes far too long on a large df. We will instead be re-loading in our data using the specified dtypes.

```python
new_df = pd.read_table(url,sep=',',dtype=dtypes)

# inspect dataframe size stats
new_df.info()
```

```
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 2458285 entries, 0 to 2458284
Data columns (total 69 columns):
caseid       int16
dAge         int8
dAncstry1    int8
dAncstry2    int8
iAvail       int8
iCitizen     int8
iClass       int8
dDepart      int8
iDisabl1     int8
iDisabl2     int8
iEnglish     int8
iFeb55       int8
iFertil      int8
dHispanic    int8
dHour89      int8
dHours       int8
iImmigr      int8
dIncome1     int8
dIncome2     int8
dIncome3     int8
dIncome4     int8
dIncome5     int8
dIncome6     int8
dIncome7     int8
dIncome8     int8
dIndustry    int8
iKorean      int8
iLang1       int8
iLooking     int8
iMarital     int8
iMay75880    int8
iMeans       int8
iMilitary    int8
iMobility    int8
iMobillim    int8
dOccup       int8
iOthrserv    int8
iPerscare    int8
dPOB         int8
dPoverty     int8
dPwgt1       int8
iRagechld    int8
dRearning    int8
iRelat1      int8
iRelat2      int8
iRemplpar    int8
iRiders      int8
iRlabor      int8
iRownchld    int8
dRpincome    int8
iRPOB        int8
iRrelchld    int8
iRspouse     int8
iRvetserv    int8
iSchool      int8
iSept80      int8
iSex         int8
iSubfam1     int8
iSubfam2     int8
iTmpabsnt    int8
dTravtime    int8
iVietnam     int8
dWeek89      int8
iWork89      int8
iWorklwk     int8
iWWII        int8
iYearsch     int8
iYearwrk     int8
dYrsserv     int8
dtypes: int16(1), int8(68)
memory usage: 164.1 MB
```

By specifying dtypes, we were able to shrink the df down from 1.3GB to 164.1 MB. That's huge!



### Categorical Columns

This dataset used above doesn't have any object columns, which suck up the most space. To see how converting an object column to a 'category' type can save space, download NYC Bus data from [Kaggle](https://www.kaggle.com/stoney71/new-york-city-transport-statistics). It's a pretty big file, so it will take a bit of time to download. 

```python
file = "
```

If you can pinpoint object columns that should actually be categorical, you'll drastically reduce the size of your df by converting them. However, many numerical columns can also be categorical. In fact, if you run the below code on the first df to determine whether a column should be categorical, you'll find that all columns aside from the ID column are, in fact, categorical. However, in this case, converting the columns to category doesn't affect the end df size at all (because they're all int8, which is pretty small).

Typically, less than 40% of the total values in a column should be distinct in order to convert it to a categorical column. Otherwise, it could potentially be less efficient to perform a categorical conversion on the column.

```python
n = df.shape[0]    # get the number of rows in the df
print('CONVERT TO CATEGORY:')
print('--------------------')

for col in df.columns:
    if df[col].nunique() / n < 0.4:
        print(col)
        dtypes[col] = 'category'
```

Turns out that all but the first ID column are categorical. Re-loading in the data with the new dtypes doesn't make it any smaller in this case, but also doesn't make it any bigger. Converting to categorcial is far more useful when a column is of the 'object' type, but since this particular dataframe didn't have any 'object' columns, I wanted to at least show how to go about converting to 'category.'



### De-duping

Although listed last, this should probably always be your first step when loading data. Oftentimes, datasets can be flooding with duplicate rows, and getting rid of them is easy.

```python
df.drop_duplicates(inplace=True)
```

### Conclusion

Hopefully using all of these techniques serves to be incredibly useful for those of you working with large datasets in the hopes of speeding up processing. 

