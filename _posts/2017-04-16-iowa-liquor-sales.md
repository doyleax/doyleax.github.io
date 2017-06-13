---
layout: post
title:  "Iowa Liquor Sales"
date:   2017-04-16
categories: jekyll update
---

For our second project, we were to take liquor sales from Iowa and determine ideal locations for opening a new liquor store. The data can be found on the government of Iowa's website, [here](https://data.iowa.gov/Economy/Iowa-Liquor-Sales/m3tr-qhgy).

As it's a fairly large dataset, I used 10% of the data for my EDA and model training. The data was pretty clean, aside for some missing county names, which could be assigned using the store's zip code. 

I've been desperately trying to figure out the equivalent of a SQL case query, using like to search the strings and assign the different liquor categories to a larger, more general categories. What I love about going back to previously completed projects is essentially being able to tackle it with fresh eyes. I was able to remove my whole SQL connnection and replace it with the following:

```python
categories = 'VODKA','BRANDIES','LIQUEUR', 'WHISK', 'TEQUILA','RUM', 'SCHNAPPS','GIN','SCOTCH' ,'BOURBON'

for category in categories:
    liquor_full.category_clean[liquor_full.category_name.str.contains(category)] = category

liquor_full.category_clean[liquor_full.category_clean=='WHISK']='WHISKEY'
liquor_full.category_clean = liquor_full.category_clean.fillna('OTHER')
```
Aside from that minor feat, returning to my project has spurred many more questions that I want to explore when I finally get some free time. My biggest question revolves around how to actually determine what an 'ideal' location for a new store front entails. Does ideal mean an area with the highest revenue, where it might be hard to break into the market based on your competition; or do you look for an area with low revenue, assess demographics, and try out that location?

I am interested in obtaining US census data for features like salary, age, etc. to help guide the answer to this question.

