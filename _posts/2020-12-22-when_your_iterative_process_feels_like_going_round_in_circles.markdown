---
layout: post
title:      "Iterative process or going round in circles?"
date:       2020-12-22 12:23:02 -0500
permalink:  when_your_iterative_process_feels_like_going_round_in_circles
---



I find it very difficult to get started with a new project. Even this blog post proved to be a challenge with at least 30 minutes staring at a blank screen.

I used to think that I was procrastinating or just being plain lazy. But, I have come to understand that, in a lot of situations, it’s just not that easy to immediately dive into a project or solve a problem.

<hr size="1" />

## Figuring out your approach vector

In most situations, I like to think (a lot) before I act. This can involve research, reading back over study material or the project brief, or simply some quiet time to contemplate the problem.

During this time, I can’t help but get frustrated. It feels like I’ve been going round in circles and made little or no progress.

Then, after a couple of days, I will finally be able to start. I’ll write that first piece of code, usually something really basic like importing a python library or two and inspecting the head of the dataset. Or, in the case of a blog post, I’ll write a stream of consciousness that I can start to massage into something coherent.

The disadvantage of studying or working remotely is that you can feel isolated with your own ideas going round and round in your head. When I worked in an office it was easy just to simply discuss the problem  with a colleague to help me decide my approach vector for making a start.

I tried that approach with my eldest daughter. Funnily enough, she skillfully dodged my chosen topic - the problem that I was having with my project on multivariate linear regression - and moved onto something that she wanted to talk about… supermassive black holes. She’s 9 years old.

So what has any of this got to do with Linear Regression Modelling? I was struggling to make any headway on my Data Science Module 2 Final Project.

I had developed the business case and had started building my regression model, a process that seemed to need a lot of iteration. But I had the strong sense that my iteration was actually just more going around in circles.

I knew what different things I need to do as part of the process, so I listed them out:

* Check my assumptions for linear regression (linearity, normality of residuals and homoscedasticity).
* If necessary, log transform the numeric variables but also one-hot encode any categorical variables that I want to include.
* Check for multicollinearity
* Not forgetting cross-validation and the test-train split.

What I was unsure of is what order to do them in.

After several false starts and a lot of code written and deleted, I decided that I would begin with a basic model simply comparing sale price with living space (as this seemed to meet the linearity assumption). Then, having done that, check the normality of residuals and then the homoscedasticity. From there I planned to introduce additional variables to the model that fit with the overall business scenario and which will require some transformation (either through scaling or one-hot encoding).

A sense check and helpful steer from my tutor and I found I had stopped circling and had begun iterating (at least for the time being).


<hr size="1" />

## A first iteration

