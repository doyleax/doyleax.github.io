---
layout: post
title:  "Convert String to Number in Python"
date:   2017-04-02
categories: how-to
---

While working on a project, I came across this issue where I had a string which was a number with commas. As it's stored as a string, it cannot be modified using int(string) or float(string) or .astype(float). I've tried them all. I've also tried removing the commas and then converting. No such luck.

Here's a number with a comma, type str:
```python
In [1]: num_string = '2,168'
In [2]: type(num_string)
Out[2]: str
```

Try to convert to float, int:
```python
In [3]: float(num_string)
Out[3]: ValueError: invalid literal for float(): 2,168

In [4]: int(num_string)
Out[4]: ValueError: invalid literal for int() with base 10: '2,168'

In [5]: num_string.strip(',')
Out[5]: '2,168'
###????????????
```
Probably the most infuriating part was this last part where the comma wasn't stripped and no error was thrown.

So now that I've gone through everything that DOESN'T work, here's what actually will get the job done:

```python
import locale

In [6]: locale.setlocale(locale.LC_ALL, 'en_US.UTF-8') # adjust your python settings to specify your locale
Out[6]: 'en_US.UTF-8'
# this would be English - US
# replace 'en_US' with 'fr_FR' for French, 'en_GB' for Great Britain
# those are the only other two I know

# set our new number to a float
new_num = float(locale.atof(string))

In [7]: num
Out[7]: 2168.0

In [8]: type(new_num)
Out[8]: float
# yay
```    
 
[Click here for more info on locale.](https://docs.python.org/2/library/locale.html)
