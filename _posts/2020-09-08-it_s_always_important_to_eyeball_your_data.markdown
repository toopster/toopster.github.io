---
layout: post
title:      "It’s important to try to “eyeball” your data"
date:       2020-09-08 11:15:08 -0400
permalink:  it_s_always_important_to_eyeball_your_data
---


<p style="line-height: 150%;">
My first “proper” job after graduating from University was working as a statistical compiler in the <a href="https://www.cipfastats.net">Statistical Information Service</a> at <a href="https://www.cipfa.org">CIPFA</a>, the Chartered Institute of Public Finance and Accountancy.
</p>

<p style="line-height: 150%;">
CIPFA has been collecting, analysing and publishing data on UK Local Government services for well over 100 years with the aim of providing a comprehensive framework for reviewing the efficiency of locally delivered services.
</p>

<p style="line-height: 150%;">
This was the 1990’s, when the statistical surveys were all completed on paper, <a href="https://en.wikipedia.org/wiki/Lotus_1-2-3">Lotus 123</a> was the analytics / spreadsheet package of choice and everything was archived on <a href="https://en.wikipedia.org/wiki/Floppy_disk">3.5 inch floppy disks</a>.
</p>

<p style="line-height: 150%;">
My role involved everything from photocopying the survey questionnaires and mailing them out to each of the almost 400 local authorities through entering the raw data, apportioning for missing data and “grossing” to providing some regional or national totals, to preparing the page layouts for the final printed publication.
</p>

<p style="line-height: 150%;">
As part of what was quite a rigorous quality assurance process, the head of department would always give each publication one last review, just to make sure that everything was correct before it was sent to the printers.  He would refer to it as “giving it one last eyeball”.
</p>

<hr size="1" />

<p style="line-height: 150%;">
Since then my career has focussed more on software application development and implementation but I’ve never been too far from the Data Science field as business intelligence was always a key part of the applications that I was working on.
</p>

<p style="line-height: 150%;">
Recently, however, I’ve wanted to refocus my career and, in an effort to update my rather rusty programming skills, have embarked on the Flatiron School Data Science course.
</p>

<p style="line-height: 150%;">
Working on my end-of-module project has brought a lot of memories back about my time at CIPFA and in the Statistical Information Service, in particular the importance that my boss would put on eyeballing the data and applying a common sense check to the information that you're being presented with.
</p>

<p style="line-height: 150%;">
Here are just a couple of examples that I found whilst working on the project analysing data from the Film Industry.
</p>

### Example 1 - When the data looks wrong but isn't

<p style="line-height: 150%;">First off, import the relevant Python Libraries.</p>

```
import pandas as pd
import numpy as np
```
<br/>
<p style="line-height: 150%;">
Next, read the <a href="https://github.com/toopster/dsc-mod-1-project-v2-1-online-ds-sp-000/blob/final-submission/zippedData/imdb.title.basics.csv.gz">basic film data</a> from the IMDb dataset and inspect using ```.info()``` and ```.head()``` methods.
</p>

```
df_imdb_titles = pd.read_csv('zippedData/imdb.title.basics.csv.gz', compression='gzip', header=0)

df_imdb_titles.info()
df_imdb_titles.head()
```

<br/>
<img src="https://i.imgur.com/2WQ3Ml8.png?1" title="source: imgur.com" />

<p style="line-height: 150%;">
Then use the ```.describe()``` method to generate some basic stats.
</p>

<br/>
<img src="https://i.imgur.com/vi5oacK.png" title="source: imgur.com" />

<p style="line-height: 150%;">
Hmmm... a maximum start year of 2115 and a maximum runtime of 51,420 minutes (or 35.7 days), that doesn't seem quite right even for a long-running TV series.  Some further investigation is clearly required here for both values, unless of course the runtime is referring to film equivalent <a href="https://www.bbc.co.uk/news/world-europe-54041568">John Cage's musical work</a> that recently had it's first chord change in 7 years since it started! 
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
So the remaining data for these records looks sensible even though the start / release year and runtime values do not.  Interrogating IMDb directly however, reveals that these are both legitimate records:

<ul style="line-height: 150%;">
<li><a href="https://www.imdb.com/title/tt5174640/">100 Years (2115)</a>, a short film written and starring John Malkovich, the content of which is currently a secret, due to be revealed only when the title is released in 2115.</li>
<li><a href="https://www.imdb.com/title/tt8273150/">Logistics (2012)</a>, a documentary about the production cycle of a pedometer in reverse chronological order. </li>
</ul>

Someone has even gone as far as watching the documentary and leaving a review on IMDb, giving it 5 out of 10 stars!
</p>

### Example 2 - When the data looks wrong and is

<p style="line-height: 150%;">
This second example comes from the <a href="https://github.com/toopster/dsc-mod-1-project-v2-1-online-ds-sp-000/blob/final-submission/zippedData/bom.movie_gross.csv.gz">Box Office Mojo dataset</a> that contains gross income figures, both US Domestic and Foreign, by film title and studio between 2010 and 2018.
</p>

<p style="line-height: 150%;">
As before, import the relevant dataset and use the ```.info()``` and ```.describe()``` methods as an initial inspection.
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
Nothing obviously wrong within these numbers until you start looking for some familiar films.
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
The figures for foreign gross income for these films is clear out by quite a large factor which will result in, at best, a skewed analysis at worst an incorrect one.  

The biggest problem that we have is that, technically, they are potentially valid numbers, they're just out by a factor of 1^6.

Here's how I ended up handling it.  If this had been a more exhaustive exercise I would have written a function that I could pass the record into in order to clean the data.
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

