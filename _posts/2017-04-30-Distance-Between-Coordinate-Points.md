---
layout: post
title:  "Calculating Distance Between Coordinate Points"
date:   2017-04-30
categories: how-to
---

For our fourth project, we worked in groups on the already closed Kaggle competition for Predicting West Nile Virus. The competition provided data on all of the traps located  throughout the neighborhoods of Chicago, along with the exact geographic coordinates, the date the trap was tested, the number of mosquitos in the sample, and the test results of whether or not the mosquitos have West Nile Virus.

The additional data they provided described the weather report for each day a trap was tested, and then a separate dataset contained the dates and geographic coordinates where the Chicago of Public Health Department sprayed something in the hopes of preventing WNV. We were hoping that, through analysis, we could help make an informed decision to make the most strategic sprays in the city.

The spray data was most interesting to me. It contained sprays for only two out of the four train years--just 2007 and 2009, while we also had data on traps for 2008 and 2010. After doing a bit of researching online as to how I could integrate this data, I realized that I would need to do a timeseries model to predict the dates and location of sprays in 2008 and 2010. We didn't have nearly enough time to add this to our project, so we had to scrap the spray data and not include it in our analysis.

Before we made the decision to not include the spray data, I was working out how to find the nearest location that was sprayed within a certain time period. My team decided that looking within the past two weeks would be a good start, and we would use that time frame to help narrow down the available locations that were sprayed. 

What I found to be extremely helpful is vincenty from geopy.distance. It easily calculates the distance between two geographic coordinate pairs. Say you have points A and B, with different latitude and longitude. All we need to do in order to find the distance between points is the following:

```python
vincenty(A,B)
```

We wanted to look specifically at miles, so by adding '.miles' we could get this value.

```python
vincenty(A,B).miles
```

You can do the same for different units of measurement, which makes this an extremely useful tool.


For my complete project on Predicting West Nile Virus, check out my [portfolio](http://doyleax.github.io/Portfolio/west-nile-virus.html).
