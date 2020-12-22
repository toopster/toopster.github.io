---
layout: post
title:      "Iterative process or going round in circles?"
date:       2020-12-22 12:23:02 -0500
permalink:  when_your_iterative_process_feels_like_going_round_in_circles
---



I find it very difficult to get started with a new project, even this blog post proved to be a challenge with at least 30 minutes staring at a blank screen.

I used to think that I was procastinating or just being plain lazy and always putting off today what I could do tomorrow, but  I have come to understand that, in a lot of situations, it’s just not that easy to immediately dive into a project or solve a problem.

<hr size="1" />

## Figuring out your approach vector

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

A sense check and very helpful steer from my tutor and I had bust out of my circle and had begun iterating (at least for the time being).  Here are the results of that first model, to make the post more readable, I've missed out the code where I read in the data from the csv file, preprocess the data and produce some visualisations, but you can see that in full [here](https://github.com/toopster/dsc-mod-2-project-v2-1-online-ds-sp-000). 

Let's start off by importing the relevant libraries for linear regression modelling

```
import statsmodels.api as sm
from statsmodels.formula.api import ols
import scipy.stats as stats
```

Then plot some initial histograms to get a sense of which variables have a relationship and their distribution 

```
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

<a href="https://imgur.com/ANbxZ7i"><img src="https://i.imgur.com/ANbxZ7i.png" title="source: imgur.com" /></a>


Then we'll visually check the linearity assumption for all chosen features and highlight categorical variables

```
fig,axes = plt.subplots(nrows=4, ncols=4, figsize=(16,16), sharey=True)

for ax,column in zip(axes.flatten(), house_sales_pred.columns):
    ax.scatter(house_sales_pred[column], house_sales_pred['price'] / 100000, label=column, alpha=0.1)
    ax.set_title(f'price vs {column}')
    ax.set_xlabel(column)
    ax.set_ylabel('price in $100,000')
    
fig.tight_layout()
```

<a href="https://imgur.com/Y9HMVto"><img src="https://i.imgur.com/Y9HMVto.png" title="source: imgur.com" /></a>

Then a check for multicollinearity between variables:

```
house_sales_coll = house_sales.drop(['price'], axis=1)

hs_coll = house_sales_coll.corr().abs().stack().reset_index().sort_values(0, ascending=False)
hs_coll['pairs'] = list(zip(hs_coll.level_0, hs_coll.level_1))
hs_coll.set_index(['pairs'], inplace=True)
hs_coll.drop(columns=['level_1','level_0'], inplace=True)
hs_coll.columns = ['cc']
hs_coll.drop_duplicates(inplace=True)
hs_coll[(hs_coll.cc > 0.75) & (hs_coll.cc < 1)]
```

<a href="https://imgur.com/fIJK59A"><img src="https://i.imgur.com/fIJK59A.png" title="source: imgur.com" /></a>

Finally we create an initial, basic linear regression model using `sqft_living` which appears to meet the linearity assumption

```
f_one = 'price~sqft_living'

model_one = ols(formula=f_one, data=house_sales).fit()

model_one.summary()
```

<a href="https://imgur.com/5iPnOl4"><img src="https://i.imgur.com/5iPnOl4.png" title="source: imgur.com" /></a>

