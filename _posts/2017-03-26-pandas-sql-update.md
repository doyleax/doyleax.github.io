---
layout: post
title:  "SQL for updating Pandas DataFrame"
date:   2017-03-26 21:55:09 -0400
categories: how-to
---

I'm fairly new to Python and even more so to Pandas, but I'm pretty experienced in SQL. As I encounter ever more issues in manipulating data in Pandas DataFrames, I find myself angrily thinking of how I could have solved each problem simply and quickly somehow using SQL on the df. 

While I knew there was a way to run SQL queries in Python, I had no idea if it could serve the purpose I had in mind. My dataframe contained columns for zip_code, city, and county, but the county column was null for a portion of the rows. I wanted to make sure I could breakdown my data by each of those three attributes, so filling in these values was a must.

I have since figured out a few great ways to accomplish this task with Pandas, but I figured I'd throw this out there for anyone else who is in a jam and would love to use SQL instead of Pandas for data manipulation.

Quick overview of what I had and what I needed to do:
I had 3 Pandas dataframes- liquor, county_lookup, and iowa_zips. The df liquor is where my county column needed some help, and I planned to fill in the missing county name by pulling county from county_lookup, linking by zip_code. Any remaining nulls in county would be filled by linking to iowa_zips by zip_code and pulling the county field.

From my Googling, I found that I would need to push those three DataFrames to a SQL connection, execute updates, and then pull the table back into a Pandas df. 

I initially struggled with sqlite3 until realizing that I would not be able to pull the table back into a Pandas df--this would only be possible with SQLAlchemy. Here's the setup that I was able to run in a Jupyter notebook:

{% highlight ruby %}
from sqlalchemy import create_engine
engine = create_engine('sqlite:///:memory:')  # create a SQL connection
conn = engine.connect()
liquor.to_sql('liquor', engine) .             # push liquor df to SQL table
county_lookup.to_sql('county_lookup', engine) # push county_lookup df to SQL table
iowa_zips.to_sql('iowa_zips', engine)         # push iowa_zips df to SQL table
{% endhighlight %}
