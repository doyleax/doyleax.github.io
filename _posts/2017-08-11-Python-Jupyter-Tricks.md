---
layout: post
comments: true
title:  "Python & Jupyter Notebook Tricks"
date:   2017-08-17
categories: how-to

---

__*Update*__ - newly added tricks are added to the top, and will be added as I discover them.
A few things that I've come across from extensive googling have been game changers for me in Python programming. Check them out below:

- Increasing width of Jupyter notebook cells [(source)](https://github.com/jupyter/notebook/issues/1909):

```python
from IPython.core.display import display, HTML 
display(HTML("<style>.container { width:100% !important; }</style>"))
```

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

This is exclusive to iPython/Jupyter notebook. In order to have the run time printed for each cell, simply run this once. It's super useful and you don't have to write %%time at the top of each cell. The only annoying thing is that if the execution time was 0, it prints out this assertion error. 

```python
Error in callback <bound method LineWatcher.stop of <autotime.LineWatcher object at 0x000001852A822D68>> (for post_run_cell):

---------------------------------------------------------------------------
AssertionError                            Traceback (most recent call last)
C:\ProgramData\Anaconda3\lib\site-packages\autotime.py in stop(self)
     23         if self.start_time:
     24             diff = time.time() - self.start_time
---> 25             assert diff > 0
     26             print('time: %s' % format_delta(diff))
     27 

AssertionError: 
```

I fixed this by editing the py file and erasing the assertion:

```python
filename = 'C:\\ProgramData\\Anaconda3\\lib\\site-packages\\autotime.py'
with open(filename, 'r') as file:
    # read a list of lines into data
    data = file.readlines()
# remove line 25 with the assertion (found this from error message)
data[25] = ''
# write everything back
with open(filename, 'w') as file:
    file.writelines( data )
```


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
