---
layout: post
title:  "Python & Jupyter Notebook Tricks"
date:   2017-08-17
categories: how-to
---

A few things that I've come across from extensive googling have been game changers for me in Python programming. Check them out below:

*Update* - newly added tricks are added to the top, and will be added as I discover them.

- Storing variables:

When you want to work across multiple jupyter notebooks, you can store your data. In the notebook where the data exists, run this command:

```python
%store df # where df is the name of your data
```

Then in whichever notebook you'd like to pull the data into, run this:
```python
%store -r df # -r probably stands for retrieve or something
```

After running that command, you can use the data in your new notebook.

- Execution timing:

```python
%install_ext https://raw.github.com/cpcloud/ipython-autotime/master/autotime.py
%load_ext autotime
```

This is exclusive to iPython/Jupyter notebook. In order to have the run time printed for each cell, simply run this once. It's super useful and you don't have to write %%time at the top of each cell.

- Warning suppresion:

```python
import warnings
warnings.filterwarnings("ignore")
```

This command will prevent tons of error messages from cluttering up your output. It's fantastic.


- Clean Matplotlib Output

When you use
```python
%matplotlib inline
```

then you enable all plots to appear in the output, instead of just the last run plot in a cell, or having to write plt.show() after each. When plotting, you'll get a ton of ugly output. For example, the following command plot:

```python
from matplotlib import pyplot as plt
%matplotlib inline
plt.plot(df[['col1','col2']])
```
will spit out this before the chart:
```python
[<matplotlib.lines.Line2D at 0x1955e5fbf28>,
 <matplotlib.lines.Line2D at 0x195606e5a58>]
```
The simple solution is to put a semicolon on the last line of your cell.
```python
plt.plot(df[['col1','col2']]);
```
This way, none of that text shows up above your plotted data.
