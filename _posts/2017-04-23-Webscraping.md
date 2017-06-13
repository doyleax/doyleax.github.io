---
layout: post
title:  "Webscraping"
date:   2017-04-23
categories: jekyll update
---

One of my favorite new skills to date is webscraping. In my last job, I had to use HTML and CSS frequently on our product to develop features and fix bugs. I transitioned to a database heavy position, and really missed working on the website. Webscraping served as a nice reminder as to how much I enjoyed my web development experience. With that said, please don't judge the appearance of my blog. I wish I had the time to spruce it up.

We recently submitted a project where we scraped job listings from Indeed.com for Data Scientists and then used the data to try and predict salaries. Only a small portion of the job postings contained salary information, so building a model was pretty tricky. I also sadly lost a ton of data somehow, so my model wasn't great. I definitely enjoyed the webscraping aspect of the project more.

Each page of results on Indeed has 10 listings, with an additional 5 sponsored postings. The 10 listings had their own div class name, while nearly each of the 5 sponsored positings had their own div class. Lucikly, all postings had some variation of the word 'result' in the div class name, so I was able to generalize that and pull each of the 15 postings. I would hit each posting, then grab the job title, the company, the location, and salary. 

With BeautifulSoup, you can use find or find_all to get what you need. While find_all will grab each instance of what you're looking for, find simply looks for the first one from wherever your starting point is. Knowing this, I felt that looping through the postings and using find for salary would potentially risk adding salary information to the wrong posting--what if one posting doesn't include salary, but the next one does, and my code jumps to that next available salary for the previous posting?! I decided that the best solution to ensure that all data relating to a particular posting would be grouped together correctly would be to find_all divs with 'result' in the class name and set that equal to a list. I'd then have a list of all 15 jobs, and one by one, could look for each of the attribtues I needed.

Below are some snippets of code from the larger function I wrote to scrape each page of results. To make the task of finding just the 15 postings with 'results' in the div class, I found that jumping into the td with id='resultsCol' would limit my divs to just those. Then I set the postings as separate items in a list.

```python
from bs4 import BeautifulSoup
page = requests.get(URL).content
soup = BeautifulSoup(page)

main = soup.find('td',{'id':'resultsCol'})   # limit our searching to solely the results portion of the page
results = main.find_all('div', {'class': re.compile("result$")}) # create a list consisting only of the 15 results
```

Another helpful tool I found was to scrape the total number of results to help my scraper know how many iterations of scraping pages of results it had to perform before quitting. I eventually used the total number of results/4 as my max, since my frequent scraping didn't require a full refresh of results each time. I usually only needed the first several pages that contained new job postings.

Each run through would export the table of results to a csv. In order to build my model, I would need to import each file, so I created a function that would load them all and append the data to a main dataframe. I found the following code in Python to help me grab all of the csv files:

```python
import glob

folder = file-path-in-quotes
# create a list of file names
files = glob.glob(folder + '\*.csv')
# create an empty df
indeed = pd.DataFrame(columns=['job','company','location','salary','description'])

# loop through files, load each csv, and append to dataframe
for f in files:
  f = pd.read_csv(f, names=['job','company','location','salary','description'],low_memory=False)
  indeed = indeed.append(f)
  indeed.drop_duplicates(inplace=True)
```

Check out my [portfolio](https://doyleax.github.io/Portfolio/salaries.html) for the full webscraper code.

