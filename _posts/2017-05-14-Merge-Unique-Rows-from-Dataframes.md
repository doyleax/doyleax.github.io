---
layout: post
title:  "Merge DataFrames: Unique Rows from Two DataFrames"
date:   2017-05-14
categories: how-to
---
I am constantly trying to remember how to add a row into a dataframe only if it doesn't already exist. My indices will never match up and are irrelevant, so I struggle to figure out how to ignore the indexes on the dataframes.

This post is for future me when I inevitably forget about the parameters of the Pandas merge function. I'm hoping I'll naturally Google some of the words here to lead me quickly to my answer.

For anyone else who thinks in SQL when they code in Python, hopefully the following will be helpful. I'm trying to see whether the row already exists. For this example, say that the table is just one column, 'letter', and has rows 'a','b','c'. Table2 will have 2 rows,'b' & 'd', and I want to see if I can add any rows that aren't already in Table1. Table2 will be 'b' and Row2 will be 'd.'

What we want to see is that 'b' already exists, but 'd' doesn't, so we know we can insert 'd.'

I apologize in advance if there are errors in this SQL script--I haven't written in SQL in about 3 or 4 months so I'm sure I'm forgetting syntax. But the idea is the focus, not the actual code.
```SQL
## in SQL
## create tables
create table Table1 (
letters varchar(1));

create table Table2 (
letters varchar(1));

insert all
  into Table1 ('a')
  into Table1 ('b')
  into Table1 ('c')
select * from dual;
commit;

insert all
  into Table2 ('b')
  into Table2 ('d')
select * from dual;
commit;


## add rows from Table2 into Table1 if they don't already exist
## also this is where my SQL is most likely incorrect... sorry
insert into Table1
  select letter from Table2
  where not exists (
    select letter from Table1
    where Table1.letter = Table2.letter);
commit;

```

Now the Pandas solution!!

```python
t1 = ['a','b','c']
t2 = ['b','d']
Table1 = pandas.DataFrame(t1,columns='letters')
Table2 = pandas.DataFrame(t2,columns='letters')

Table1 = Table1.merge(Table2, how='inner', left_index=False, right_index=False).reset_index(drop=True)
```

Just to summarize, this python script above merges the rows from Table2 into Table1 that do not already exist, and whose indexes are not of concern.

There are different 'how' options in the merge function--'outer','left','right'.

If you're anything like me and need to double and triple check that you got what you wanted, then add the indicator column:
```python
test = Table1.merge(Table2, how='inner', left_index=False, right_index=False,indicator=True).reset_index(drop=True)
```
You'll see a new column called '_merge' that will tell you where the row came from: 'left_only' (Table1), 'right_only' (Table2), 'both' (in both Table1 and Table2).

In our case, we wouldn't have any 'both' values (but you can see 'both' if you use how='outer')

More on merge [here](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.merge.html).


