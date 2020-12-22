---
layout: post
title:      "Iterative process or going round in circles?"
date:       2020-12-22 12:23:02 -0500
permalink:  when_your_iterative_process_feels_like_going_round_in_circles
---



I find it very difficult to get started with a new project, even this blog post is proving to be a challenge and I’ve spent at least 30 minutes staring at a blank screen.

I used to think that I was procastinating or just being plain lazy and always putting off today what I could do tomorrow.  But 

I have come to understand that, in a lot of situations, it’s just not that easy to immediately dive into a project or solve a problem.

# Figuring out your approach vector

In most situations I like to think (a lot) before I act, this can involve research, reading back over study material or the project brief or simply just some quiet time to contemplate the project or problem.  

During this time I can’t help but get frustrated as it feels like I’ve made little or no progress with the task at hand.  

Then after a couple of days I will finally be able to start, write that first piece of code, usually something really basic like importing a python library or 2 and inspecting the head of the dataset or, in the case of a blog post, a stream of consciousness that I can start to massage into something coherent.

The disadvantage of studying or working remotely is that you can feel isolated with your own ideas going round and round in your head.  When I worked in an office it was easy just to simply discuss the project or project with a colleague and that would usually help me decide my approach vector for making a start.

I tried talking to my eldest daughter about the problem that I was having with my project on multivariate linear regression but she skillfully dodged what, for her, was a boring topic of conversation and moved onto something that she wanted to talk about… supermassive black holes, she’s 9 years old btw.

So what has any of this got to do with Linear Regression Modelling?  I was struggling to make any headway on my Data Science Module 2 Final Project.

I had developed the business case and had started building my regression model but I had got the strong sense that I was going around in circles and definitely not in a way that one could describe as being part of the iterative process.
I knew what different things I need to do as part of the process:

* Check my assumptions for linear regression (linearity, normality of residuals and homoscedasticity).
* Potentially log transform the numeric variables but also one-hot encode any categorical variables that I want to include.
* Check for multicollinearity 
* Not forgetting cross validation and the test-train split.

What I was unsure of is what order to do them in.

After several false starts, and a lot of code written and deleted, I decided that I would start with a really basic, initial model simply comparing sale price with living space (as this seemed to meet the linearity assumption) then, having done that, check the normality of residuals and then the homoscedasticity.  From there I planned to introduce additional variables to the model that fit with the overall business scenario and which will require some transformation (either through scaling or one-hot encoding).

A sense check and very helpful steer from my tutor and I had bust out of my circle and had begun iterating (at least for the time being).  Here are the results of that first model.



```
# Import the relevant libraries for linear regression modelling

import statsmodels.api as sm
from statsmodels.formula.api import ols
import scipy.stats as stats
```

```
# Plot initial histograms to get a sense of which variables have a relationship and their distribution 

predictors_subset = ['price',
                     'bedrooms',
                     'bathrooms',
                     'floors',
                     'view',
                     'sqft_living',
                     'sqft_lot',
                     'condition',
                     'grade',
                     'sqft_above',
                     'sqft_basement',
                     'yr_built',
                     'yr_renovated',
                     'zipcode',
                     'sqft_living15',
                     'sqft_lot15']
house_sales_pred = house_sales.loc[:, predictors_subset]
house_sales_pred.hist(figsize=(16,16), bins='auto');
```

