---
layout: post
title:      "“Eyeballing” your data"
date:       2020-09-08 11:15:08 -0400
permalink:  it_s_always_important_to_eyeball_your_data
---


<p style="line-height: 150%;">
My first “proper” job after graduating from University was working as a statistical compiler in the <a target="_blank" href="https://www.cipfastats.net">Statistical Information Service</a> at <a target="_blank" href="https://www.cipfa.org">CIPFA</a>, the Chartered Institute of Public Finance and Accountancy.
</p>

<p style="line-height: 150%;">
CIPFA has been collecting, analysing and publishing data on UK Local Government services for well over 100 years with the aim of providing a comprehensive framework for reviewing the efficiency of locally delivered services.
</p>

<p style="line-height: 150%;">
This was the 1990’s, when the statistical surveys were all completed on paper, <a target="_blank" href="https://en.wikipedia.org/wiki/Lotus_1-2-3">Lotus 123</a> was the analytics / spreadsheet package of choice and everything was archived on <a target="_blank" href="https://en.wikipedia.org/wiki/Floppy_disk">3.5 inch floppy disks</a>.
</p>

<p style="line-height: 150%;">
My role involved everything from photocopying the survey questionnaires and mailing them out to each of the almost 400 local authorities through entering the raw data, apportioning for missing data and “grossing” to provide some regional or national totals, to preparing the page layouts for the final printed publication.
</p>

<p style="line-height: 150%;">
As part of what was quite a rigorous quality assurance process, the head of department would always encourage us to "give it one last eyeball" before submitting the final dataset into the full review process before being sent to the printers for publication.
</p>

<hr size="1" />

<p style="line-height: 150%;">
Since then my career has focussed more on software application development and implementation but I’ve never been too far from the Data Science field as business intelligence was always a key part of the applications that I was working on.  Recently, however, I’ve wanted to refocus my career and have embarked on the Flatiron School Data Science course.
</p>

<p style="line-height: 150%;">
Working on my end-of-module project has brought back a lot of memories of my time at CIPFA and in the Statistical Information Service, in particular the importance that my boss would put on eyeballing the data and applying any contextual understanding we happen to have of the dataset, in order to quickly identify any problems.
</p>

<p style="line-height: 150%;">
Let's walk through 2 instances that I found whilst working on the project analysing data from the Film Industry.
</p>

<hr size="1" />

## 1. When the data looks wrong, but isn't

<p style="line-height: 150%;">First off, import the relevant Python Libraries.</p>

```
import pandas as pd
import numpy as np
```
<br/>
<p style="line-height: 150%;">
Next, read the <a target="_blank" href="https://github.com/toopster/dsc-mod-1-project-v2-1-online-ds-sp-000/blob/final-submission/zippedData/imdb.title.basics.csv.gz">basic film data</a> from the IMDb dataset and inspect using the <code>.info()</code> and <code>.head()</code> methods.
</p>

```
df_imdb_titles = pd.read_csv('zippedData/imdb.title.basics.csv.gz', compression='gzip', header=0)

df_imdb_titles.info()
df_imdb_titles.head()
```

<br/>
<img src="https://i.imgur.com/2WQ3Ml8.png?1" title="source: imgur.com" />

<p style="line-height: 150%;">
Then use the <code>.describe()</code> method to generate some basic stats and see the full scope of the numerical columns within the dataset. Taking this approach let's us quickly identify some outliers by comparing the maximum and minimum values with the mean and quartile values.
</p>

<br/>
<img src="https://i.imgur.com/vi5oacK.png" title="source: imgur.com" />


<p style="line-height: 150%;">With this dataset, about the film industry, we already have a basic feel for the data based on our own experience of watching films, and could identify obvious problems with the data. However, imagine if this was something less accessible, the bundle specifications for different species of wood imported into the UK from China for example. Since most data scientists are unlikely to also be experts in Far Eastern wood species, we'd have to build up that understanding using the data and not from our own experience. In that situation the <code>.describe()</code> method becomes even more important.</p>

<p style="line-height: 150%;">
Let's go back to the results of our <code>.describe()</code> method.
</p>

<br/>
<img src="https://i.imgur.com/vi5oacK.png" title="source: imgur.com" />

