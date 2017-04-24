---
layout: post
title:  "Logistic Regression"
date:   2017-04-09
categories: jekyll update
---

In this post, I'll talk about some of the basics of logistic regression, and why we would use this type of modeling over linear regression. To help illustrate these concepts, I'll use the Titanic dataset which can be found [here](https://www.kaggle.com/c/titanic/data).

Before we begin, let's assume we want to use a linear regression model for our data, and keep in mind the key assumptions of a linear model:
Linearity- our dependent and independent variables have a linear relationship
Independence- the errors/residuals are independent
Normality- the errors/residuals follow a normal distribution
Equality of variances- the errors/residuals have generally equal variances/follow the same pattern

Let's take a simple example and check out survival based on age. The 'Survived' column is 0 for didn't survive and 1 for did survive, and the age column is the passengers' ages. Here's a scatter plot:
![Titanic Scatter plot]({{ site.baseurl }}/images/survived_dist.png)

Notice how the points form two separate lines. If we think about what the 'Survived' variable is, it makes sense that we have two separate lines of points, as 'Survived' can only be either 0 or 1. How would we even place a line of best fit?
![Titanic Linear Model]({{ site.baseurl }}/images/lin_line.png)

There isn't an area that makes sense for a line to best describe the data we have. Referring back to the key assumptions, we can already cross off the linearity assumption: for X and Y to have a linear relationship, a change in 1 unit of X implies a change in Y. Our data doesn't act in this condition, as one distinct age could be either of the Y values. While this data already doesn't qualify for a linear model, we can confirm this by looking at the residuals.
```python
import seaborn as sns
sns.residplot(x='Age',y='Survived', data=titanic)
```
![Titanic Residuals]({{ site.baseurl }}/images/titanic_residuals.png)

Clearly, we cannot say that the residuals follow a normal distribution. Two out of the four key assumptions do not apply to our data, and therefore, means we cannot use linear regression. Since we've proved this, moving forward we can just rule out linear regression for data where the dependent variable, 'Survived' in our case, has binary values. Enter logistic regression!

Logistic regression is a linear model that requires a binary, mutually exclusive dependent variable. This can be either categorical or numerical. Logistic regression is used to model the probability that our dependent variable is either 0 or 1. Additionally, instead of using the model to predict the value of the dependent variable based on our independent variable, logistic regression models predict the probability that our dependent variable will be 1. For all of these reasons, we can see how different the linear and logistic regression models are.

A bit more about the fit-- we call our fit the logit, which you can compare to the best line of fit for a linear regression model. Our logit does not actually hit values 0 or 1, but approaches them closely. Another interpretation of this is that probability values fall between 0 and 1.
