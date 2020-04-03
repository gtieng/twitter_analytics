# Performance Reporting with Twitter Analytics data

Today, we'll be using Python to chart Twitter performance metrics based on the standard set of data provided by Twitter Analytics. By the end of this project, we will be able to vizualize or list the following pieces of information:

- Most active windows on Twitter.
- Most mentioned accounts and hashtags.
- Twitter engagement totals and averages per tweet
- Optimal time windows for the best engagement performance.
- Themes of the user's most popular content.

![](https://github.com/gtieng/twitter_analytics/blob/master/readme_images/yearly_impressions.png)

## Getting Started
Before we begin, please refer to the code below which details the libraries we'll use to explore our Twitter data:

```
#for dataframe management and slicing
import pandas as pd
import numpy as np
import datetime as dt

#main visualization library
import matplotlib.pyplot as plt
%matplotlib inline

#string cleaning and visualization
import re
from wordcloud import WordCloud, STOPWORDS
```

## Data Cleaning and Transformations
Native data from Twitter Analytics is fairly clean, but we'll still need to perform a few transformations of the data to get the answers we want from it. 

### Adding Time Columns
Using the `datetime` library is great for parsing months, days, and days of the week from the `time` columns. We'll be adding these classifications to their own columns for further aggregate plotting further in the project.

```
gerard["time"] = pd.to_datetime(gerard["time"])
gerard["hour"] = gerard["time"].dt.hour
gerard["month"] = gerard["time"].dt.month
gerard["dayofweek"] = gerard["time"].dt.dayofweek
```

### Separating Organic Tweets from Replies
When it comes to measurement, separating the organic tweets from replies is necessary to compare your apples against apples. Organic tweets are more often than not a one-to-many conversation--usually leading to higher engagement totals--while replies are typically a one-to-one type of dialogue.

As all replies begin with "@", using Python's `string.startswith()` method is a very easy way to filter those tweets from the rest. Use of the `~` symbol is just as easy to perform the opposite.

```
gerard_tweets = gerard[~gerard["Tweet text"].str.startswith("@")]
gerard_replies = gerard[gerard["Tweet text"].str.startswith("@")]
```