Here are the results of that first model. To make the post more readable, I’ve missed out the code where I read in the data from the csv file, preprocess the data and produce some visualisations, but you can see that in full [here](https://github.com/toopster/dsc-mod-2-project-v2-1-online-ds-sp-000). 

Let’s start off by importing the relevant libraries for linear regression modelling:

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

<img src="https://i.imgur.com/ANbxZ7i.png" title="source: imgur.com" />


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

<img src="https://i.imgur.com/Y9HMVto.png" title="source: imgur.com" />

From these plots it’s quite clear to see which variables have a linear relationship and which are categorical variables. We’ll then a check for multicollinearity between variables:

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

<img src="https://i.imgur.com/fIJK59A.png" title="source: imgur.com" />

Since we’re only planning on using one predictor for the moment this is possibly a bit premature, but it’s good to have for when we make the model multivariate.

Now we can create an initial, basic linear regression model using sqft_living which appears to meet the linearity assumption:


```
f_one = 'price~sqft_living'
model_one = ols(formula=f_one, data=house_sales).fit()
model_one.summary()
```

<img src="https://i.imgur.com/5iPnOl4.png" title="source: imgur.com" />

Our model looks okay although, ideally, we’d like the R-Squared value to be higher than **0.461** and our predictor has a value less than 0.05.

But that Jarque-Bera score is very high and would indicate that our model residuals do not follow a normal distribution and meet the normality assumption.

Just to be sure, we’ll check the normality assumption with a QQ plot:


```
residuals_one = model_one.resid
fig = sm.graphics.qqplot(residuals_one, dist=stats.norm, line='45', fit=True)
fig.show();
```

<img src="https://i.imgur.com/Ieqm49h.png" title="source: imgur.com" />


This confirms our suspicions that the model does not meet the normality assumption so it is time to iterate again.

<hr size="1" />

## A second iteration, but have I missed something?

The King County Housing dataset is quite “well used” among students of data science and I was interested to find out how my peers had approached the issue of the high Jarque-Bera test score in the hope that it would better inform my next iteration.

What surprised me was that very few mentioned inspecting it as part of their modelling process and I began to second guess my work and wondered if I was perhaps dwelling on the high test score too much.

Having got that feeling of going round in circles again, I found some useful posts on the [Minitab blog](https://blog.minitab.com/blog) which encouraged me to produce my second iteration of the model.

This time my plan was to use log scaling and normalise the continuous variables to see if that improved the model. At the same time, I would pre-flight the categorical variables so that they are ready to be added to the model when required.


```
# Log transform continuous variables and one-hot encode categoricals

continuous = ['price','sqft_living','sqft_lot']
categoricals = ['bedrooms','view','zipcode']

house_sales_cont = house_sales[continuous]

# Log features

log_names = [f'{column}_log' for column in house_sales_cont.columns]

house_sales_log = np.log(house_sales_cont)
house_sales_log.columns = log_names

# Normalize

def normalize(feature):
    return (feature - feature.mean()) / feature.std()

house_sales_log_norm = house_sales_log.apply(normalize)

# One hot encode categoricals

house_sales_ohe = pd.get_dummies(house_sales[categoricals], columns=categoricals, drop_first=True)

house_sales_wf = house_sales['waterfront']
hs_preprocessed = pd.concat([house_sales_log_norm, house_sales_wf, house_sales_ohe], axis=1)

# Create second version of the model

f_two = 'price_log~sqft_living_log'
model_two = ols(formula=f_two, data=hs_preprocessed).fit()
model_two.summary()
```

<img src="https://i.imgur.com/S5rdpvu.png" title="source: imgur.com" />

```
residuals_two = model_two.resid
fig = sm.graphics.qqplot(residuals_two, dist=stats.norm, line='45', fit=True)
fig.show();
```

<img src="https://i.imgur.com/kWVWfOq.png" title="source: imgur.com" />


Scaling and normalising the variables improved the model with regard to the assumptions for linear regression with residuals being more normally distributed and the appearance of homoscedasticity but the value of R-Squared has decreased to **0.428**.

<hr size="1" />

## A third iteration, limiting the dataset

I began to think that some specific feature might be keeping the R-squared value low so I decided to reduce the size of the dataset to focus on one zipcode alone.

```
zip_98118 = hs_preprocessed[(hs_preprocessed['zipcode_98118'] == 1)]

# Create the formula

f_98118 = 'price_log~sqft_living_log'
model_98118 = ols(formula=f_98118, data=zip_98118).fit()
model_98118.summary()
```

<img src="https://i.imgur.com/NWqePuw.png?1" title="source: imgur.com" />

<img src="https://i.imgur.com/0cRIxpF.png?1" title="source: imgur.com" />

Focussing in on a single zipcode certainly improved the Jarque-Bera test score to be within acceptable limits but the value of R-Squared has fallen even further to  **0.412**.

It was time to add some other predictors into the model to improve it.

<hr size="1" />

## A fourth iteration, going multivariate

At this point I used stepwise feature selection to choose which predictors should be included in the model based on a p-value of less than 0.05.

```
def stepwise_selection(X, y, 
                       initial_list=[], 
                       threshold_in=0.01, 
                       threshold_out = 0.05, 
                       verbose=True):
    """ 
    Perform a forward-backward feature selection 
    based on p-value from statsmodels.api.OLS
    Arguments:
        X - pandas.DataFrame with candidate features
        y - list-like with the target
        initial_list - list of features to start with (column names of X)
        threshold_in - include a feature if its p-value < threshold_in
        threshold_out - exclude a feature if its p-value > threshold_out
        verbose - whether to print the sequence of inclusions and exclusions
    Returns: list of selected features 
    Always set threshold_in < threshold_out to avoid infinite looping.
    See https://en.wikipedia.org/wiki/Stepwise_regression for the details
    """
    included = list(initial_list)
    while True:
        changed=False
        # forward step
        excluded = list(set(X.columns)-set(included))
        new_pval = pd.Series(index=excluded)
        for new_column in excluded:
            model = sm.OLS(y, sm.add_constant(pd.DataFrame(X[included+[new_column]]))).fit()
            new_pval[new_column] = model.pvalues[new_column]
        best_pval = new_pval.min()
        if best_pval < threshold_in:
            best_feature = new_pval.idxmin()
            included.append(best_feature)
            changed=True
            if verbose:
                print('Add  {:30} with p-value {:.6}'.format(best_feature, best_pval))

        # backward step
        model = sm.OLS(y, sm.add_constant(pd.DataFrame(X[included]))).fit()
        # use all coefs except intercept
        pvalues = model.pvalues.iloc[1:]
        worst_pval = pvalues.max() # null if pvalues is empty
        if worst_pval > threshold_out:
            changed=True
            worst_feature = pvalues.argmax()
            included.remove(worst_feature)
            if verbose:
                print('Drop {:30} with p-value {:.6}'.format(worst_feature, worst_pval))
        if not changed:
            break
    return included
```
		
```
		X = hs_preprocessed.drop('price_log', axis=1)
y = hs_preprocessed['price_log']

result = stepwise_selection(X, y, verbose = True)
print('Resulting features:')
print(result)
```

```
developers_predictors = hs_preprocessed.drop([
    'zipcode_98058',
    'zipcode_98038',
    'zipcode_98055',
    'zipcode_98178',
    'zipcode_98031',
    'zipcode_98188',
    'zipcode_98030', 
    'zipcode_98198',
    'zipcode_98022',
    'zipcode_98003',                
    'zipcode_98168',          
    'zipcode_98092'    
], axis=1)

outcome = 'price_log'
predictors = developers_predictors.drop(['price_log'], axis=1)
pred_sum = '+'.join(predictors.columns)
formula = outcome + '~' + pred_sum
model_five = ols(formula=formula, data=developers_predictors).fit()
model_five.summary()
```

This is how I saw the results as they appeared on the screen, fantastic, the R-squared value is now at **0.840**.

<img src="https://i.imgur.com/EbbYRUY.png?1" title="source: imgur.com" />


That was until I scrolled down to discover what had happened to the Jarque-Bera test score.

<img src="https://i.imgur.com/Nzpqp8q.png" title="source: imgur.com" />

I was starting to feel like I was going in circles again, but again, a [Minitab blog post](https://blog.minitab.com/blog/adventures-in-statistics-2/how-high-should-r-squared-be-in-regression-analysis) gave me the confidence to persevere.

<hr size="1" />

## A fifth and "final" iteration

My final model took the previous model and, with some more manual trial and error adding and removing different zipcodes, I was able to get the different values to a point that I was happy with.  The R-squared value was **0.559** but , most importantly, the model achieved the necessary Jarque-Bera test score to fulfill the normality assumption for linear regression.

<img src="https://i.imgur.com/axztqi8.png?1" title="source: imgur.com" />

<hr size="1" />


 