<p style="line-height: 150%;">
Hmmm... a maximum start year of 2115 and a maximum runtime of 51,420 minutes (or 35.7 days), that doesn't seem quite right even for a long-running TV series.
</p>

<p style="line-height: 150%;">
Some further investigation is clearly required here for both values unless, of course, the runtime is referring to film equivalent <a target="_blank" href="https://www.bbc.co.uk/news/world-europe-54041568">John Cage's musical work</a> that recently had it's first chord change in 7 years since it started!
</p>

<p style="line-height: 150%;">We could simply just dismiss and drop these values as outliers but it would be good to dig a bit deeper to give us greater confidence in the validity of the dataset.  Of more concern, we could overlook these values, jump quickly to aggregate our data with the aim of show a specific feature and skew our findings.
</p> 

<p style="line-height: 150%;">
Let's filter the dataframe just to show the record(s) that have those maximum values:
</p>

```
df_imdb_titles = df_imdb_titles[(df_imdb_titles['start_year'] == 2115) | (df_imdb_titles['runtime_minutes'] == 51420)]
df_imdb_titles.head()
```

<br/>
<img src="https://i.imgur.com/xO1rhmT.png" title="source: imgur.com" />

<p style="line-height: 150%;">
So the remaining data for these records look sensible even though the start / release year and runtime values do not.  Interrogating IMDb directly however, reveals that these are both legitimate records:

<ul style="line-height: 150%;">
<li><a target="_blank" href="https://www.imdb.com/title/tt5174640/" style="text-decoration: underline;">100 Years (2115)</a>, a short film written and starring John Malkovich, the content of which is currently a secret, due to be revealed only when the title is released in 2115.</li>
<li><a target="_blank" href="https://www.imdb.com/title/tt8273150/" style="text-decoration: underline;">Logistics (2012)</a>, a documentary about the production cycle of a pedometer in reverse chronological order. </li>
</ul>

Someone has even gone as far as watching the documentary and leaving a review on IMDb, giving it 5 out of 10 stars!
</p>

<hr size="1" />

## 2. When the data looks wrong, and is

<p style="line-height: 150%;">
This second example comes from the <a target="_blank" href="https://github.com/toopster/dsc-mod-1-project-v2-1-online-ds-sp-000/blob/final-submission/zippedData/bom.movie_gross.csv.gz">Box Office Mojo dataset</a> that contains gross income figures, both US Domestic and Foreign, by film title and studio between 2010 and 2018.
</p>

<p style="line-height: 150%;">
As before, import the relevant dataset and use the <code>.info()</code> and <code>.describe()</code> methods as an initial inspection.
</p>

```
df_bom = pd.read_csv('zippedData/bom.movie_gross.csv.gz', compression='gzip', header=0, thousands=",")

df_bom.info()
df_bom.describe()
```

<p style="line-height: 150%;">
<img src="https://i.imgur.com/zFigqaC.png" title="source: imgur.com" />
<br/>
<img src="https://i.imgur.com/FFC2I9x.png" title="source: imgur.com" />
</p>

<p style="line-height: 150%;">
Nothing obviously wrong within these numbers until, eyeballing the full dataset (or a statistical significant sample of it) you notice some familiar films doing quite badly outside the US.
</p>

```
incorrect_df = df_bom[df_bom.title.isin(['The Fate of the Furious', 
                                         'Jurassic World',
                                         'Star Wars: The Force Awakens',
                                         'Furious 7',
                                         'Avengers: Infinity War'
                                        ])] 
incorrect_df
```
<br/>
<img src="https://i.imgur.com/KFbhOAB.png" title="source: imgur.com" />

<p style="line-height: 150%;">
The figures for foreign gross income for these films are clearly wrong by quite a large factor which will result in, at best, a skewed analysis at worst an incorrect one.
</p>

