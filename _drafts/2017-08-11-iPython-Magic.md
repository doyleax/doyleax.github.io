---
layout: post
title:  "My Favorite iPython Tricks"
date:   2017-08-17
categories: how-to
---

Jupyter notebook is the best. What makes me love it even more are my among my favorite things:

- Execution timing:

```python
%load_ext autotime
```

Simply run this and each cell of your notebook will be timed when it's run. It's super useful and less annoying than writing %%time at the top of each cell.

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
The simple solution is to end the plot command with a semicolon:
```python
plt.plot(df[['col1','col2']]);
```
This way, none of that text shows up above your plotted data.