<p style="line-height: 150%;">
The problem is that, technically, they are potentially valid numbers but, given our knowledge of these particular films and by referring back to the <a target="_blank" href="https://www.boxofficemojo.com/">Box Office Mojo website</a>, you can see they are incorrect.
<ul style="line-height: 150%;">
<li><a target="_blank" href="https://www.boxofficemojo.com/title/tt4630562/" style="text-decoration: underline;">The Fate of the Furious</a></li>
<li><a target="_blank" href="https://www.boxofficemojo.com/title/tt0369610/" style="text-decoration: underline;">Jurassic World</a></li>
<li><a target="_blank" href="https://www.boxofficemojo.com/title/tt2488496/" style="text-decoration: underline;">Star Wars: The Force Awakens</a></li>
<li><a target="_blank" href="https://www.boxofficemojo.com/title/tt2820852/" style="text-decoration: underline;">Furious 7</a></li>
<li><a target="_blank" href="https://www.boxofficemojo.com/title/tt4154756/" style="text-decoration: underline;">Avengers: Infinity War</a></li>
</ul>
</p>

<p style="line-height: 150%;">
Let's find a general rule for finding potential inaccuracies within the data that doesn't involve passing specific film titles or having a prior knowledge of box office hit films.  In general, foreign income is usually more than US domestic income but this isn't a clear enough indicator. We'll try the ratio of domestic income to foreign income and filter to show those films for which the foreign income was 0.1% or less than domestic income.
</p>

```
df_bom.insert(4, 'foreign_percent', (df_bom['foreign_gross']/(df_bom['foreign_gross'] + df_bom['domestic_gross']))*100)

low_foreign = df_bom[(df_bom['foreign_percent'] <= 0.1)]
low_foreign.head(10)
```

<img src="https://i.imgur.com/ySmFeCv.png" title="source: imgur.com" />

<p style="line-height: 150%;">
As you can see, this returned some more results, but not all of these are out by the same magnitude as our blockbusters.  For example:
<ul style="line-height: 150%;">
<li><a target="_blank" href="https://www.boxofficemojo.com/title/tt1405500/" style="text-decoration: underline;">For Colored Girls</a> actually made $252,286 foreign income, the figure in the dataset appears to be the figure from the UK alone.</li>
<li><a target="_blank" href="https://www.boxofficemojo.com/title/tt1629757/" style="text-decoration: underline;">Chasing Mavericks</a> actually made $1,939,360 foreign income.</li>
</ul>
</p>

<p style="line-height: 150%;">
Here's how I ended up handling it.  If this had been a more exhaustive exercise I would have tried to obtain a corrected dataset or written a function that I could pass the record into in order to correct the data.
</p>


```
df_bom.loc[df_bom.title.isin(['The Fate of the Furious',
                              'Jurassic World',
                              'Star Wars: The Force Awakens',
                              'Furious 7',
                              'Avengers: Infinity War'
                             ]) & (df_bom.foreign_gross < 1e6), 'foreign_gross'] = df_bom['foreign_gross'] * 1e6


corrected_df = df_bom[df_bom.title.isin(['The Fate of the Furious', 
                                         'Jurassic World',
                                         'Star Wars: The Force Awakens',
                                         'Furious 7',
                                         'Avengers: Infinity War'
                                        ])] 
corrected_df
```

<br/>
<img src="https://i.imgur.com/3TFscm3.png" title="source: imgur.com" />

<hr size="1" />

<p style="line-height: 150%;">
Throughout this project I've been repeatedly delighted by the speed of these methods. Something that takes 5 seconds to do in Pandas would've taken at least 10 to 15 minutes in Lotus 123.
</p>

<p style="line-height: 150%;">
This ease and speed is great but it also made me conscious of how easy it would be to start analysing or summarising without gaining a familiarity for the underlying raw data as you would be when you're forced to scroll through and eyeball records one-by-one.  Over the course of this project I found myself repeatedly using the following code within my Jupyter notebook to make Pandas to nicely display all rows within the dataframe, just so that I could take a look at <em>all</em> the output and not just the first or last 5 rows.
</p>

```
pandas.set_option('display.max_rows', df.shape[0]+1)
```

<p style="line-height: 150%;">
Obviously, taking the eyeball approach of my old boss literally would not be practical with the size of datasets data scientists are expected to interrogate today. But the principle is still really important and the data scientist still has a range of ways to "eyeball" the data without having to physically look at every record.  I'm looking forward to learning more of them over the duration of my course.
</p>





